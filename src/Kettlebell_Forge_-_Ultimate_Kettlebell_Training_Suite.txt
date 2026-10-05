import React, { useState, useEffect, useRef } from 'react';
import {
  Dumbbell, Play, Pause, RotateCcw, Plus, Trash2, Calendar, Trophy,
  Activity, Scale, Info, CheckCircle, ChevronRight, Volume2, VolumeX,
  Flame, Shield, Zap, Search, Clock, ArrowRight, Award, BarChart2, Check,
  Sliders, User, Filter, RefreshCw
} from 'lucide-react';
import {
  AreaChart, Area, XAxis, YAxis, Tooltip, ResponsiveContainer, BarChart, Bar
} from 'recharts';

const EXERCISE_LIBRARY = [
  {
    id: 'swing',
    name: 'Russian Kettlebell Swing',
    category: 'Ballistic / Hinge',
    difficulty: 'Beginner',
    primaryMuscles: ['Glutes', 'Hamstrings', 'Lower Back'],
    secondaryMuscles: ['Core', 'Lats', 'Shoulders'],
    description: 'The foundational ballistic movement for hip drive, explosive power, and posterior chain conditioning.',
    cues: [
      'Hinge at hips, not a squat; keep back flat and neutral',
      'Hike kettlebell high between legs close to groin',
      'Drive hips forward explosively to propel the weight',
      'Let bell float to chest level; do not pull with arms'
    ],
    mistakes: [
      'Squatting instead of hinging at the hips',
      'Lifting weight with shoulder muscles',
      'Hyperextending spine at the top lock-out',
      'Letting the bell pull lower back down too early'
    ],
    startingWeightKg: { male: 16, female: 8 },
    svgType: 'swing'
  },
  {
    id: 'tgu',
    name: 'Turkish Get-Up',
    category: 'Grind / Stability',
    difficulty: 'Intermediate',
    primaryMuscles: ['Shoulders', 'Core', 'Hips', 'Glutes'],
    secondaryMuscles: ['Triceps', 'Upper Back', 'Forearms'],
    description: 'A total-body mobility and stability masterpiece moving from floor to standing under loaded vertical alignment.',
    cues: [
      'Maintain vertical arm stack and gaze on kettlebell',
      'Roll onto elbow, then push onto straight hand',
      'Bridge hips high, sweep leg through to half-kneel',
      'Stand smoothly through the front heel'
    ],
    mistakes: [
      'Bending the loaded elbow or wrist',
      'Losing eye contact with the bell before standing',
      'Rushing transitions without establishing balance',
      'Collapsing the shoulder socket under load'
    ],
    startingWeightKg: { male: 12, female: 6 },
    svgType: 'tgu'
  },
  {
    id: 'clean-press',
    name: 'Kettlebell Clean & Press',
    category: 'Power & Overhead',
    difficulty: 'Intermediate',
    primaryMuscles: ['Shoulders', 'Upper Back', 'Triceps'],
    secondaryMuscles: ['Hamstrings', 'Glutes', 'Core'],
    description: 'Combines hip power to rack the bell cleanly with overhead shoulder strength and core stability.',
    cues: [
      'Keep bell trajectory close to body during clean',
      'Catch bell smoothly in rack position without banging wrist',
      'Brace core, root feet, press straight up overhead',
      'Lock out with arm in line with ear'
    ],
    mistakes: [
      'Casting the bell outward away from chest',
      'Allowing forearm to bang aggressively into rack',
      'Leaning back excessively during pressing phase',
      'Failing to brace glutes and abdominal wall'
    ],
    startingWeightKg: { male: 16, female: 8 },
    svgType: 'clean'
  },
  {
    id: 'snatch',
    name: 'Kettlebell Snatch',
    category: 'Ballistic Power',
    difficulty: 'Advanced',
    primaryMuscles: ['Posterior Chain', 'Shoulders', 'Lats'],
    secondaryMuscles: ['Grip', 'Core', 'Cardio System'],
    description: 'The Tsar of kettlebell exercises; launches bell from backswing straight overhead in one continuous trajectory.',
    cues: [
      'Drive forcefully from hips as in a high swing',
      'Tuck elbow in and tame the arc as bell ascends',
      'Punch hand upward smoothly through handle at top',
      'Lock out bell overhead with stability'
    ],
    mistakes: [
      'Flaying arm out wide causing forearm impact at top',
      'Pressing the bell up instead of snatching in momentum',
      'Gripping handle too tightly during transition',
      'Arching lumbar spine under heavy load'
    ],
    startingWeightKg: { male: 12, female: 8 },
    svgType: 'snatch'
  },
  {
    id: 'goblet-squat',
    name: 'Goblet Squat',
    category: 'Squat / Lower Body',
    difficulty: 'Beginner',
    primaryMuscles: ['Quadriceps', 'Glutes'],
    secondaryMuscles: ['Core', 'Upper Back', 'Calves'],
    description: 'Essential lower body leg builder with front-loaded kettlebell forcing active posture and depth.',
    cues: [
      'Hold bell close to chest by horns or base',
      'Sit hips back between ankles while pushing knees out',
      'Keep chest up and elbows pointing down inside knees',
      'Drive straight up through mid-foot and heel'
    ],
    mistakes: [
      'Knees collapsing inward (valgus collapse)',
      'Rounding upper back or letting shoulders slump forward',
      'Heels lifting off ground during descent',
      'Shallow depth short of parallel'
    ],
    startingWeightKg: { male: 16, female: 12 },
    svgType: 'squat'
  },
  {
    id: 'windmill',
    name: 'Kettlebell Windmill',
    category: 'Mobility / Core',
    difficulty: 'Intermediate',
    primaryMuscles: ['Obliques', 'Hamstrings', 'Shoulder Complex'],
    secondaryMuscles: ['Glutes', 'Lats', 'Spinal Erectors'],
    description: 'Enhances thoracic mobility, hip hinge flexibility, and shoulder stability under dynamic angle tension.',
    cues: [
      'Set feet 45° away from overhead locked bell arm',
      'Hinge rear hip out laterally beneath weight',
      'Lower front arm inside front leg toward ground',
      'Keep overhead arm vertical and eyes fixed on weight'
    ],
    mistakes: [
      'Bending both knees excessively',
      'Allowing top shoulder to collapse forward',
      'Flexing spine instead of hinging at hip socket',
      'Taking eyes off the overhead kettlebell'
    ],
    startingWeightKg: { male: 12, female: 6 },
    svgType: 'windmill'
  },
  {
    id: 'halo',
    name: 'Kettlebell Halo',
    category: 'Shoulder Mobility',
    difficulty: 'Beginner',
    primaryMuscles: ['Rotator Cuff', 'Upper Back', 'Core'],
    secondaryMuscles: ['Biceps', 'Triceps'],
    description: 'Preps shoulders and neck mobility by orbiting inverted kettlebell tightly around crown of head.',
    cues: [
      'Hold kettlebell upside down by horns near chest',
      'Orbit bell close around ear, back of neck, opposite ear',
      'Keep ribs pulled down and core tight (no torso wobbling)',
      'Alternate directions with smooth rhythm'
    ],
    mistakes: [
      'Swinging whole upper body instead of isolating shoulders',
      'Moving bell too far away from neck/head',
      'Flaring ribs and arching lower back',
      'Rush timing causing uneven movement speed'
    ],
    startingWeightKg: { male: 12, female: 8 },
    svgType: 'halo'
  },
  {
    id: 'rdl',
    name: 'Single-Leg Kettlebell RDL',
    category: 'Posterior & Balance',
    difficulty: 'Intermediate',
    primaryMuscles: ['Hamstrings', 'Glutes'],
    secondaryMuscles: ['Core', 'Ankle Stabilizers', 'Grip'],
    description: 'Unilateral hinge building single-leg hamstring power, balance, and hip socket alignment.',
    cues: [
      'Slight bend in standing working leg',
      'Extend rear non-working leg long behind like pendulum',
      'Hinge forward from hips keeping hips square to floor',
      'Squeeze working glute to return upright'
    ],
    mistakes: [
      'Opening or rotating hips outward to side',
      'Rounding lower spine as weight descends',
      'Locking working knee out stiff',
      'Losing neck alignment by looking straight up'
    ],
    startingWeightKg: { male: 16, female: 8 },
    svgType: 'rdl'
  }
];

