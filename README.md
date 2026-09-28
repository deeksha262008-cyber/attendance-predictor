import React, { useState, useMemo } from 'react';
import { 
  Calendar, AlertTriangle, CheckCircle2, XCircle, 
  Bot, Send, Sparkles, UserCheck, ShieldAlert, BarChart3
} from 'lucide-react';

// --- SEMESTER METADATA ---
const SEMESTER_START = new Date('2026-08-29');
const SEMESTER_END = new Date('2026-11-29');

// --- 10 CLASS SECTIONS TIMETABLE DATA (EXTRACTED FROM TIMETABLES) ---
const SECTION_TIMETABLES = {
  'I ECE-A': [
    { code: '21MAB102T', name: 'Advanced Calculus & Complex Analysis', totalSem: 52, defaultAttended: 32, defaultConducted: 38 },
    { code: '21CYB101J', name: 'Chemistry', totalSem: 60, defaultAttended: 38, defaultConducted: 42 },
    { code: '21BTB102J', name: 'Electronic System & PCB Design', totalSem: 30, defaultAttended: 18, defaultConducted: 22 },
    { code: '21CSS101J', name: 'Programming for Problem Solving', totalSem: 50, defaultAttended: 30, defaultConducted: 36 },
    { code: '21LEH104T', name: 'German Language', totalSem: 36, defaultAttended: 25, defaultConducted: 28 },
    { code: '21MES101L', name: 'Basic Civil & Mech Workshop', totalSem: 40, defaultAttended: 28, defaultConducted: 30 },
  ],
  'I ECE-B / EEE': [
    { code: '21MAB102T', name: 'Advanced Calculus & Complex Analysis', totalSem: 52, defaultAttended: 35, defaultConducted: 40 },
    { code: '21CYB101J', name: 'Chemistry', totalSem: 60, defaultAttended: 40, defaultConducted: 45 },
    { code: '21EEC101J', name: 'Electrical Circuits', totalSem: 32, defaultAttended: 15, defaultConducted: 25 },
    { code: '21CSS101J', name: 'Programming for Problem Solving', totalSem: 50, defaultAttended: 32, defaultConducted: 38 },
    { code: '21LEH104T', name: 'German Language', totalSem: 36, defaultAttended: 20, defaultConducted: 28 },
  ],
  'I ECE-DS': [
    { code: '21MAB102T', name: 'Advanced Calculus & Complex Analysis', totalSem: 52, defaultAttended: 30, defaultConducted: 42 },
    { code: '21CYB101J', name: 'Chemistry', totalSem: 60, defaultAttended: 42, defaultConducted: 48 },
    { code: '21BTB102J', name: 'Electronic System & PCB Design', totalSem: 30, defaultAttended: 20, defaultConducted: 24 },
    { code: '21CSS101J', name: 'Programming for Problem Solving', totalSem: 50, defaultAttended: 28, defaultConducted: 35 },
  ],
  'I Biotech-B / BME': [
    { code: '21MAB102T', name: 'Advanced Calculus & Complex Analysis', totalSem: 52, defaultAttended: 33, defaultConducted: 38 },
    { code: '21CYB101J', name: 'Chemistry', totalSem: 60, defaultAttended: 39, defaultConducted: 44 },
    { code: '21BTC105T', name: 'Cell Biology', totalSem: 30, defaultAttended: 22, defaultConducted: 24 },
    { code: '21BTC101T', name: 'Biochemistry', totalSem: 40, defaultAttended: 25, defaultConducted: 32 },
  ],
  'II ECE-DS A': [
    { code: '21MAB201T', name: 'Transforms & Boundary Value Problems', totalSem: 52, defaultAttended: 28, defaultConducted: 40 },
    { code: '21ECC201T', name: 'Solid State Devices', totalSem: 40, defaultAttended: 24, defaultConducted: 32 },
    { code: '21CSS201T', name: 'Computer Organization & Architecture', totalSem: 52, defaultAttended: 34, defaultConducted: 38 },
    { code: '21ECC203T', name: 'Digital Logic Design', totalSem: 40, defaultAttended: 18, defaultConducted: 30 },
  ],
  'II ECE-DS B': [
    { code: '21MAB201T', name: 'Transforms & Boundary Value Problems', totalSem: 52, defaultAttended: 30, defaultConducted: 38 },
    { code: '21ECC201T', name: 'Solid State Devices', totalSem: 40, defaultAttended: 22, defaultConducted: 31 },
    { code: '21CSS201T', name: 'Computer Organization & Architecture', totalSem: 52, defaultAttended: 36, defaultConducted: 40 },
    { code: '21ECC203T', name: 'Digital Logic Design', totalSem: 40, defaultAttended: 20, defaultConducted: 28 },
  ],
  'II BME': [
    { code: '21MAB201T', name: 'Transforms & Boundary Value Problems', totalSem: 52, defaultAttended: 32, defaultConducted: 36 },
    { code: '21BMC202T', name: 'Biomedical Signals and Systems', totalSem: 40, defaultAttended: 25, defaultConducted: 30 },
    { code: '21BMC203J', name: 'Electric and Electronic Circuits', totalSem: 52, defaultAttended: 20, defaultConducted: 35 },
    { code: '21BMC204J', name: 'Digital Logic for Medical Systems', totalSem: 40, defaultAttended: 28, defaultConducted: 30 },
  ],
  'III ECE-A': [
    { code: '21MAB302T', name: 'Discrete Mathematics', totalSem: 52, defaultAttended: 35, defaultConducted: 40 },
    { code: '21ECC301P', name: 'Microprocessor & Microcontrollers', totalSem: 52, defaultAttended: 30, defaultConducted: 38 },
    { code: '21ECC303T', name: 'VLSI Design and Technology', totalSem: 40, defaultAttended: 22, defaultConducted: 32 },
    { code: '21ECE468T', name: 'System and Network on Chip', totalSem: 40, defaultAttended: 28, defaultConducted: 30 },
  ],
  'III ECE-B': [
    { code: '21MAB302T', name: 'Discrete Mathematics', totalSem: 52, defaultAttended: 36, defaultConducted: 42 },
    { code: '21ECC301P', name: 'Microprocessor & Microcontrollers', totalSem: 52, defaultAttended: 32, defaultConducted: 39 },
    { code: '21ECC303T', name: 'VLSI Design and Technology', totalSem: 40, defaultAttended: 19, defaultConducted: 31 },
    { code: '21ECE468T', name: 'System and Network on Chip', totalSem: 40, defaultAttended: 27, defaultConducted: 29 },
  ],
  'III BME': [
    { code: '21MAB301T', name: 'Probability and Statistics', totalSem: 52, defaultAttended: 34, defaultConducted: 38 },
    { code: '21BMC302J', name: 'Microcontrollers in Medicine', totalSem: 52, defaultAttended: 30, defaultConducted: 37 },
    { code: '21BMC301J', name: 'Biomedical Signal Processing', totalSem: 52, defaultAttended: 22, defaultConducted: 36 },
    { code: '21BME266T', name: 'Biometrics', totalSem: 40, defaultAttended: 26, defaultConducted: 28 },
  ]
};

