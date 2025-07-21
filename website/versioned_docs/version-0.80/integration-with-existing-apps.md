import { useState, useEffect, useRef } from 'react';

export default function App() {
  // Estados principales
  const [activeTab, setActiveTab] = useState('activities');
  const [theme, setTheme] = useState('vibrant');
  const [activities, setActivities] = useState([
    { id: 1, name: 'Desayunar', icon: '🍳', time: '08:00', completed: false },
    { id: 2, name: 'Cepillarse', icon: '🦷', time: '08:30', completed: false },
    { id: 3, name: 'Ir al colegio', icon: '🎒', time: '09:00', completed: false },
    { id: 4, name: 'Jugar', icon: '🎮', time: '12:00', completed: false },
    { id: 5, name: 'Dormir', icon: '😴', time: '21:00', completed: false },
  ]);
  const [draggedItem, setDraggedItem] = useState(null);
  const [showTimer, setShowTimer] = useState(false);
  const [timerDuration, setTimerDuration] = useState(5);
  const [timeLeft, setTimeLeft] = useState(0);
  const [timerRunning, setTimerRunning] = useState(false);
  const [language, setLanguage] = useState('es');
  const [rewards, setRewards] = useState(0);
  const [currentProfile, setCurrentProfile] = useState('default');

  // Traducciones
  const translations = {
    es: {
      title: "Emoti & Más",
      subtitle: "Apoyo emocional, sensorial y rutinas para personas autistas",
      mode: "Modo",
      calm: "Calmado",
      vibrant: "Vibrante",
      activities: "Inicio de Actividades",
      routines: "Rutinas",
      caa: "CAA",
      sensory: "Sensorial",
      rewards: "Recompensas",
      notes: "Notas",
      music: "Música",
      addActivity: "+ Añadir Actividad",
      clearHistory: "Borrar historial",
      star: "⭐",
      startTimer: "Iniciar Temporizador",
      minutes: "minutos",
      completed: "Completado",
      pending: "Pendiente",
      rewardsSystem: "Sistema de Recompensas",
      todayRoutine: "Rutina de hoy",
      activityName: "Nombre de la actividad",
      activityTime: "Hora",
      activityIcon: "Icono",
      save: "Guardar",
      cancel: "Cancelar",
      profile: "Perfil",
      default: "Predeterminado",
      kid: "Niño",
      teen: "Adolescente",
      adult: "Adulto",
    },
    en: {
      title: "Emoti & More",
      subtitle: "Emotional, sensory and routine support for autistic people",
      mode: "Mode",
      calm: "Calm",
      vibrant: "Vibrant",
      activities: "Start Activities",
      routines: "Routines",
      caa: "AAC",
      sensory: "Sensory",
      rewards: "Rewards",
      notes: "Notes",
      music: "Music",
      addActivity: "+ Add Activity",
      clearHistory: "Clear history",
      star: "⭐",
      startTimer: "Start Timer",
      minutes: "minutes",
      completed: "Completed",
      pending: "Pending",
      rewardsSystem: "Reward System",
      todayRoutine: "Today's Routine",
      activityName: "Activity Name",
      activityTime: "Time",
      activityIcon: "Icon",
      save: "Save",
      cancel: "Cancel",
      profile: "Profile",
      default: "Default",
      kid: "Kid",
      teen: "Teen",
      adult: "Adult",
    },
    zh: {
      title: "Emoti & 更多",
      subtitle: "为自闭症人士提供情绪、感官和日常支持",
      mode: "模式",
      calm: "平静",
      vibrant: "活力",
      activities: "开始活动",
      routines: "日常",
      caa: "AAC",
      sensory: "感官",
      rewards: "奖励",
      notes: "笔记",
      music: "音乐",
      addActivity: "+ 添加活动",
      clearHistory: "清除历史",
      star: "⭐",
      startTimer: "开始计时器",
      minutes: "分钟",
      completed: "已完成",
      pending: "待处理",
      rewardsSystem: "奖励系统",
      todayRoutine: "今日计划",
      activityName: "活动名称",
      activityTime: "时间",
      activityIcon: "图标",
      save: "保存",
      cancel: "取消",
      profile: "档案",
      default: "默认",
      kid: "儿童",
      teen: "青少年",
      adult: "成人",
    },
  };

  // Cargar datos desde localStorage
  useEffect(() => {
    const savedTheme = localStorage.getItem('theme');
    const savedActivities = localStorage.getItem('activities');
    const savedRewards = localStorage.getItem('rewards');
    const savedLanguage = localStorage.getItem('language');
    const savedProfile = localStorage.getItem('currentProfile');

    if (savedTheme) setTheme(savedTheme);
    if (savedActivities) setActivities(JSON.parse(savedActivities));
    if (savedRewards) setRewards(parseInt(savedRewards));
    if (savedLanguage) setLanguage(savedLanguage);
    if (savedProfile) setCurrentProfile(savedProfile);
  }, []);

  // Guardar datos en localStorage
  useEffect(() => {
    localStorage.setItem('theme', theme);
    localStorage.setItem('activities', JSON.stringify(activities));
    localStorage.setItem('rewards', rewards);
    localStorage.setItem('language', language);
    localStorage.setItem('currentProfile', currentProfile);
  }, [theme, activities, rewards, language, currentProfile]);

  const t = translations[language];

  // Funciones para actividades
  const toggleActivity = (id) => {
    setActivities(prev =>
      prev.map(a =>
        a.id === id ? { ...a, completed: !a.completed } : a
      )
    );
  };

  const addActivity = () => {
    const name = prompt(t.activityName);
    const time = prompt(t.activityTime);
    const icon = prompt(t.activityIcon + " (ej: 🎮)");
    if (name && time && icon) {
      setActivities(prev => [...prev, { 
        id: Date.now(), 
        name, 
        time, 
        icon, 
        completed: false 
      }]);
    }
  };

  const removeActivity = (id) => {
    setActivities(prev => prev.filter(a => a.id !== id));
  };

  // Funciones para el temporizador
  const startTimer = () => {
    setTimeLeft(timerDuration * 60);
    setTimerRunning(true);
    setShowTimer(true);
  };

  const stopTimer = () => {
    setTimerRunning(false);
    setShowTimer(false);
  };

  const completeAllActivities = () => {
    setActivities(prev => prev.map(a => ({ ...a, completed: true })));
    setRewards(prev => prev + 1);
  };

  // Manejo de drag & drop
  const handleDragStart = (e, item) => {
    setDraggedItem(item);
  };

  const handleDragOver = (e, targetId) => {
    e.preventDefault();
    if (!draggedItem) return;
    
    setActivities(prev => {
      const draggedIndex = prev.findIndex(item => item.id === draggedItem.id);
      const targetIndex = prev.findIndex(item => item.id === targetId);
      const newActivities = [...prev];
      const [movedItem] = newActivities.splice(draggedIndex, 1);
      newActivities.splice(targetIndex, 0, movedItem);
      return newActivities;
    });
  };

  // Temporizador
  useEffect(() => {
    let interval = null;
    if (timerRunning && timeLeft > 0) {
      interval = setInterval(() => {
        setTimeLeft(prev => prev - 1);
      }, 1000);
    } else if (timeLeft === 0 && timerRunning) {
      setTimerRunning(false);
      const star = document.createElement('div');
      star.className = 'fixed top-4 right-4 text-yellow-400 text-2xl animate-bounce';
      star.innerText = '⭐';
      document.body.appendChild(star);
      setTimeout(() => star.remove(), 1000);
    }
    return () => clearInterval(interval);
  }, [timerRunning, timeLeft]);

  // Perfiles personalizados
  const profileColors = {
    default: 'from-pink-500 to-purple-500',
    kid: 'from-blue-400 to-teal-400',
    teen: 'from-green-400 to-lime-500',
    adult: 'from-amber-400 to-rose-500',
  };

  const profileIcons = {
    default: '🧠',
    kid: '👶',
    teen: '👦',
    adult: '🧑',
  };

  return (
    <div className={`min-h-screen ${theme === 'vibrant' ? 'bg-gradient-to-br from-pink-200 via-purple-200 to-indigo-200 text-gray-900' : 'bg-gray-100 text-gray-800'} transition-colors duration-300 p-4 font-sans`}>
      <header className="text-center py-4">
        <div className="flex justify-between items-center">
          <h1 className="text-3xl font-bold">{t.title}</h1>
          <div className="flex items-center space-x-2">
            <select 
              value={language} 
              onChange={(e) => setLanguage(e.target.value)}
              className="p-1 rounded text-sm"
            >
              <option value="es">Español</option>
              <option value="en">English</option>
              <option value="zh">中文</option>
            </select>
            <select 
              value={currentProfile} 
              onChange={(e) => setCurrentProfile(e.target.value)}
              className="p-1 rounded text-sm"
            >
              <option value="default">{t.default}</option>
              <option value="kid">{t.kid}</option>
              <option value="teen">{t.teen}</option>
              <option value="adult">{t.adult}</option>
            </select>
            <button
              onClick={() => setTheme(theme === 'vibrant' ? 'calm' : 'vibrant')}
              className={`px-3 py-1 rounded-full text-xs ${theme === 'vibrant' ? 'bg-white/30 text-white' : 'bg-gray-200 text-gray-700'}`}
            >
              {theme === 'vibrant' ? t.calm : t.vibrant}
            </button>
          </div>
        </div>
        <p className="text-sm mt-1">{t.subtitle}</p>
      </header>

      <nav className="flex flex-wrap justify-center gap-2 mb-6">
        <button
          onClick={() => setActiveTab('activities')}
          className={`px-4 py-2 rounded ${activeTab === 'activities' ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-white/50 backdrop-blur-sm shadow'}`}
        >
          {t.activities}
        </button>
        <button
          onClick={() => setActiveTab('routines')}
          className={`px-4 py-2 rounded ${activeTab === 'routines' ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-white/50 backdrop-blur-sm shadow'}`}
        >
          {t.routines}
        </button>
        <button
          onClick={() => setActiveTab('sensory')}
          className={`px-4 py-2 rounded ${activeTab === 'sensory' ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-white/50 backdrop-blur-sm shadow'}`}
        >
          {t.sensory}
        </button>
        <button
          onClick={() => setActiveTab('rewards')}
          className={`px-4 py-2 rounded ${activeTab === 'rewards' ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-white/50 backdrop-blur-sm shadow'}`}
        >
          {t.rewards}
        </button>
        <button
          onClick={() => setActiveTab('music')}
          className={`px-4 py-2 rounded ${activeTab === 'music' ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-white/50 backdrop-blur-sm shadow'}`}
        >
          {t.music}
        </button>
      </nav>

      <main className="max-w-3xl mx-auto">
        {activeTab === 'activities' && (
          <section className={`p-4 rounded-lg shadow-md ${theme === 'vibrant' ? 'bg-white/80 backdrop-blur-sm' : 'bg-white'}`}>
            <div className="flex justify-between items-center mb-4">
              <h2 className="text-xl font-semibold">{t.todayRoutine}</h2>
              <button
                onClick={addActivity}
                className={`px-3 py-1 bg-gradient-to-r ${profileColors[currentProfile]} text-white rounded`}
              >
                {t.addActivity}
              </button>
            </div>
            
            {/* Temporizador */}
            <div className="mb-6">
              <div className="flex items-center space-x-2">
                <input
                  type="number"
                  min="1"
                  max="60"
                  value={timerDuration}
                  onChange={(e) => setTimerDuration(parseInt(e.target.value))}
                  className="w-16 p-1 rounded text-center"
                />
                <span>{t.minutes}</span>
                <button
                  onClick={startTimer}
                  className={`px-3 py-1 bg-gradient-to-r ${profileColors[currentProfile]} text-white rounded`}
                >
                  {t.startTimer}
                </button>
              </div>
              
              {showTimer && (
                <div className="mt-4 text-center">
                  <div className={`text-5xl font-mono ${timeLeft < 60 ? 'text-red-500 animate-pulse' : 'text-blue-500'}`}>
                    {Math.floor(timeLeft / 60)}:{String(timeLeft % 60).padStart(2, '0')}
                  </div>
                  <div className="mt-2">
                    {timerRunning ? (
                      <button 
                        onClick={stopTimer}
                        className="px-3 py-1 bg-red-500 text-white rounded"
                      >
                        🛑 {t.cancel}
                      </button>
                    ) : (
                      <button 
                        onClick={() => setShowTimer(false)}
                        className="px-3 py-1 bg-green-500 text-white rounded"
                      >
                        ✔️ {t.save}
                      </button>
                    )}
                  </div>
                </div>
              )}
            </div>

            {/* Lista de actividades */}
            <ul className="space-y-3">
              {activities.map(activity => (
                <li
                  key={activity.id}
                  draggable
                  onDragStart={(e) => handleDragStart(e, activity)}
                  onDragOver={(e) => handleDragOver(e, activity.id)}
                  className={`flex items-center justify-between p-3 rounded shadow-sm ${
                    theme === 'vibrant' ? 'bg-white/60' : 'bg-gray-100'
                  } ${activity.completed ? 'opacity-60' : ''}`}
                >
                  <div className="flex items-center">
                    <div className="text-3xl mr-3">{activity.icon}</div>
                    <div>
                      <div className="font-medium">{activity.name}</div>
                      <div className="text-sm text-gray-500">{activity.time}</div>
                    </div>
                  </div>
                  <div className="flex items-center space-x-2">
                    <span className={`text-sm ${activity.completed ? 'text-green-500' : 'text-gray-500'}`}>
                      {activity.completed ? t.completed : t.pending}
                    </span>
                    <button
                      onClick={() => toggleActivity(activity.id)}
                      className={`w-6 h-6 rounded-full flex items-center justify-center border-2 ${
                        activity.completed ? 'bg-green-500 border-green-500' : 'border-gray-400'
                      }`}
                    >
                      {activity.completed && (
                        <svg className="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M5 13l4 4L19 7" />
                        </svg>
                      )}
                    </button>
                    <button
                      onClick={() => removeActivity(activity.id)}
                      className="text-red-500 hover:text-red-700"
                    >
                      🗑️
                    </button>
                  </div>
                </li>
              ))}
            </ul>

            {/* Botón de completar todo */}
            <div className="mt-6">
              <button
                onClick={completeAllActivities}
                className={`w-full py-2 rounded-lg font-bold flex items-center justify-center ${
                  profileColors[currentProfile] ? `bg-gradient-to-r ${profileColors[currentProfile]} text-white` : 'bg-blue-500 text-white'
                }`}
              >
                <span className="mr-2">🎉</span> {t.rewardsSystem}
              </button>
            </div>
          </section>
        )}

        {activeTab === 'rewards' && (
          <section className={`p-4 rounded-lg shadow-md ${theme === 'vibrant' ? 'bg-white/80 backdrop-blur-sm' : 'bg-white'}`}>
            <h2 className="text-xl font-semibold mb-4">{t.rewardsSystem}</h2>
            <div className="flex justify-between items-center">
              <button 
                onClick={() => setRewards(prev => (prev > 0 ? prev - 1 : 0))}
                className="text-2xl">-</button>
              <div className="text-4xl">{t.star} {rewards}</div>
              <button 
                onClick={() => setRewards(prev => prev + 1)}
                className="text-2xl">+</button>
            </div>
          </section>
        )}
      </main>

      <footer className="text-center text-sm mt-8 text-gray-600">
        {profileIcons[currentProfile]} {t.profile}: {t[currentProfile]} • {t.rewardsSystem}: {rewards} {t.star} • © 2025
      </footer>
    </div>
  );
}