const PRESET_PROGRAMS = [
  {
    id: 'prog-simple-sinister',
    title: 'Simple & Sinister Protocol',
    level: 'Beginner',
    duration: '25 min',
    tags: ['Pavel Tsatsouline', 'Strength & Minimalist'],
    description: 'The golden standard minimalist kettlebell strength & endurance routine built around Swings and Get-ups.',
    type: 'EMOM',
    exercises: [
      { exerciseId: 'swing', reps: 10, sets: 10, restSec: 60, title: 'One-Arm Swings (EMOM)' },
      { exerciseId: 'tgu', reps: 1, sets: 10, restSec: 60, title: 'Turkish Get-Ups (1 per min)' }
    ]
  },
  {
    id: 'prog-hiit-burn',
    title: 'Kettlebell Inferno HIIT',
    level: 'Intermediate',
    duration: '20 min',
    tags: ['Fat Loss', 'Metabolic Conditioning'],
    description: 'High-intensity interval blast designed for high heart rates and dynamic total-body fat burning.',
    type: 'Tabata',
    exercises: [
      { exerciseId: 'swing', workSec: 20, restSec: 10, sets: 8, title: 'Explosive Swings' },
      { exerciseId: 'goblet-squat', workSec: 20, restSec: 10, sets: 8, title: 'Goblet Squats' },
      { exerciseId: 'clean-press', workSec: 20, restSec: 10, sets: 8, title: 'Clean & Press' }
    ]
  },
  {
    id: 'prog-power-builder',
    title: 'Armor Building Complex',
    level: 'Advanced',
    duration: '35 min',
    tags: ['Hypertrophy', 'Double Kettlebell'],
    description: 'Classic Dan John formula: Clean, Press, Squat chain for rugged muscular endurance and density.',
    type: 'EMOM',
    exercises: [
      { exerciseId: 'clean-press', reps: 2, sets: 12, restSec: 60, title: '2 Cleans + 1 Press' },
      { exerciseId: 'goblet-squat', reps: 3, sets: 12, restSec: 60, title: '3 Front Squats' },
      { exerciseId: 'snatch', reps: 5, sets: 6, restSec: 90, title: 'Power Snatches' }
    ]
  },
  {
    id: 'prog-flow-mobility',
    title: 'Kettlebell Flow & Resilience',
    level: 'Beginner',
    duration: '30 min',
    tags: ['Joint Mobility', 'Active Recovery'],
    description: 'Gentle flow linking dynamic hip hinges, rotational core work, and shoulder health patterns.',
    type: 'AMRAP',
    exercises: [
      { exerciseId: 'halo', reps: 10, sets: 4, restSec: 45, title: 'Kettlebell Halos' },
      { exerciseId: 'windmill', reps: 6, sets: 4, restSec: 45, title: 'Windmills (3/side)' },
      { exerciseId: 'rdl', reps: 8, sets: 4, restSec: 45, title: 'Single-Leg RDL' }
    ]
  }
];

const INITIAL_LOGS = [
  { id: '1', date: '2026-10-01', workoutTitle: 'Simple & Sinister Protocol', durationMin: 22, totalVolumeKg: 3200, setsCompleted: 20 },
  { id: '2', date: '2026-10-03', workoutTitle: 'Kettlebell Inferno HIIT', durationMin: 20, totalVolumeKg: 2800, setsCompleted: 24 },
  { id: '3', date: '2026-10-04', workoutTitle: 'Armor Building Complex', durationMin: 32, totalVolumeKg: 4500, setsCompleted: 30 },
  { id: '4', date: '2026-10-05', workoutTitle: 'Simple & Sinister Protocol', durationMin: 21, totalVolumeKg: 3400, setsCompleted: 20 }
];