export default function AttendancePredictorDashboard() {
  const [selectedSection, setSelectedSection] = useState('I ECE-A');
  const [targetDate, setTargetDate] = useState('2026-11-29');
  const [subjects, setSubjects] = useState(SECTION_TIMETABLES['I ECE-A']);

  const [odClasses, setOdClasses] = useState(0);
  const [medicalLeaves, setMedicalLeaves] = useState(0);
  const [isChatOpen, setIsChatOpen] = useState(false);
  const [chatMessages, setChatMessages] = useState([
    { sender: 'bot', text: 'Hello! I am your AI Attendance Advisor. Ask me anything like: "If I take a 3-day sick leave starting tomorrow, will my Chemistry attendance drop below 75%?"' }
  ]);
  const [chatInput, setChatInput] = useState('');

  const todayFormatted = useMemo(() => {
    const today = new Date();
    return today.toISOString().split('T')[0];
  }, []);

  const handleSectionChange = (sec) => {
    setSelectedSection(sec);
    setSubjects(SECTION_TIMETABLES[sec] || SECTION_TIMETABLES['I ECE-A']);
    setOdClasses(0);
    setMedicalLeaves(0);
  };

  const handleSubjectInputChange = (idx, field, val) => {
    const updated = [...subjects];
    updated[idx][field] = Math.max(0, Number(val));
    setSubjects(updated);
  };

  const evaluatedSubjects = useMemo(() => {
    return subjects.map(sub => {
      const adjAttended = sub.defaultAttended + odClasses;
      const adjConducted = sub.defaultConducted + odClasses + medicalLeaves;
      
      const currentPct = adjConducted > 0 ? (adjAttended / adjConducted) * 100 : 0;
      const remainingClasses = Math.max(0, sub.totalSem - adjConducted);

      const req75 = Math.max(0, Math.ceil((0.75 * (adjConducted + remainingClasses) - adjAttended) / (1 - 0.75)));
      const req90 = Math.max(0, Math.ceil((0.90 * (adjConducted + remainingClasses) - adjAttended) / (1 - 0.90)));

      const isIrreversible = req75 > remainingClasses;

      return {
        ...sub,
        adjAttended,
        adjConducted,
        currentPct,
        remainingClasses,
        req75,
        req90,
        isIrreversible
      };
    });
  }, [subjects, odClasses, medicalLeaves]);

  const overallStats = useMemo(() => {
    let totalAttended = 0, totalConducted = 0, totalRemaining = 0, irreversibleCount = 0;
    evaluatedSubjects.forEach(s => {
      totalAttended += s.adjAttended;
      totalConducted += s.adjConducted;
      totalRemaining += s.remainingClasses;
      if (s.isIrreversible) irreversibleCount++;
    });
    const pct = totalConducted > 0 ? (totalAttended / totalConducted) * 100 : 0;
    return { totalAttended, totalConducted, totalRemaining, pct, irreversibleCount };
  }, [evaluatedSubjects]);

  const handleChatSubmit = (e) => {
    e.preventDefault();
    if (!chatInput.trim()) return;

    const userQuery = chatInput;
    setChatMessages(prev => [...prev, { sender: 'user', text: userQuery }]);
    setChatInput('');

    setTimeout(() => {
      let botResponse = "";
      const queryLower = userQuery.toLowerCase();

      if (queryLower.includes('sick') || queryLower.includes('leave') || queryLower.includes('absent')) {
        const numbers = queryLower.match(/\d+/);
        const days = numbers ? parseInt(numbers[0]) : 3;

        const lowestSub = [...evaluatedSubjects].sort((a, b) => a.currentPct - b.currentPct)[0];
        const projectedPct = (lowestSub.adjAttended / (lowestSub.adjConducted + days)) * 100;

        if (projectedPct < 75) {
          botResponse = `⚠️ Danger Alert! If you take a ${days}-day leave, your ${lowestSub.name} attendance will drop to ${projectedPct.toFixed(1)}%, which falls BELOW the mandatory 75% threshold!`;
        } else {
          botResponse = `✅ You're safe! Taking a ${days}-day leave will lower your lowest subject (${lowestSub.name}) to ${projectedPct.toFixed(1)}%, which remains above the 75% detention threshold.`;
        }
      } else if (queryLower.includes('90') || queryLower.includes('top')) {
        botResponse = `To maintain overall attendance above 90%, you must attend at least ${evaluatedSubjects.reduce((acc, curr) => acc + curr.req90, 0)} total classes across your subjects before the semester ends on Nov 29, 2026.`;
      } else {
        botResponse = `Your overall attendance currently stands at ${overallStats.pct.toFixed(1)}%. You have ${overallStats.totalRemaining} total remaining classes this semester. Let me know if you want to simulate any leaves or ODs!`;
      }

      setChatMessages(prev => [...prev, { sender: 'bot', text: botResponse }]);
    }, 500);
  };

  return (
    <div className="min-h-screen bg-slate-950 text-slate-100 font-sans p-4 md:p-8">
      <div className="max-w-7xl mx-auto space-y-6">

        {/* HEADER SECTION */}
        <header className="flex flex-col lg:flex-row justify-between items-start lg:items-center gap-4 border-b border-slate-800 pb-5">
          <div>
            <div className="flex items-center gap-2 text-indigo-400 text-xs font-bold uppercase tracking-widest">
              <Sparkles className="w-4 h-4" /> SRM IST Tiruchirappalli Campus
            </div>
            <h1 className="text-3xl font-extrabold text-white mt-1">Smart Attendance Predictor</h1>
            <p className="text-xs text-slate-400 mt-1">
              Semester Timeline: <span className="text-slate-200 font-semibold">29th Aug 2026 – 29th Nov 2026</span>
            </p>
          </div>

          <div className="flex flex-wrap items-center gap-3">
            <div className="bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 flex items-center gap-2">
              <span className="text-xs text-slate-400 font-medium">Section:</span>
              <select 
                value={selectedSection} 
                onChange={(e) => handleSectionChange(e.target.value)}
                className="bg-transparent text-sm font-bold text-white focus:outline-none cursor-pointer"
              >
                {Object.keys(SECTION_TIMETABLES).map(sec => (
                  <option key={sec} value={sec} className="bg-slate-900 text-white">{sec}</option>
                ))}
              </select>
            </div>

            <div className="bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 flex items-center gap-2">
              <Calendar className="w-4 h-4 text-indigo-400" />
              <span className="text-xs text-slate-400 font-medium">Planning Target:</span>
              <input 
                type="date" 
                value={targetDate} 
                min={todayFormatted}
                max="2026-11-29"
                onChange={(e) => setTargetDate(e.target.value)}
                className="bg-transparent text-sm font-bold text-white focus:outline-none"
              />
            </div>
          </div>
        </header>

        {/* IRREVERSIBLE DETENTION BANNER */}
        {overallStats.irreversibleCount > 0 && (
          <div className="bg-rose-950/90 border-2 border-rose-500 rounded-xl p-5 flex items-center gap-4 text-rose-100 shadow-2xl animate-pulse">
            <ShieldAlert className="w-10 h-10 text-rose-400 flex-shrink-0" />
            <div>
              <h2 className="font-extrabold text-rose-100 text-lg uppercase tracking-wider">
                🚨 Irreversible Detention Alert!
              </h2>
              <p className="text-xs text-rose-200 mt-1 leading-relaxed">
                Your attendance in <strong className="underline">{overallStats.irreversibleCount} subject(s)</strong> is mathematically impossible to reach 75% before November 29, 2026, even if you attend 100% of all remaining classes. Please contact your department head immediately.
              </p>
            </div>
          </div>
        )}

        {/* HEALTH & LEAVE SIMULATOR */}
        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
          
          <div className="bg-slate-900/80 border border-slate-800 rounded-xl p-5 space-y-4">
            <div className="flex justify-between items-center">
              <span className="text-xs font-bold text-slate-400 uppercase tracking-wider">Overall Semester Health</span>
              <span className={`px-2.5 py-1 rounded-full text-xs font-bold border ${
                overallStats.pct >= 90 ? 'bg-emerald-500/10 border-emerald-500/30 text-emerald-400' :
                overallStats.pct >= 75 ? 'bg-amber-500/10 border-amber-500/30 text-amber-400' :
                'bg-rose-500/10 border-rose-500/30 text-rose-400'
              }`}>
                {overallStats.pct >= 90 ? 'Safe Zone' : overallStats.pct >= 75 ? 'Warning Zone' : 'Detention Risk'}
              </span>
            </div>

            <div className="flex items-baseline gap-2">
              <span className="text-4xl font-extrabold text-white">{overallStats.pct.toFixed(1)}%</span>
              <span className="text-xs text-slate-400">({overallStats.totalAttended} / {overallStats.totalConducted} classes)</span>
            </div>

            <div className="w-full bg-slate-800 h-2.5 rounded-full overflow-hidden">
              <div 
                className={`h-full transition-all duration-500 ${overallStats.pct >= 75 ? 'bg-emerald-500' : 'bg-rose-500'}`}
                style={{ width: `${Math.min(100, overallStats.pct)}%` }}
              />
            </div>

            <p className="text-xs text-slate-400 pt-1">
              Total remaining scheduled classes in semester: <strong className="text-slate-200">{overallStats.totalRemaining}</strong>
            </p>
          </div>

          <div className="lg:col-span-2 bg-slate-900/80 border border-slate-800 rounded-xl p-5 space-y-4">
            <h2 className="text-sm font-bold text-indigo-400 uppercase tracking-wider flex items-center gap-2">
              <UserCheck className="w-4 h-4" /> On-Duty (OD) & Medical Leave Simulator
            </h2>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div className="bg-slate-950 p-4 rounded-xl border border-slate-800 space-y-2">
                <div className="flex justify-between text-xs font-semibold text-slate-300">
                  <span>Approved On-Duty (OD) Classes</span>
                  <span className="text-emerald-400 font-bold">+{odClasses} Classes</span>
                </div>
                <input 
                  type="number" 
                  min="0" 
                  max="20"
                  value={odClasses} 
                  onChange={(e) => setOdClasses(Number(e.target.value))}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 text-sm font-bold text-white focus:outline-none"
                />
                <p className="text-[11px] text-slate-500">OD counts as attended classes without penalty.</p>
              </div>

              <div className="bg-slate-950 p-4 rounded-xl border border-slate-800 space-y-2">
                <div className="flex justify-between text-xs font-semibold text-slate-300">
                  <span>Medical / Sick Leave Days</span>
                  <span className="text-rose-400 font-bold">+{medicalLeaves} Days</span>
                </div>
                <input 
                  type="number" 
                  min="0" 
                  max="20"
                  value={medicalLeaves} 
                  onChange={(e) => setMedicalLeaves(Number(e.target.value))}
                  className="w-full bg-slate-900 border border-slate-700 rounded-lg px-3 py-1.5 text-sm font-bold text-white focus:outline-none"
                />
                <p className="text-[11px] text-slate-500">Recalculates impact on total conducted classes.</p>
              </div>
            </div>
          </div>

        </div>

        {/* SUBJECT TABLE */}
        <div className="bg-slate-900/80 border border-slate-800 rounded-xl p-6 space-y-4">
          <div className="flex justify-between items-center border-b border-slate-800 pb-3">
            <h2 className="text-base font-bold text-white flex items-center gap-2">
              <BarChart3 className="w-5 h-5 text-indigo-400" /> Subject Attendance Requirements
            </h2>
            <span className="text-xs text-slate-400">Interactive Inputs: Update attended/conducted below</span>
          </div>

          <div className="overflow-x-auto">
            <table className="w-full text-left text-xs text-slate-300">
              <thead className="bg-slate-950 text-slate-400 uppercase font-semibold border-b border-slate-800">
                <tr>
                  <th className="p-3">Course Code & Name</th>
                  <th className="p-3">Attended</th>
                  <th className="p-3">Conducted</th>
                  <th className="p-3">Current %</th>
                  <th className="p-3">Remaining</th>
                  <th className="p-3">Req. for 75%</th>
                  <th className="p-3">Req. for 90%</th>
                  <th className="p-3">Status</th>
                </tr>
              </thead>
              <tbody className="divide-y divide-slate-800">
                {evaluatedSubjects.map((sub, idx) => (
                  <tr key={sub.code} className="hover:bg-slate-800/40 transition-colors">
                    <td className="p-3 font-medium text-white">
                      <span className="text-indigo-400 font-mono text-[11px] block">{sub.code}</span>
                      {sub.name}
                    </td>
                    <td className="p-3">
                      <input 
                        type="number" 
                        value={sub.defaultAttended} 
                        onChange={(e) => handleSubjectInputChange(idx, 'defaultAttended', e.target.value)}
                        className="w-14 bg-slate-950 border border-slate-700 rounded px-2 py-1 text-center font-bold text-white"
                      />
                    </td>
                    <td className="p-3">
                      <input 
                        type="number" 
                        value={sub.defaultConducted} 
                        onChange={(e) => handleSubjectInputChange(idx, 'defaultConducted', e.target.value)}
                        className="w-14 bg-slate-950 border border-slate-700 rounded px-2 py-1 text-ce# attendance-predictor
