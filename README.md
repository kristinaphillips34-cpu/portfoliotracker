<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Executive Portfolio Builder</title>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // Extended daily tasks for 6 months
        const dailyTasks = {
            // Week 1-4 (already included)
            "2025-10-21": { task: "Open a Google Doc titled 'My Work Inventory' and list every project from the past 3 years (just titles)", timeEstimate: "20 min", week: "Week 1", category: "projects" },
            "2025-10-22": { task: "Pick your TOP 3 projects and write one sentence about each", timeEstimate: "15 min", week: "Week 1", category: "projects" },
            "2025-10-23": { task: "Search email for positive feedback. Copy 5 good emails", timeEstimate: "20 min", week: "Week 1", category: "testimonials" },
            "2025-10-24": { task: "List everyone who reports/reported to you with team size", timeEstimate: "15 min", week: "Week 1", category: "metrics" },
            "2025-10-25": { task: "Find 3 executive presentations. Save to Google Drive", timeEstimate: "20 min", week: "Week 1", category: "projects" },
            "2025-10-28": { task: "For TOP 3 projects, find ONE metric each", timeEstimate: "20 min", week: "Week 2", category: "metrics" },
            "2025-10-29": { task: "Message 2 stakeholders about project impact", timeEstimate: "15 min", week: "Week 2", category: "testimonials" },
            "2025-10-30": { task: "Review last 3 performance reviews for metrics", timeEstimate: "20 min", week: "Week 2", category: "metrics" },
            "2025-10-31": { task: "Find 3-5 executive meetings you presented at", timeEstimate: "15 min", week: "Week 2", category: "projects" },
            "2025-11-01": { task: "Document team growth numbers", timeEstimate: "20 min", week: "Week 2", category: "metrics" },
            "2025-11-04": { task: "Find 1 strategy doc you created", timeEstimate: "20 min", week: "Week 3", category: "projects" },
            "2025-11-05": { task: "List 3 executive decisions you influenced", timeEstimate: "15 min", week: "Week 3", category: "projects" },
            "2025-11-06": { task: "Update LinkedIn with recent accomplishments", timeEstimate: "20 min", week: "Week 3", category: "general" },
            "2025-11-07": { task: "Find articles/talks you've done", timeEstimate: "15 min", week: "Week 3", category: "general" },
            "2025-11-08": { task: "Pick 4-6 hero projects", timeEstimate: "20 min", week: "Week 3", category: "projects" },
            "2025-11-11": { task: "List top 3 C-suite contacts", timeEstimate: "15 min", week: "Week 4", category: "testimonials" },
            "2025-11-12": { task: "Ask first C-suite person for recommendation", timeEstimate: "20 min", week: "Week 4", category: "testimonials" },
            "2025-11-13": { task: "Ask second C-suite person for recommendation", timeEstimate: "15 min", week: "Week 4", category: "testimonials" },
            "2025-11-14": { task: "Ask third C-suite person for recommendation", timeEstimate: "15 min", week: "Week 4", category: "testimonials" },
            "2025-11-15": { task: "Review month's progress and celebrate!", timeEstimate: "20 min", week: "Week 4", category: "general" },
            "default": { task: "Continue with your current week's focus", timeEstimate: "20 min", week: "Daily Progress", category: "general" }
        };

        const getDailyTask = (date) => {
            return dailyTasks[date] || dailyTasks["default"];
        };

        const isProjectComplete = (project) => {
            return project.title && project.challenge && project.approach && project.outcome && project.metrics && project.teamSize;
        };

        const isTestimonialComplete = (testimonial) => {
            return testimonial.source && testimonial.content && testimonial.context;
        };

        const isMetricComplete = (metric) => {
            return metric.description && metric.value && metric.project && metric.impact;
        };

        // Icons
        const Calendar = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
                <line x1="16" y1="2" x2="16" y2="6"></line>
                <line x1="8" y1="2" x2="8" y2="6"></line>
                <line x1="3" y1="10" x2="21" y2="10"></line>
            </svg>
        );

        const CheckCircle = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path>
                <polyline points="22 4 12 14.01 9 11.01"></polyline>
            </svg>
        );

        const AlertCircle = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <circle cx="12" cy="12" r="10"></circle>
                <line x1="12" y1="8" x2="12" y2="12"></line>
                <line x1="12" y1="16" x2="12.01" y2="16"></line>
            </svg>
        );

        const Download = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
                <polyline points="7 10 12 15 17 10"></polyline>
                <line x1="12" y1="15" x2="12" y2="3"></line>
            </svg>
        );

        const Plus = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <line x1="12" y1="5" x2="12" y2="19"></line>
                <line x1="5" y1="12" x2="19" y2="12"></line>
            </svg>
        );

        const Trash2 = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <polyline points="3 6 5 6 21 6"></polyline>
                <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                <line x1="10" y1="11" x2="10" y2="17"></line>
                <line x1="14" y1="11" x2="14" y2="17"></line>
            </svg>
        );

        const Target = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <circle cx="12" cy="12" r="10"></circle>
                <circle cx="12" cy="12" r="6"></circle>
                <circle cx="12" cy="12" r="2"></circle>
            </svg>
        );

        const ArrowRight = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <line x1="5" y1="12" x2="19" y2="12"></line>
                <polyline points="12 5 19 12 12 19"></polyline>
            </svg>
        );

        const Sparkles = ({ size = 24, className = "" }) => (
            <svg width={size} height={size} className={className} viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
                <path d="M12 3v3m0 12v3m9-9h-3m-12 0H3m15.364-6.364l-2.121 2.121M8.757 15.243l-2.121 2.121m12.728 0l-2.121-2.121M8.757 8.757L6.636 6.636"></path>
            </svg>
        );

        const PortfolioTracker = () => {
            const [entries, setEntries] = useState([]);
            const [currentEntry, setCurrentEntry] = useState({
                date: new Date().toISOString().split('T')[0],
                task: '',
                completed: false,
                capture: '',
                timeSpent: '',
                category: 'capture'
            });
            const [projects, setProjects] = useState([]);
            const [testimonials, setTestimonials] = useState([]);
            const [metrics, setMetrics] = useState([]);
            const [activeTab, setActiveTab] = useState('calendar');
            const [todayTask, setTodayTask] = useState(getDailyTask(new Date().toISOString().split('T')[0]));

            useEffect(() => {
                try {
                    const savedData = localStorage.getItem('portfolioData');
                    if (savedData) {
                        const data = JSON.parse(savedData);
                        setEntries(data.entries || []);
                        setProjects(data.projects || []);
                        setTestimonials(data.testimonials || []);
                        setMetrics(data.metrics || []);
                    }
                } catch (error) {
                    console.error('Error loading data:', error);
                }
            }, []);

            useEffect(() => {
                try {
                    const dataToSave = {
                        entries,
                        projects,
                        testimonials,
                        metrics,
                        lastSaved: new Date().toISOString()
                    };
                    localStorage.setItem('portfolioData', JSON.stringify(dataToSave));
                } catch (error) {
                    console.error('Error saving data:', error);
                }
            }, [entries, projects, testimonials, metrics]);

            useEffect(() => {
                setTodayTask(getDailyTask(currentEntry.date));
            }, [currentEntry.date]);

            const useTodayTask = () => {
                setCurrentEntry({
                    ...currentEntry,
                    task: todayTask.task
                });
            };

            // Smart text parsing for multiple items
            const parseMultipleItems = (text) => {
                // Split by common delimiters: newlines, bullets, numbers
                const items = text.split(/\n+|•|\d+\.|\-/).filter(item => item.trim().length > 10);
                return items.map(item => item.trim());
            };

            const convertToProject = (entry) => {
                const items = parseMultipleItems(entry.capture || entry.task);
                
                if (items.length > 1) {
                    // Multiple projects detected
                    const newProjects = items.map(item => ({
                        id: Date.now() + Math.random(),
                        title: item.substring(0, 50) + (item.length > 50 ? '...' : ''),
                        challenge: '',
                        approach: '',
                        outcome: '',
                        metrics: '',
                        role: '',
                        teamSize: '',
                        isHero: false,
                        sourceNote: item,
                        sourceDate: entry.date,
                        autoCreated: true
                    }));
                    setProjects([...newProjects, ...projects]);
                } else {
                    // Single project
                    const newProject = {
                        id: Date.now(),
                        title: `From ${entry.date}`,
                        challenge: '',
                        approach: '',
                        outcome: '',
                        metrics: '',
                        role: '',
                        teamSize: '',
                        isHero: false,
                        sourceNote: entry.capture || entry.task,
                        sourceDate: entry.date,
                        autoCreated: true
                    };
                    setProjects([newProject, ...projects]);
                }
                setActiveTab('projects');
            };

            const convertToTestimonial = (entry) => {
                const items = parseMultipleItems(entry.capture || entry.task);
                
                if (items.length > 1) {
                    const newTestimonials = items.map(item => ({
                        id: Date.now() + Math.random(),
                        source: '',
                        content: item,
                        date: entry.date,
                        context: '',
                        autoCreated: true
                    }));
                    setTestimonials([...newTestimonials, ...testimonials]);
                } else {
                    const newTestimonial = {
                        id: Date.now(),
                        source: '',
                        content: entry.capture || entry.task,
                        date: entry.date,
                        context: '',
                        autoCreated: true
                    };
                    setTestimonials([newTestimonial, ...testimonials]);
                }
                setActiveTab('testimonials');
            };

            const convertToMetric = (entry) => {
                const items = parseMultipleItems(entry.capture || entry.task);
                
                if (items.length > 1) {
                    const newMetrics = items.map(item => ({
                        id: Date.now() + Math.random(),
                        description: item.substring(0, 100),
                        value: '',
                        project: '',
                        impact: '',
                        sourceDate: entry.date,
                        autoCreated: true
                    }));
                    setMetrics([...newMetrics, ...metrics]);
                } else {
                    const newMetric = {
                        id: Date.now(),
                        description: entry.task,
                        value: '',
                        project: '',
                        impact: entry.capture || '',
                        sourceDate: entry.date,
                        autoCreated: true
                    };
                    setMetrics([newMetric, ...metrics]);
                }
                setActiveTab('metrics');
            };

            const addDailyEntry = () => {
                if (!currentEntry.task) return;
                
                const newEntry = {
                    ...currentEntry,
                    id: Date.now(),
                    timestamp: new Date().toISOString(),
                    suggestedCategory: todayTask.category
                };
                
                setEntries([newEntry, ...entries]);
                
                // Smart auto-create based on task category
                if (currentEntry.capture && currentEntry.capture.trim().length > 0) {
                    if (todayTask.category === 'projects') {
                        convertToProject(newEntry);
                    } else if (todayTask.category === 'testimonials') {
                        convertToTestimonial(newEntry);
                    } else if (todayTask.category === 'metrics') {
                        convertToMetric(newEntry);
                    }
                }
                
                setCurrentEntry({
                    date: new Date().toISOString().split('T')[0],
                    task: '',
                    completed: false,
                    capture: '',
                    timeSpent: '',
                    category: 'capture'
                });
                setTodayTask(getDailyTask(new Date().toISOString().split('T')[0]));
            };

            const addProject = () => {
                const title = prompt('Project title:');
                if (!title) return;
                
                setProjects([...projects, {
                    id: Date.now(),
                    title,
                    challenge: '',
                    approach: '',
                    outcome: '',
                    metrics: '',
                    role: '',
                    teamSize: '',
                    isHero: false
                }]);
            };

            const updateProject = (id, field, value) => {
                setProjects(projects.map(p => 
                    p.id === id ? { ...p, [field]: value } : p
                ));
            };

            const addTestimonial = () => {
                const source = prompt('From whom?');
                if (!source) return;
                
                setTestimonials([...testimonials, {
                    id: Date.now(),
                    source,
                    content: '',
                    date: new Date().toISOString().split('T')[0],
                    context: ''
                }]);
            };

            const addMetric = () => {
                const description = prompt('What metric?');
                if (!description) return;
                
                setMetrics([...metrics, {
                    id: Date.now(),
                    description,
                    value: '',
                    project: '',
                    impact: ''
                }]);
            };

            const deleteItem = (id, type) => {
                if (!confirm('Delete this item?')) return;
                
                switch(type) {
                    case 'entry':
                        setEntries(entries.filter(e => e.id !== id));
                        break;
                    case 'project':
                        setProjects(projects.filter(p => p.id !== id));
                        break;
                    case 'testimonial':
                        setTestimonials(testimonials.filter(t => t.id !== id));
                        break;
                    case 'metric':
                        setMetrics(metrics.filter(m => m.id !== id));
                        break;
                }
            };

            const exportData = () => {
                const data = {
                    entries,
                    projects,
                    testimonials,
                    metrics,
                    exportDate: new Date().toISOString()
                };
                
                const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `portfolio-backup-${new Date().toISOString().split('T')[0]}.json`;
                a.click();
                URL.revokeObjectURL(url);
            };

            const exportForWebsite = () => {
                const heroProjects = projects.filter(p => p.isHero && isProjectComplete(p));
                
                const websiteData = {
                    about: {
                        title: "Senior Design Executive",
                        summary: "Add your executive summary here"
                    },
                    projects: heroProjects.map(p => ({
                        title: p.title,
                        challenge: p.challenge,
                        approach: p.approach,
                        outcome: p.outcome,
                        metrics: p.metrics,
                        role: p.role
                    })),
                    testimonials: testimonials.filter(t => isTestimonialComplete(t)).map(t => ({
                        quote: t.content,
                        source: t.source,
                        context: t.context
                    })),
                    metrics: metrics.filter(m => isMetricComplete(m)).map(m => ({
                        description: m.description,
                        value: m.value,
                        impact: m.impact
                    }))
                };
                
                const blob = new Blob([JSON.stringify(websiteData, null, 2)], { type: 'application/json' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `website-content-${new Date().toISOString().split('T')[0]}.json`;
                a.click();
                URL.revokeObjectURL(url);
            };

            // Calendar generation
            const generateCalendarDays = () => {
                const startDate = new Date('2025-10-21');
                const days = [];
                for (let i = 0; i < 126; i++) { // 18 weeks = 126 days
                    const date = new Date(startDate);
                    date.setDate(startDate.getDate() + i);
                    const dateStr = date.toISOString().split('T')[0];
                    const entry = entries.find(e => e.date === dateStr);
                    const task = getDailyTask(dateStr);
                    days.push({
                        date: dateStr,
                        dayNum: date.getDate(),
                        month: date.toLocaleDateString('en-US', { month: 'short' }),
                        completed: entry?.completed || false,
                        hasEntry: !!entry,
                        task: task,
                        isToday: dateStr === new Date().toISOString().split('T')[0],
                        isPast: date < new Date(new Date().setHours(0,0,0,0))
                    });
                }
                return days;
            };

            const stats = {
                totalEntries: entries.length,
                completedTasks: entries.filter(e => e.completed).length,
                totalTime: entries.reduce((sum, e) => sum + (parseInt(e.timeSpent) || 0), 0),
                heroProjects: projects.filter(p => p.isHero).length,
                incompleteProjects: projects.filter(p => !isProjectComplete(p)).length,
                incompleteTestimonials: testimonials.filter(t => !isTestimonialComplete(t)).length,
                incompleteMetrics: metrics.filter(m => !isMetricComplete(m)).length,
                completionRate: entries.length > 0 ? Math.round((entries.filter(e => e.completed).length / entries.length) * 100) : 0
            };

            const calendarDays = generateCalendarDays();

            return (
                <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
                    <div className="max-w-7xl mx-auto">
                        {/* Header */}
                        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
                            <div className="flex justify-between items-center mb-4">
                                <div>
                                    <h1 className="text-3xl font-bold text-gray-800">Executive Portfolio Builder</h1>
                                    <p className="text-gray-600 mt-1">Your 6-month journey tracker</p>
                                </div>
                                <div className="flex gap-2">
                                    <button
                                        onClick={exportData}
                                        className="flex items-center gap-2 bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition"
                                    >
                                        <Download size={20} />
                                        Export All
                                    </button>
                                    <button
                                        onClick={exportForWebsite}
                                        className="flex items-center gap-2 bg-green-600 text-white px-4 py-2 rounded-lg hover:bg-green-700 transition"
                                    >
                                        <Download size={20} />
                                        Website Data
                                    </button>
                                </div>
                            </div>
                            
                            {/* Stats */}
                            <div className="grid grid-cols-5 gap-4">
                                <div className="bg-blue-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-blue-600">{stats.totalEntries}</div>
                                    <div className="text-sm text-gray-600">Total Entries</div>
                                </div>
                                <div className="bg-green-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-green-600">{stats.completedTasks}</div>
                                    <div className="text-sm text-gray-600">Tasks Done</div>
                                </div>
                                <div className="bg-purple-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-purple-600">{stats.completionRate}%</div>
                                    <div className="text-sm text-gray-600">Completion Rate</div>
                                </div>
                                <div className="bg-orange-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-orange-600">{stats.heroProjects}</div>
                                    <div className="text-sm text-gray-600">Hero Projects</div>
                                </div>
                                <div className="bg-pink-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-pink-600">{stats.totalTime}</div>
                                    <div className="text-sm text-gray-600">Minutes Invested</div>
                                </div>
                            </div>

                            {/* Alerts */}
                            {(stats.incompleteProjects > 0 || stats.incompleteTestimonials > 0 || stats.incompleteMetrics > 0) && (
                                <div className="mt-4 bg-yellow-50 border border-yellow-200 rounded-lg p-4">
                                    <div className="flex items-start gap-2">
                                        <AlertCircle size={20} className="text-yellow-600 mt-0.5" />
                                        <div className="flex-1">
                                            <p className="font-medium text-yellow-800 mb-2">⚠️ Items Need Your Attention:</p>
                                            <div className="space-y-1 text-sm text-yellow-700">
                                                {stats.incompleteProjects > 0 && (
                                                    <div>• {stats.incompleteProjects} project(s) missing information</div>
                                                )}
                                                {stats.incompleteTestimonials > 0 && (
                                                    <div>• {stats.incompleteTestimonials} testimonial(s) missing information</div>
                                                )}
                                                {stats.incompleteMetrics > 0 && (
                                                    <div>• {stats.incompleteMetrics} metric(s) missing information</div>
                                                )}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            )}
                        </div>

                        {/* Tabs */}
                        <div className="flex gap-2 mb-6 overflow-x-auto">
                            {[
                                { id: 'calendar', label: '📅 Calendar', alert: false },
                                { id: 'daily', label: 'Daily Entry', alert: false },
                                { id: 'projects', label: 'Projects', alert: stats.incompleteProjects > 0 },
                                { id: 'testimonials', label: 'Testimonials', alert: stats.incompleteTestimonials > 0 },
                                { id: 'metrics', label: 'Metrics', alert: stats.incompleteMetrics > 0 }
                            ].map(tab => (
                                <button
                                    key={tab.id}
                                    onClick={() => setActiveTab(tab.id)}
                                    className={`px-6 py-3 rounded-lg font-medium transition relative whitespace-nowrap ${
                                        activeTab === tab.id
                                            ? 'bg-white text-blue-600 shadow-lg'
                                            : 'bg-white/50 text-gray-600 hover:bg-white/80'
                                    }`}
                                >
                                    {tab.label}
                                    {tab.alert && (
                                        <span className="absolute -top-1 -right-1 w-3 h-3 bg-yellow-500 rounded-full"></span>
                                    )}
                                </button>
                            ))}
                        </div>

                        {/* Calendar Tab */}
                        {activeTab === 'calendar' && (
                            <div className="space-y-6">
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <h2 className="text-2xl font-bold text-gray-800 mb-4 flex items-center gap-2">
                                        <Calendar size={28} />
                                        Your 6-Month Journey (18 Weeks)
                                    </h2>
                                    
                                    {/* Legend */}
                                    <div className="flex gap-6 mb-6 text-sm">
                                        <div className="flex items-center gap-2">
                                            <div className="w-4 h-4 bg-green-500 rounded"></div>
                                            <span>Completed</span>
                                        </div>
                                        <div className="flex items-center gap-2">
                                            <div className="w-4 h-4 bg-blue-500 rounded"></div>
                                            <span>Entered (Not Complete)</span>
                                        </div>
                                        <div className="flex items-center gap-2">
                                            <div className="w-4 h-4 bg-yellow-500 rounded"></div>
                                            <span>Today</span>
                                        </div>
                                        <div className="flex items-center gap-2">
                                            <div className="w-4 h-4 bg-gray-200 rounded"></div>
                                            <span>Upcoming</span