function MovementVisualizer({ type }) {
  return (
    <div className="w-full h-44 bg-slate-900 rounded-xl flex items-center justify-center border border-slate-800 p-2 relative overflow-hidden group">
      <div className="absolute inset-0 bg-gradient-to-t from-slate-950/80 to-transparent z-10 pointer-events-none" />
      <svg className="w-32 h-32 text-amber-500 drop-shadow-[0_0_12px_rgba(245,158,11,0.3)] transition-transform duration-300 group-hover:scale-110" viewBox="0 0 100 100" fill="none" stroke="currentColor" strokeWidth="2.5">
        {type === 'swing' && (
          <g>
            <circle cx="35" cy="25" r="7" stroke="currentColor" />
            <path d="M 35,32 L 30,55 L 15,85 M 30,55 L 48,85" />
            <path d="M 35,40 L 65,42" />
            <path d="M 65,42 Q 72,42 72,49 C 72,56 65,58 65,58 Z" fill="rgba(245, 158, 11, 0.2)" stroke="currentColor" />
            <path d="M 50,45 Q 60,35 65,42" strokeDasharray="2,2" stroke="rgba(245,158,11,0.6)" />
          </g>
        )}
        {type === 'tgu' && (
          <g>
            <circle cx="50" cy="20" r="7" />
            <path d="M 50,27 L 50,60 M 50,60 L 30,90 M 50,60 L 70,90" />
            <path d="M 50,38 L 75,25" />
            <path d="M 50,38 L 25,45" />
            <circle cx="78" cy="20" r="6" fill="rgba(245, 158, 11, 0.3)" />
            <line x1="75" y1="25" x2="78" y2="20" strokeWidth="3" />
          </g>
        )}
        {type === 'squat' && (
          <g>
            <circle cx="50" cy="25" r="7" />
            <path d="M 50,32 L 50,55 M 50,55 L 30,80 M 50,55 L 70,80" />
            <path d="M 50,38 L 40,42 L 50,48" />
            <path d="M 50,38 L 60,42 L 50,48" />
            <circle cx="50" cy="48" r="8" fill="rgba(245, 158, 11, 0.25)" />
          </g>
        )}
        {type === 'snatch' && (
          <g>
            <circle cx="50" cy="25" r="7" />
            <path d="M 50,32 L 50,65 M 50,65 L 35,90 M 50,65 L 65,90" />
            <line x1="50" y1="38" x2="50" y2="8" strokeWidth="3" />
            <circle cx="50" cy="5" r="7" fill="rgba(245, 158, 11, 0.4)" />
          </g>
        )}
        {(!['swing', 'tgu', 'squat', 'snatch'].includes(type)) && (
          <g>
            <circle cx="50" cy="25" r="7" />
            <path d="M 50,32 L 50,65 M 50,65 L 35,90 M 50,65 L 65,90" />
            <path d="M 50,40 L 70,55" />
            <path d="M 50,40 L 30,55" />
            <circle cx="72" cy="58" r="7" fill="rgba(245, 158, 11, 0.3)" />
          </g>
        )}
      </svg>
      <span className="absolute bottom-2 right-3 text-[10px] font-mono tracking-widest text-amber-500/80 uppercase">Form Diagram</span>
    </div>
  );
}

export default function App() {
  const [activeTab, setActiveTab] = useState('programs');
  const [selectedLevel, setSelectedLevel] = useState('All');
  const [customExercises, setCustomExercises] = useState([]);
  const [customTitle, setCustomTitle] = useState('My Custom Kettlebell Routine');

  // Interactive Weight Calculator State
  const [calcGender, setCalcGender] = useState('male');
  const [calcLevel, setCalcLevel] = useState('Intermediate');
  const [calcUnit, setCalcUnit] = useState('kg');
  const [calcInputWeight, setCalcInputWeight] = useState(16);

  // Active Workout Engine State
  const [activeWorkout, setActiveWorkout] = useState(null);
  const [currentExIndex, setCurrentExIndex] = useState(0);
  const [currentSet, setCurrentSet] = useState(1);
  const [timerSeconds, setTimerSeconds] = useState(0);
  const [isTimerRunning, setIsTimerRunning] = useState(false);
  const [timerPhase, setTimerPhase] = useState('work'); // 'work' or 'rest'
  const [selectedWeight, setSelectedWeight] = useState(16);
  const [soundEnabled, setSoundEnabled] = useState(true);

  // Logs state
  const [workoutLogs, setWorkoutLogs] = useState(() => {
    const saved = localStorage.getItem('kb_workout_logs');
    return saved ? JSON.parse(saved) : INITIAL_LOGS;
  });

  useEffect(() => {
    localStorage.setItem('kb_workout_logs', JSON.stringify(workoutLogs));
  }, [workoutLogs]);

  // Audio Beeper Simulation using Web Audio API
  const playBeep = (freq = 800, duration = 150) => {
    if (!soundEnabled) return;
    try {
      const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.type = 'sine';
      osc.frequency.value = freq;
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      osc.start();
      gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + duration / 1000);
      setTimeout(() => osc.stop(), duration);
    } catch (e) {
      // Audio context fallbacks
    }
  };

  // Timer Interval Effect
  useEffect(() => {
    let interval = null;
    if (isTimerRunning && activeWorkout) {
      interval = setInterval(() => {
        setTimerSeconds((prev) => {
          if (prev <= 1 && prev > 0) {
            playBeep(1200, 300); // end cue sound
            handleNextPhaseOrSet();
            return 0;
          }
          if (prev <= 4 && prev > 1) {
            playBeep(600, 100); // countdown sound
          }
          return prev - 1;
        });
      }, 1000);
    } else {
      clearInterval(interval);
    }
    return () => clearInterval(interval);
  }, [isTimerRunning, activeWorkout, timerPhase, currentSet, currentExIndex]);

  const startWorkout = (program) => {
    setActiveWorkout(program);
    setCurrentExIndex(0);
    setCurrentSet(1);
    const firstEx = program.exercises[0];
    setTimerPhase('work');
    setTimerSeconds(firstEx.workSec || 30);
    setIsTimerRunning(true);
    setActiveTab('active-workout');
  };

  const handleNextPhaseOrSet = () => {
    if (!activeWorkout) return;
    const currentEx = activeWorkout.exercises[currentExIndex];
    const totalSets = currentEx.sets || 5;

    if (timerPhase === 'work') {
      if (currentSet < totalSets) {
        setTimerPhase('rest');
        setTimerSeconds(currentEx.restSec || 30);
      } else {
        // Move to next exercise
        if (currentExIndex < activeWorkout.exercises.length - 1) {
          setCurrentExIndex((prev) => prev + 1);
          setCurrentSet(1);
          setTimerPhase('work');
          const nextEx = activeWorkout.exercises[currentExIndex + 1];
          setTimerSeconds(nextEx.workSec || 30);
        } else {
          // Workout Complete
          completeWorkout();
        }
      }
    } else {
      // Resting complete, back to work set
      setCurrentSet((prev) => prev + 1);
      setTimerPhase('work');
      setTimerSeconds(currentEx.workSec || 30);
    }
  };

  const completeWorkout = () => {
    setIsTimerRunning(false);
    playBeep(1500, 600);

    const totalEstVolume = (activeWorkout.exercises.length * 5 * 10 * selectedWeight);
    const newLog = {
      id: Date.now().toString(),
      date: new Date().toISOString().split('T')[0],
      workoutTitle: activeWorkout.title,
      durationMin: Math.max(12, Math.floor(activeWorkout.exercises.length * 6)),
      totalVolumeKg: totalEstVolume,
      setsCompleted: activeWorkout.exercises.length * 5
    };

    setWorkoutLogs([newLog, ...workoutLogs]);
    alert(`🎉 Workout Complete! You logged ${totalEstVolume} kg total volume.`);
    setActiveWorkout(null);
    setActiveTab('analytics');
  };

  const getRecommendedWeight = (exId) => {
    const ex = EXERCISE_LIBRARY.find(e => e.id === exId) || EXERCISE_LIBRARY[0];
    let base = ex.startingWeightKg[calcGender] || 12;
    if (calcLevel === 'Beginner') base = Math.max(6, base - 4);
    if (calcLevel === 'Advanced') base = base + 8;
    return calcUnit === 'lbs' ? Math.round(base * 2.20462) : base;
  };

  const convertedKgToLbs = Math.round(calcInputWeight * 2.20462);
  const convertedLbsToKg = (calcInputWeight * 0.453592).toFixed(1);

  const filteredPrograms = PRESET_PROGRAMS.filter(p =>
    selectedLevel === 'All' ? true : p.level === selectedLevel
  );

  return (
    <div className="min-h-screen bg-slate-950 text-slate-100 flex flex-col font-sans antialiased selection:bg-amber-500 selection:text-slate-950">
      
      {/* HEADER NAVBAR */}
      {}
      <header className="border-b border-slate-800 bg-slate-900/90 backdrop-blur sticky top-0 z-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
          <div className="flex items-center space-x-3 cursor-pointer" onClick={() => setActiveTab('programs')}>
            <div className="p-2 bg-gradient-to-tr from-amber-600 to-orange-500 rounded-xl text-slate-950 shadow-lg shadow-amber-500/20">
              <Flame className="w-6 h-6 fill-current" />
            </div>
            <div>
              <h1 className="text-xl font-extrabold tracking-tight bg-gradient-to-r from-amber-400 via-orange-400 to-amber-200 bg-clip-text text-transparent">
                KETTLEBELL FORGE
              </h1>
              <p className="text-[10px] uppercase tracking-widest text-amber-500 font-semibold -mt-1">Precision Training</p>
            </div>
          </div>

          <nav className="hidden md:flex items-center space-x-1">
            {[
              { id: 'programs', label: 'Routines', icon: Shield },
              { id: 'exercises', label: 'Movement Library', icon: Dumbbell },
              { id: 'builder', label: 'Custom Builder', icon: Plus },
              { id: 'calculator', label: 'Weight Guide', icon: Scale },
              { id: 'analytics', label: 'Progress & Logs', icon: BarChart2 }
            ].map(tab => {
              const Icon = tab.icon;
              const isActive = activeTab === tab.id;
              return (
                <button
                  key={tab.id}
                  onClick={() => setActiveTab(tab.id)}
                  className={`flex items-center space-x-2 px-3.5 py-2 rounded-lg text-sm font-medium transition-all ${
                    isActive
                      ? 'bg-amber-500/10 text-amber-400 border border-amber-500/30 shadow-inner'
                      : 'text-slate-400 hover:text-slate-200 hover:bg-slate-800/60'
                  }`}
                >
                  <Icon className="w-4 h-4" />
                  <span>{tab.label}</span>
                </button>
              );
            })}
          </nav>

          {/* Active Workout Quick-Access Button */}
          {activeWorkout && (
            <button
              onClick={() => setActiveTab('active-workout')}
              className="flex items-center space-x-2 px-3 py-1.5 bg-amber-500 text-slate-950 font-bold rounded-lg text-xs animate-pulse hover:bg-amber-400"
            >
              <Zap className="w-4 h-4 fill-current" />
              <span>IN WORKOUT</span>
            </button>
          )}
        </div>
      </header>

      {/* MOBILE BOTTOM NAVIGATION */}
      {}
      <div className="md:hidden fixed bottom-0 left-0 right-0 bg-slate-900 border-t border-slate-800 z-50 px-2 py-2 flex justify-around items-center">
        {[
          { id: 'programs', label: 'Routines', icon: Shield },
          { id: 'exercises', label: 'Library', icon: Dumbbell },
          { id: 'builder', label: 'Build', icon: Plus },
          { id: 'calculator', label: 'Guide', icon: Scale },
          { id: 'analytics', label: 'Stats', icon: BarChart2 }
        ].map(tab => {
          const Icon = tab.icon;
          const isActive = activeTab === tab.id;
          return (
            <button
              key={tab.id}
              onClick={() => setActiveTab(tab.id)}
              className={`flex flex-col items-center py-1 px-2 rounded-md ${
                isActive ? 'text-amber-400 font-bold' : 'text-slate-400 text-xs'
              }`}
            >
              <Icon className="w-5 h-5 mb-0.5" />
              <span className="text-[10px]">{tab.label}</span>
            </button>
          );
        })}
      </div>

      {/* MAIN CONTENT BODY */}
      <main className="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-8 mb-16 md:mb-0">
        
        {/* TAB 1: PRESET WORKOUT PROGRAMS */}
        {}
        {activeTab === 'programs' && (
          <div className="space-y-8">
            <div className="flex flex-col md:flex-row md:items-center justify-between gap-4 bg-slate-900/60 p-6 rounded-2xl border border-slate-800">
              <div>
                <h2 className="text-2xl font-bold tracking-tight text-white flex items-center gap-2">
                  <Shield className="w-6 h-6 text-amber-500" /> Preset Workout Protocols
                </h2>
                <p className="text-slate-400 text-sm mt-1">
                  High-yield kettlebell programs designed for strength, mobility, and cardiovascular endurance.
                </p>
              </div>

              {/* Level Filter Switcher */}
              <div className="flex items-center space-x-1 bg-slate-950 p-1 rounded-xl border border-slate-800">
                {['All', 'Beginner', 'Intermediate', 'Advanced'].map(lvl => (
                  <button
                    key={lvl}
                    onClick={() => setSelectedLevel(lvl)}
                    className={`px-3 py-1.5 rounded-lg text-xs font-semibold transition-all ${
                      selectedLevel === lvl
                        ? 'bg-amber-500 text-slate-950 shadow'
                        : 'text-slate-400 hover:text-slate-200'
                    }`}
                  >
                    {lvl}
                  </button>
                ))}
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              {filteredPrograms.map(program => (
                <div
                  key={program.id}
                  className="bg-slate-900 border border-slate-800 hover:border-amber-500/40 rounded-2xl p-6 flex flex-col justify-between transition-all duration-300 hover:shadow-xl hover:shadow-amber-500/5 group"
                >
                  <div className="space-y-4">
                    <div className="flex items-start justify-between">
                      <span className={`px-2.5 py-1 rounded-md text-xs font-bold uppercase tracking-wider ${
                        program.level === 'Beginner' ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/20' :
                        program.level === 'Intermediate' ? 'bg-amber-500/10 text-amber-400 border border-amber-500/20' :
                        'bg-rose-500/10 text-rose-400 border border-rose-500/20'
                      }`}>
                        {program.level}
                      </span>
                      <span className="flex items-center text-xs text-slate-400 font-mono">
                        <Clock className="w-3.5 h-3.5 mr-1 text-slate-500" />
                        {program.duration}
                      </span>
                    </div>

                    <div>
                      <h3 className="text-xl font-bold text-slate-100 group-hover:text-amber-400 transition-colors">
                        {program.title}
                      </h3>
                      <p className="text-slate-400 text-sm mt-2 leading-relaxed">
                        {program.description}
                      </p>
                    </div>

                    {/* Exercise Tags / Stack */}
                    <div className="pt-2">
                      <div className="text-xs text-slate-500 font-semibold mb-2 uppercase tracking-wider">Routine Highlights</div>
                      <div className="space-y-2">
                        {program.exercises.map((ex, i) => (
                          <div key={i} className="flex items-center justify-between bg-slate-950/60 px-3 py-2 rounded-lg text-xs text-slate-300 border border-slate-800/80">
                            <span className="font-medium text-amber-300">{ex.title}</span>
                            <span className="text-slate-400 font-mono">{ex.sets ? `${ex.sets} Sets` : '4 Rounds'}</span>
                          </div>
                        ))}
                      </div>
                    </div>
                  </div>

                  <div className="pt-6 mt-6 border-t border-slate-800/80 flex items-center justify-between">
                    <div className="flex flex-wrap gap-1">
                      {program.tags.map((tag, i) => (
                        <span key={i} className="text-[10px] bg-slate-800 text-slate-400 px-2 py-0.5 rounded">
                          #{tag}
                        </span>
                      ))}
                    </div>

                    <button
                      onClick={() => startWorkout(program)}
                      className="flex items-center space-x-2 bg-gradient-to-r from-amber-500 to-orange-500 hover:from-amber-400 hover:to-orange-400 text-slate-950 font-bold px-4 py-2 rounded-xl transition-all shadow-md shadow-amber-500/20"
                    >
                      <Play className="w-4 h-4 fill-current" />
                      <span>Start Session</span>
                    </button>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* TAB 2: EXERCISE LIBRARY */}
        {}
        {activeTab === 'exercises' && (
          <div className="space-y-8">
            <div className="bg-slate-900/60 p-6 rounded-2xl border border-slate-800">
              <h2 className="text-2xl font-bold text-white flex items-center gap-2">
                <Dumbbell className="w-6 h-6 text-amber-500" /> Fundamental Movement Guide
              </h2>
              <p className="text-slate-400 text-sm mt-1">
                Master biomechanics, essential alignment cues, and common mistakes to prevent injury and optimize muscle engagement.
              </p>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              {EXERCISE_LIBRARY.map(ex => (
                <div key={ex.id} className="bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden flex flex-col justify-between hover:border-slate-700 transition">
                  <div className="p-5 space-y-4">
                    <MovementVisualizer type={ex.svgType} />

                    <div>
                      <div className="flex items-center justify-between mb-1">
                        <span className="text-xs font-semibold text-amber-500 uppercase tracking-wider">{ex.category}</span>
                        <span className="text-[10px] font-mono px-2 py-0.5 bg-slate-800 text-slate-300 rounded">{ex.difficulty}</span>
                      </div>
                      <h3 className="text-lg font-bold text-slate-100">{ex.name}</h3>
                      <p className="text-xs text-slate-400 mt-1 leading-relaxed">{ex.description}</p>
                    </div>

                    {/* Primary & Secondary Muscles */}
                    <div className="space-y-1">
                      <div className="text-[11px] font-medium text-slate-400">Target Muscles:</div>
                      <div className="flex flex-wrap gap-1">
                        {ex.primaryMuscles.map((m, i) => (
                          <span key={i} className="text-[10px] bg-amber-500/10 text-amber-400 border border-amber-500/20 px-2 py-0.5 rounded-full font-medium">
                            {m}
                          </span>
                        ))}
                      </div>
                    </div>

                    {/* Key Form Cues */}
                    <div className="space-y-2 pt-2">
                      <div className="text-xs font-semibold text-slate-300 flex items-center gap-1">
                        <CheckCircle className="w-3.5 h-3.5 text-emerald-400" /> Essential Form Cues:
                      </div>
                      <ul className="space-y-1 text-xs text-slate-400 pl-2">
                        {ex.cues.map((cue, idx) => (
                          <li key={idx} className="flex items-start gap-1.5">
                            <span className="text-amber-500 font-bold">•</span>
                            <span>{cue}</span>
                          </li>
                        ))}
                      </ul>
                    </div>

                    {/* Common Pitfalls */}
                    <div className="space-y-2 pt-1 border-t border-slate-800/60">
                      <div className="text-xs font-semibold text-rose-400 flex items-center gap-1">
                        <Info className="w-3.5 h-3.5" /> Common Mistakes:
                      </div>
                      <ul className="space-y-1 text-xs text-slate-400 pl-2">
                        {ex.mistakes.map((m, idx) => (
                          <li key={idx} className="flex items-start gap-1.5">
                            <span className="text-rose-500 font-bold">×</span>
                            <span>{m}</span>
                          </li>
                        ))}
                      </ul>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* TAB 3: CUSTOM WORKOUT BUILDER */}
        {}
        {activeTab === 'builder' && (
          <div className="space-y-8">
            <div className="bg-slate-900/60 p-6 rounded-2xl border border-slate-800">
              <h2 className="text-2xl font-bold text-white flex items-center gap-2">
                <Plus className="w-6 h-6 text-amber-500" /> Build Custom Workout Routine
              </h2>
              <p className="text-slate-400 text-sm mt-1">
                Select movements from the library, customize set/rep parameters, and trigger custom timer sessions.
              </p>
            </div>

            <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
              {/* Exercise Selector Palette */}
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-5 space-y-4">
                <h3 className="text-sm font-bold uppercase tracking-wider text-slate-300 flex items-center gap-2">
                  <Dumbbell className="w-4 h-4 text-amber-500" /> Select Movements
                </h3>
                <div className="space-y-2 max-h-[500px] overflow-y-auto pr-1">
                  {EXERCISE_LIBRARY.map(ex => (
                    <div
                      key={ex.id}
                      className="p-3 bg-slate-950 border border-slate-800 rounded-xl flex items-center justify-between hover:border-amber-500/50 transition cursor-pointer"
                      onClick={() => {
                        setCustomExercises([
                          ...customExercises,
                          {
                            exerciseId: ex.id,
                            title: ex.name,
                            sets: 4,
                            reps: 10,
                            workSec: 30,
                            restSec: 30
                          }
                        ]);
                      }}
                    >
                      <div>
                        <div className="text-sm font-semibold text-slate-200">{ex.name}</div>
                        <div className="text-[10px] text-slate-400">{ex.category}</div>
                      </div>
                      <button className="p-1.5 bg-amber-500/10 hover:bg-amber-500 hover:text-slate-950 text-amber-400 rounded-lg transition">
                        <Plus className="w-4 h-4" />
                      </button>
                    </div>
                  ))}
                </div>
              </div>

              {/* Routine Canvas */}
              <div className="lg:col-span-2 bg-slate-900 border border-slate-800 rounded-2xl p-6 space-y-6 flex flex-col justify-between">
                <div className="space-y-6">
                  <div>
                    <label className="text-xs font-semibold text-slate-400 uppercase tracking-wider block mb-2">
                      Routine Name
                    </label>
                    <input
                      type="text"
                      value={customTitle}
                      onChange={(e) => setCustomTitle(e.target.value)}
                      className="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-slate-100 font-bold focus:outline-none focus:border-amber-500"
                    />
                  </div>

                  <div>
                    <div className="flex items-center justify-between mb-3">
                      <h4 className="text-sm font-semibold text-slate-300 uppercase tracking-wider">
                        Exercise Stack ({customExercises.length})
                      </h4>
                      {customExercises.length > 0 && (
                        <button
                          onClick={() => setCustomExercises([])}
                          className="text-xs text-rose-400 hover:underline flex items-center gap-1"
                        >
                          <Trash2 className="w-3.5 h-3.5" /> Clear All
                        </button>
                      )}
                    </div>

                    {customExercises.length === 0 ? (
                      <div className="text-center py-12 border-2 border-dashed border-slate-800 rounded-2xl text-slate-500 text-sm">
                        Click movements on the left to add them to your custom stack.
                      </div>
                    ) : (
                      <div className="space-y-3">
                        {customExercises.map((item, idx) => (
                          <div key={idx} className="bg-slate-950 p-4 rounded-xl border border-slate-800 flex flex-col sm:flex-row sm:items-center justify-between gap-4">
                            <div className="font-semibold text-amber-400 text-sm">
                              {idx + 1}. {item.title}
                            </div>

                            <div className="flex items-center space-x-3 text-xs">
                              <div className="flex items-center space-x-1">
                                <span className="text-slate-400">Sets:</span>
                                <input
                                  type="number"
                                  value={item.sets}
                                  onChange={(e) => {
                                    const updated = [...customExercises];
                                    updated[idx].sets = parseInt(e.target.value) || 1;
                                    setCustomExercises(updated);
                                  }}
                                  className="w-12 bg-slate-900 border border-slate-800 rounded text-center py-1 font-mono text-white"
                                />
                              </div>

                              <div className="flex items-center space-x-1">
                                <span className="text-slate-400">Work (s):</span>
                                <input
                                  type="number"
                                  value={item.workSec}
                                  onChange={(e) => {
                                    const updated = [...customExercises];
                                    updated[idx].workSec = parseInt(e.target.value) || 10;
                                    setCustomExercises(updated);
                                  }}
                                  className="w-14 bg-slate-900 border border-slate-800 rounded text-center py-1 font-mono text-white"
                                />
                              </div>

                              <div className="flex items-center space-x-1">
                                <span className="text-slate-400">Rest (s):</span>
                                <input
                                  type="number"
                                  value={item.restSec}
                                  onChange={(e) => {
                                    const updated = [...customExercises];
                                    updated[idx].restSec = parseInt(e.target.value) || 5;
                                    setCustomExercises(updated);
                                  }}
                                  className="w-14 bg-slate-900 border border-slate-800 rounded text-center py-1 font-mono text-white"
                                />
                              </div>

                              <button
                                onClick={() => {
                                  setCustomExercises(customExercises.filter((_, i) => i !== idx));
                                }}
                                className="text-slate-500 hover:text-rose-400 p-1"
                              >
                                <Trash2 className="w-4 h-4" />
                              </button>
                            </div>
                          </div>
                        ))}
                      </div>
                    )}
                  </div>
                </div>

                {customExercises.length > 0 && (
                  <button
                    onClick={() => {
                      const newProgram = {
                        id: 'custom-' + Date.now(),
                        title: customTitle,
                        level: 'Custom',
                        duration: `${customExercises.length * 5} min`,
                        tags: ['Custom', 'User Routine'],
                        description: 'Custom build workout protocol.',
                        exercises: customExercises
                      };
                      startWorkout(newProgram);
                    }}
                    className="w-full mt-6 py-3.5 bg-gradient-to-r from-amber-500 to-orange-500 text-slate-950 font-extrabold rounded-xl shadow-lg shadow-amber-500/20 hover:from-amber-400 hover:to-orange-400 transition flex items-center justify-center space-x-2"
                  >
                    <Play className="w-5 h-5 fill-current" />
                    <span>Launch Custom Routine</span>
                  </button>
                )}
              </div>
            </div>
          </div>
        )}

        {/* TAB 4: WEIGHT CALCULATOR & CONVERTER */}
        {}
        {activeTab === 'calculator' && (
          <div className="space-y-8 max-w-4xl mx-auto">
            <div className="bg-slate-900/60 p-6 rounded-2xl border border-slate-800">
              <h2 className="text-2xl font-bold text-white flex items-center gap-2">
                <Scale className="w-6 h-6 text-amber-500" /> Kettlebell Weight Guide & Calculator
              </h2>
              <p className="text-slate-400 text-sm mt-1">
                Determine optimum starting weights based on exercise biomechanics, plus convert load units instantly.
              </p>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
              {/* Calculator Form */}
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 space-y-6">
                <h3 className="text-lg font-bold text-amber-400 flex items-center gap-2">
                  <Sliders className="w-5 h-5" /> Weight Recommendation Selector
                </h3>

                {/* Gender */}
                <div className="space-y-2">
                  <label className="text-xs text-slate-400 font-semibold uppercase tracking-wider">Biological Frame / Gender Standard</label>
                  <div className="grid grid-cols-2 gap-2">
                    {['male', 'female'].map(g => (
                      <button
                        key={g}
                        onClick={() => setCalcGender(g)}
                        className={`py-2 rounded-xl text-xs font-bold uppercase transition ${
                          calcGender === g ? 'bg-amber-500 text-slate-950' : 'bg-slate-950 text-slate-400 border border-slate-800'
                        }`}
                      >
                        {g}
                      </button>
                    ))}
                  </div>
                </div>

                {/* Experience Level */}
                <div className="space-y-2">
                  <label className="text-xs text-slate-400 font-semibold uppercase tracking-wider">Experience Level</label>
                  <div className="grid grid-cols-3 gap-2">
                    {['Beginner', 'Intermediate', 'Advanced'].map(lvl => (
                      <button
                        key={lvl}
                        onClick={() => setCalcLevel(lvl)}
                        className={`py-2 rounded-xl text-xs font-bold transition ${
                          calcLevel === lvl ? 'bg-amber-500 text-slate-950' : 'bg-slate-950 text-slate-400 border border-slate-800'
                        }`}
                      >
                        {lvl}
                      </button>
                    ))}
                  </div>
                </div>

                {/* Unit Switch */}
                <div className="space-y-2">
                  <label className="text-xs text-slate-400 font-semibold uppercase tracking-wider">Preferred Unit</label>
                  <div className="grid grid-cols-2 gap-2">
                    {['kg', 'lbs'].map(u => (
                      <button
                        key={u}
                        onClick={() => setCalcUnit(u)}
                        className={`py-2 rounded-xl text-xs font-bold uppercase transition ${
                          calcUnit === u ? 'bg-amber-500 text-slate-950' : 'bg-slate-950 text-slate-400 border border-slate-800'
                        }`}
                      >
                        {u}
                      </button>
                    ))}
                  </div>
                </div>
              </div>

              {/* Recommended Output Matrix */}
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 space-y-4">
                <h3 className="text-lg font-bold text-slate-200">Recommended Load Matrix</h3>
                <div className="space-y-3">
                  {EXERCISE_LIBRARY.slice(0, 5).map(ex => (
                    <div key={ex.id} className="flex items-center justify-between p-3 bg-slate-950 rounded-xl border border-slate-800">
                      <span className="text-sm font-medium text-slate-300">{ex.name}</span>
                      <span className="text-base font-extrabold text-amber-400 font-mono">
                        {getRecommendedWeight(ex.id)} {calcUnit}
                      </span>
                    </div>
                  ))}
                </div>
              </div>
            </div>

            {/* Quick Converter Box */}
            <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 space-y-4">
              <h3 className="text-lg font-bold text-slate-200 flex items-center gap-2">
                <RefreshCw className="w-5 h-5 text-amber-500" /> Instant Unit Converter
              </h3>
              <div className="grid grid-cols-1 sm:grid-cols-3 gap-4 items-center">
                <div>
                  <label className="text-xs text-slate-400 mb-1 block">Input Weight Value</label>
                  <input
                    type="number"
                    value={calcInputWeight}
                    onChange={(e) => setCalcInputWeight(parseFloat(e.target.value) || 0)}
                    className="w-full bg-slate-950 border border-slate-800 rounded-xl p-3 font-mono font-bold text-amber-400 focus:outline-none focus:border-amber-500 text-lg"
                  />
                </div>
                <div className="p-4 bg-slate-950 rounded-xl border border-slate-800 text-center">
                  <div className="text-xs text-slate-500">Kilograms to Pounds</div>
                  <div className="text-xl font-extrabold text-slate-100 font-mono mt-1">{convertedKgToLbs} lbs</div>
                </div>
                <div className="p-4 bg-slate-950 rounded-xl border border-slate-800 text-center">
                  <div className="text-xs text-slate-500">Pounds to Kilograms</div>
                  <div className="text-xl font-extrabold text-slate-100 font-mono mt-1">{convertedLbsToKg} kg</div>
                </div>
              </div>
            </div>
          </div>
        )}

        {/* TAB 5: ACTIVE WORKOUT TIMER & MODE */}
        {}
        {activeTab === 'active-workout' && activeWorkout && (
          <div className="max-w-2xl mx-auto space-y-8">
            <div className="bg-slate-900 border-2 border-amber-500/40 rounded-3xl p-6 sm:p-8 space-y-8 shadow-2xl shadow-amber-500/10">
              
              {/* Header HUD */}
              <div className="flex items-center justify-between border-b border-slate-800 pb-4">
                <div>
                  <span className="text-xs font-bold text-amber-500 uppercase tracking-widest">{activeWorkout.title}</span>
                  <h2 className="text-2xl font-extrabold text-white mt-0.5">
                    {activeWorkout.exercises[currentExIndex]?.title || 'Workout Session'}
                  </h2>
                </div>
                <button
                  onClick={() => setSoundEnabled(!soundEnabled)}
                  className="p-2.5 bg-slate-800 hover:bg-slate-700 rounded-xl text-slate-300 transition"
                >
                  {soundEnabled ? <Volume2 className="w-5 h-5 text-amber-400" /> : <VolumeX className="w-5 h-5 text-slate-500" />}
                </button>
              </div>

              {/* Main Ring / Display Timer */}
              <div className="flex flex-col items-center justify-center py-6">
                <div className={`w-56 h-56 rounded-full border-8 flex flex-col items-center justify-center transition-colors duration-500 shadow-inner ${
                  timerPhase === 'work'
                    ? 'border-amber-500 bg-amber-500/5 shadow-amber-500/20'
                    : 'border-emerald-500 bg-emerald-500/5 shadow-emerald-500/20'
                }`}>
                  <span className={`text-xs font-extrabold uppercase tracking-widest ${
                    timerPhase === 'work' ? 'text-amber-400 animate-pulse' : 'text-emerald-400'
                  }`}>
                    {timerPhase === 'work' ? '🔥 WORK INTERVAL' : '🧘 REST INTERVAL'}
                  </span>
                  <span className="text-6xl font-black font-mono tracking-tighter text-white mt-1">
                    {timerSeconds}s
                  </span>
                  <span className="text-xs text-slate-400 mt-2 font-mono">
                    Set {currentSet} of {activeWorkout.exercises[currentExIndex]?.sets || 5}
                  </span>
                </div>
              </div>

              {/* Weight Selector for Log Accuracy */}
              <div className="bg-slate-950 p-4 rounded-2xl border border-slate-800 flex items-center justify-between">
                <span className="text-xs font-semibold text-slate-400 uppercase tracking-wider">Active Kettlebell Load:</span>
                <div className="flex items-center space-x-2">
                  {[8, 12, 16, 20, 24, 32].map(w => (
                    <button
                      key={w}
                      onClick={() => setSelectedWeight(w)}
                      className={`px-2.5 py-1 rounded-lg text-xs font-mono font-bold transition ${
                        selectedWeight === w
                          ? 'bg-amber-500 text-slate-950'
                          : 'bg-slate-900 text-slate-400 hover:bg-slate-800'
                      }`}
                    >
                      {w}kg
                    </button>
                  ))}
                </div>
              </div>

              {/* Action Controls */}
              <div className="grid grid-cols-3 gap-4">
                <button
                  onClick={() => setIsTimerRunning(!isTimerRunning)}
                  className={`py-4 rounded-2xl font-extrabold text-sm uppercase tracking-wider flex items-center justify-center space-x-2 transition shadow-lg ${
                    isTimerRunning
                      ? 'bg-slate-800 text-amber-400 border border-amber-500/30'
                      : 'bg-amber-500 text-slate-950 hover:bg-amber-400'
                  }`}
                >
                  {isTimerRunning ? <Pause className="w-5 h-5" /> : <Play className="w-5 h-5 fill-current" />}
                  <span>{isTimerRunning ? 'Pause' : 'Resume'}</span>
                </button>

                <button
                  onClick={handleNextPhaseOrSet}
                  className="py-4 bg-slate-800 hover:bg-slate-700 text-slate-200 font-bold text-sm rounded-2xl flex items-center justify-center space-x-1 border border-slate-700"
                >
                  <ChevronRight className="w-5 h-5" />
                  <span>Next Set</span>
                </button>

                <button
                  onClick={completeWorkout}
                  className="py-4 bg-emerald-500/20 hover:bg-emerald-500/30 text-emerald-400 border border-emerald-500/40 font-bold text-sm rounded-2xl flex items-center justify-center space-x-1"
                >
                  <Check className="w-5 h-5" />
                  <span>Finish</span>
                </button>
              </div>
            </div>
          </div>
        )}

        {/* TAB 6: PROGRESS LOGS & ANALYTICS */}
        {}
        {activeTab === 'analytics' && (
          <div className="space-y-8">
            <div className="bg-slate-900/60 p-6 rounded-2xl border border-slate-800">
              <h2 className="text-2xl font-bold text-white flex items-center gap-2">
                <BarChart2 className="w-6 h-6 text-amber-500" /> Training Volume & History
              </h2>
              <p className="text-slate-400 text-sm mt-1">
                Monitor progressive overload, completed training volume (kg moved), and workout consistency.
              </p>
            </div>

            {/* Top Stat Cards */}
            <div className="grid grid-cols-1 sm:grid-cols-3 gap-6">
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-5">
                <div className="text-xs font-semibold text-slate-400 uppercase tracking-wider">Total Workouts Completed</div>
                <div className="text-3xl font-black text-amber-400 font-mono mt-2">{workoutLogs.length}</div>
              </div>
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-5">
                <div className="text-xs font-semibold text-slate-400 uppercase tracking-wider">Total Volume Lifted</div>
                <div className="text-3xl font-black text-amber-400 font-mono mt-2">
                  {workoutLogs.reduce((acc, curr) => acc + curr.totalVolumeKg, 0).toLocaleString()} kg
                </div>
              </div>
              <div className="bg-slate-900 border border-slate-800 rounded-2xl p-5">
                <div className="text-xs font-semibold text-slate-400 uppercase tracking-wider">Active Streak</div>
                <div className="text-3xl font-black text-emerald-400 font-mono mt-2">4 Days 🔥</div>
              </div>
            </div>

            {/* Recharts Chart */}
            <div className="bg-slate-900 border border-slate-800 rounded-2xl p-6 space-y-4">
              <h3 className="text-lg font-bold text-slate-200">Volume Progression Trend (Kg)</h3>
              <div className="h-64 w-full">
                <ResponsiveContainer width="100%" height="100%">
                  <AreaChart data={workoutLogs}>
                    <defs>
                      <linearGradient id="colorVol" x1="0" y1="0" x2="0" y2="1">
                        <stop offset="5%" stopColor="#f59e0b" stopOpacity={0.4}/>
                        <stop offset="95%" stopColor="#f59e0b" stopOpacity={0}/>
                      </linearGradient>
                    </defs>
                    <XAxis dataKey="date" stroke="#64748b" fontSize={12} />
                    <YAxis stroke="#64748b" fontSize={12} />
                    <Tooltip
                      contentStyle={{ backgroundColor: '#0f172a', borderColor: '#334155', borderRadius: '8px' }}
                      itemStyle={{ color: '#f59e0b' }}
                    />
                    <Area type="monotone" dataKey="totalVolumeKg" stroke="#f59e0b" strokeWidth={3} fillOpacity={1} fill="url(#colorVol)" />
                  </AreaChart>
                </ResponsiveContainer>
              </div>
            </div>

            {/* Workout History Table */}
            <div className="bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden">
              <div className="p-5 border-b border-slate-800">
                <h3 className="text-lg font-bold text-slate-200">Completed Sessions Log</h3>
              </div>
              <div className="divide-y divide-slate-800/60 overflow-x-auto">
                {workoutLogs.map((log) => (
                  <div key={log.id} className="p-4 flex items-center justify-between text-sm hover:bg-slate-800/30 transition">
                    <div>
                      <div className="font-bold text-slate-200">{log.workoutTitle}</div>
                      <div className="text-xs text-slate-500 font-mono mt-0.5">{log.date} • {log.durationMin} minutes</div>
                    </div>
                    <div className="text-right">
                      <div className="font-mono font-extrabold text-amber-400">{log.totalVolumeKg.toLocaleString()} kg</div>
                      <div className="text-xs text-slate-500">{log.setsCompleted} sets logged</div>
                    </div>
                  </div>
                ))}
              </div>
            </div>
          </div>
        )}

      </main>

      {/* FOOTER */}
      {}
      <footer className="border-t border-slate-800 bg-slate-900/50 py-6 text-center text-xs text-slate-500">
        <div className="max-w-7xl mx-auto px-4 flex flex-col sm:flex-row items-center justify-between gap-2">
          <span>© 2026 Kettlebell Forge. Built for high-performance minimalist conditioning.</span>
          <span className="font-mono text-amber-500/80">Iron • Form • Discipline</span>
        </div>
      </footer>
    </div>
  );
}