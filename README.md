# portfoliotracker
Tracking portfolio
[portfolio_tracker_html.html](https://github.com/user-attachments/files/23019253/portfolio_tracker_html.html)
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

        // Lucide React icons as inline SVG components
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
            const [activeTab, setActiveTab] = useState('daily');

            // Load data from localStorage on mount
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

            // Save to localStorage whenever data changes
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

            const addDailyEntry = () => {
                if (!currentEntry.task) return;
                
                const newEntry = {
                    ...currentEntry,
                    id: Date.now(),
                    timestamp: new Date().toISOString()
                };
                
                setEntries([newEntry, ...entries]);
                setCurrentEntry({
                    date: new Date().toISOString().split('T')[0],
                    task: '',
                    completed: false,
                    capture: '',
                    timeSpent: '',
                    category: 'capture'
                });
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
                const heroProjects = projects.filter(p => p.isHero);
                
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
                    testimonials: testimonials.map(t => ({
                        quote: t.content,
                        source: t.source,
                        context: t.context
                    })),
                    metrics: metrics.map(m => ({
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

            const stats = {
                totalEntries: entries.length,
                completedTasks: entries.filter(e => e.completed).length,
                totalTime: entries.reduce((sum, e) => sum + (parseInt(e.timeSpent) || 0), 0),
                heroProjects: projects.filter(p => p.isHero).length
            };

            return (
                <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
                    <div className="max-w-6xl mx-auto">
                        {/* Header */}
                        <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
                            <div className="flex justify-between items-center mb-4">
                                <div>
                                    <h1 className="text-3xl font-bold text-gray-800">Executive Portfolio Builder</h1>
                                    <p className="text-gray-600 mt-1">Your daily progress tracker & portfolio database</p>
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
                            <div className="grid grid-cols-4 gap-4">
                                <div className="bg-blue-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-blue-600">{stats.totalEntries}</div>
                                    <div className="text-sm text-gray-600">Total Entries</div>
                                </div>
                                <div className="bg-green-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-green-600">{stats.completedTasks}</div>
                                    <div className="text-sm text-gray-600">Tasks Done</div>
                                </div>
                                <div className="bg-purple-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-purple-600">{stats.totalTime}</div>
                                    <div className="text-sm text-gray-600">Minutes Worked</div>
                                </div>
                                <div className="bg-orange-50 p-4 rounded-lg">
                                    <div className="text-2xl font-bold text-orange-600">{stats.heroProjects}</div>
                                    <div className="text-sm text-gray-600">Hero Projects</div>
                                </div>
                            </div>
                        </div>

                        {/* Tabs */}
                        <div className="flex gap-2 mb-6">
                            {['daily', 'projects', 'testimonials', 'metrics'].map(tab => (
                                <button
                                    key={tab}
                                    onClick={() => setActiveTab(tab)}
                                    className={`px-6 py-3 rounded-lg font-medium transition ${
                                        activeTab === tab
                                            ? 'bg-white text-blue-600 shadow-lg'
                                            : 'bg-white/50 text-gray-600 hover:bg-white/80'
                                    }`}
                                >
                                    {tab.charAt(0).toUpperCase() + tab.slice(1)}
                                </button>
                            ))}
                        </div>

                        {/* Daily Entry Tab */}
                        {activeTab === 'daily' && (
                            <div className="space-y-6">
                                {/* Entry Form */}
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <h2 className="text-xl font-bold text-gray-800 mb-4 flex items-center gap-2">
                                        <Calendar size={24} />
                                        Today's Entry
                                    </h2>
                                    
                                    <div className="space-y-4">
                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-2">Date</label>
                                            <input
                                                type="date"
                                                value={currentEntry.date}
                                                onChange={(e) => setCurrentEntry({...currentEntry, date: e.target.value})}
                                                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            />
                                        </div>

                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-2">Category</label>
                                            <select
                                                value={currentEntry.category}
                                                onChange={(e) => setCurrentEntry({...currentEntry, category: e.target.value})}
                                                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            >
                                                <option value="capture">Capture Mode</option>
                                                <option value="visibility">Build Visibility</option>
                                                <option value="publish">Create & Publish</option>
                                                <option value="polish">Polish & Connect</option>
                                                <option value="strategic">Strategic Moves</option>
                                                <option value="transition">Activate & Transition</option>
                                            </select>
                                        </div>

                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-2">Today's ONE Thing</label>
                                            <input
                                                type="text"
                                                value={currentEntry.task}
                                                onChange={(e) => setCurrentEntry({...currentEntry, task: e.target.value})}
                                                placeholder="What's the one task you're focusing on today?"
                                                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            />
                                        </div>

                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-2">Capture (anything to save for portfolio)</label>
                                            <textarea
                                                value={currentEntry.capture}
                                                onChange={(e) => setCurrentEntry({...currentEntry, capture: e.target.value})}
                                                placeholder="Notes, ideas, metrics, feedback, screenshots taken, etc."
                                                rows={4}
                                                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            />
                                        </div>

                                        <div>
                                            <label className="block text-sm font-medium text-gray-700 mb-2">Time Spent (minutes)</label>
                                            <input
                                                type="number"
                                                value={currentEntry.timeSpent}
                                                onChange={(e) => setCurrentEntry({...currentEntry, timeSpent: e.target.value})}
                                                placeholder="15"
                                                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                                            />
                                        </div>

                                        <div className="flex items-center gap-2">
                                            <input
                                                type="checkbox"
                                                checked={currentEntry.completed}
                                                onChange={(e) => setCurrentEntry({...currentEntry, completed: e.target.checked})}
                                                className="w-5 h-5 text-blue-600"
                                            />
                                            <label className="text-sm font-medium text-gray-700">Task Completed</label>
                                        </div>

                                        <button
                                            onClick={addDailyEntry}
                                            className="w-full bg-blue-600 text-white py-3 rounded-lg hover:bg-blue-700 transition font-medium flex items-center justify-center gap-2"
                                        >
                                            <Plus size={20} />
                                            Save Today's Entry
                                        </button>
                                    </div>
                                </div>

                                {/* Entry History */}
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <h2 className="text-xl font-bold text-gray-800 mb-4">Your Journey</h2>
                                    <div className="space-y-4">
                                        {entries.length === 0 ? (
                                            <p className="text-gray-500 text-center py-8">No entries yet. Start your first one above!</p>
                                        ) : (
                                            entries.map(entry => (
                                                <div key={entry.id} className="border border-gray-200 rounded-lg p-4 hover:border-blue-300 transition">
                                                    <div className="flex justify-between items-start mb-2">
                                                        <div className="flex items-center gap-2">
                                                            {entry.completed && <CheckCircle size={20} className="text-green-600" />}
                                                            <span className="font-medium text-gray-800">{entry.date}</span>
                                                            <span className="text-xs bg-blue-100 text-blue-700 px-2 py-1 rounded">{entry.category}</span>
                                                        </div>
                                                        <button
                                                            onClick={() => deleteItem(entry.id, 'entry')}
                                                            className="text-red-500 hover:text-red-700"
                                                        >
                                                            <Trash2 size={16} />
                                                        </button>
                                                    </div>
                                                    <p className="font-medium text-gray-900 mb-2">{entry.task}</p>
                                                    {entry.capture && (
                                                        <p className="text-sm text-gray-600 mb-2 bg-gray-50 p-2 rounded">{entry.capture}</p>
                                                    )}
                                                    {entry.timeSpent && (
                                                        <p className="text-xs text-gray-500">⏱️ {entry.timeSpent} minutes</p>
                                                    )}
                                                </div>
                                            ))
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}

                        {/* Projects Tab */}
                        {activeTab === 'projects' && (
                            <div className="space-y-6">
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <div className="flex justify-between items-center mb-4">
                                        <h2 className="text-xl font-bold text-gray-800">Portfolio Projects</h2>
                                        <button
                                            onClick={addProject}
                                            className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition flex items-center gap-2"
                                        >
                                            <Plus size={20} />
                                            Add Project
                                        </button>
                                    </div>

                                    <div className="space-y-6">
                                        {projects.length === 0 ? (
                                            <p className="text-gray-500 text-center py-8">No projects yet. Add your first one!</p>
                                        ) : (
                                            projects.map(project => (
                                                <div key={project.id} className="border border-gray-200 rounded-lg p-6">
                                                    <div className="flex justify-between items-start mb-4">
                                                        <input
                                                            type="text"
                                                            value={project.title}
                                                            onChange={(e) => updateProject(project.id, 'title', e.target.value)}
                                                            className="text-xl font-bold text-gray-800 border-b-2 border-transparent hover:border-blue-300 focus:border-blue-500 focus:outline-none px-2"
                                                            placeholder="Project title"
                                                        />
                                                        <div className="flex items-center gap-2">
                                                            <label className="flex items-center gap-2 text-sm">
                                                                <input
                                                                    type="checkbox"
                                                                    checked={project.isHero}
                                                                    onChange={(e) => updateProject(project.id, 'isHero', e.target.checked)}
                                                                    className="w-4 h-4"
                                                                />
                                                                <span className={project.isHero ? 'text-orange-600 font-medium' : 'text-gray-600'}>
                                                                    ⭐ Hero Project
                                                                </span>
                                                            </label>
                                                            <button
                                                                onClick={() => deleteItem(project.id, 'project')}
                                                                className="text-red-500 hover:text-red-700"
                                                            >
                                                                <Trash2 size={16} />
                                                            </button>
                                                        </div>
                                                    </div>

                                                    <div className="space-y-3">
                                                        <div>
                                                            <label className="block text-sm font-medium text-gray-700 mb-1">The Challenge</label>
                                                            <textarea
                                                                value={project.challenge}
                                                                onChange={(e) => updateProject(project.id, 'challenge', e.target.value)}
                                                                placeholder="What problem were you solving?"
                                                                rows={2}
                                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
                                                            />
                                                        </div>

                                                        <div>
                                                            <label className="block text-sm font-medium text-gray-700 mb-1">Your Approach</label>
                                                            <textarea
                                                                value={project.approach}
                                                                onChange={(e) => updateProject(project.id, 'approach', e.target.value)}
                                                                placeholder="How did you lead the solution?"
                                                                rows={2}
                                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
                                                            />
                                                        </div>

                                                        <div>
                                                            <label className="block text-sm font-medium text-gray-700 mb-1">The Outcome</label>
                                                            <textarea
                                                                value={project.outcome}
                                                                onChange={(e) => updateProject(project.id, 'outcome', e.target.value)}
                                                                placeholder="What was the result?"
                                                                rows={2}
                                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
                                                            />
                                                        </div>

                                                        <div className="grid grid-cols-2 gap-3">
                                                            <div>
                                                                <label className="block text-sm font-medium text-gray-700 mb-1">Metrics/Impact</label>
                                                                <input
                                                                    type="text"
                                                                    value={project.metrics}
                                                                    onChange={(e) => updateProject(project.id, 'metrics', e.target.value)}
                                                                    placeholder="e.g., 23% increase, $4.2M revenue"
                                                                    className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
                                                                />
                                                            </div>
                                                            <div>
                                                                <label className="block text-sm font-medium text-gray-700 mb-1">Your Role/Team Size</label>
                                                                <input
                                                                    type="text"
                                                                    value={project.teamSize}
                                                                    onChange={(e) => updateProject(project.id, 'teamSize', e.target.value)}
                                                                    placeholder="e.g., Led team of 8 designers"
                                                                    className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500"
                                                                />
                                                            </div>
                                                        </div>
                                                    </div>
                                                </div>
                                            ))
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}

                        {/* Testimonials Tab */}
                        {activeTab === 'testimonials' && (
                            <div className="space-y-6">
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <div className="flex justify-between items-center mb-4">
                                        <h2 className="text-xl font-bold text-gray-800">Testimonials & Praise</h2>
                                        <button
                                            onClick={addTestimonial}
                                            className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition flex items-center gap-2"
                                        >
                                            <Plus size={20} />
                                            Add Testimonial
                                        </button>
                                    </div>

                                    <div className="space-y-4">
                                        {testimonials.length === 0 ? (
                                            <p className="text-gray-500 text-center py-8">No testimonials yet. Add feedback from executives!</p>
                                        ) : (
                                            testimonials.map(testimonial => (
                                                <div key={testimonial.id} className="border border-gray-200 rounded-lg p-4 bg-blue-50">
                                                    <div className="flex justify-between items-start mb-2">
                                                        <div>
                                                            <input
                                                                type="text"
                                                                value={testimonial.source}
                                                                onChange={(e) => setTestimonials(testimonials.map(t => 
                                                                    t.id === testimonial.id ? {...t, source: e.target.value} : t
                                                                ))}
                                                                className="font-medium text-gray-800 bg-transparent border-b-2 border-transparent hover:border-blue-300 focus:border-blue-500 focus:outline-none px-2"
                                                                placeholder="Person's name & title"
                                                            />
                                                            <p className="text-xs text-gray-500 px-2">{testimonial.date}</p>
                                                        </div>
                                                        <button
                                                            onClick={() => deleteItem(testimonial.id, 'testimonial')}
                                                            className="text-red-500 hover:text-red-700"
                                                        >
                                                            <Trash2 size={16} />
                                                        </button>
                                                    </div>
                                                    
                                                    <textarea
                                                        value={testimonial.content}
                                                        onChange={(e) => setTestimonials(testimonials.map(t => 
                                                            t.id === testimonial.id ? {...t, content: e.target.value} : t
                                                        ))}
                                                        placeholder="Paste the testimonial or feedback here..."
                                                        rows={3}
                                                        className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 mb-2 bg-white"
                                                    />
                                                    
                                                    <input
                                                        type="text"
                                                        value={testimonial.context}
                                                        onChange={(e) => setTestimonials(testimonials.map(t => 
                                                            t.id === testimonial.id ? {...t, context: e.target.value} : t
                                                        ))}
                                                        placeholder="Context (e.g., 'After Product Launch Q3 2024')"
                                                        className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 text-sm bg-white"
                                                    />
                                                </div>
                                            ))
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}

                        {/* Metrics Tab */}
                        {activeTab === 'metrics' && (
                            <div className="space-y-6">
                                <div className="bg-white rounded-lg shadow-lg p-6">
                                    <div className="flex justify-between items-center mb-4">
                                        <h2 className="text-xl font-bold text-gray-800">Business Metrics & Impact</h2>
                                        <button
                                            onClick={addMetric}
                                            className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition flex items-center gap-2"
                                        >
                                            <Plus size={20} />
                                            Add Metric
                                        </button>
                                    </div>

                                    <div className="space-y-4">
                                        {metrics.length === 0 ? (
                                            <p className="text-gray-500 text-center py-8">No metrics yet. Add measurable outcomes!</p>
                                        ) : (
                                            metrics.map(metric => (
                                                <div key={metric.id} className="border border-gray-200 rounded-lg p-4">
                                                    <div className="flex justify-between items-start mb-3">
                                                        <input
                                                            type="text"
                                                            value={metric.description}
                                                            onChange={(e) => setMetrics(metrics.map(m => 
                                                                m.id === metric.id ? {...m, description: e.target.value} : m
                                                            ))}
                                                            className="font-medium text-gray-800 bg-transparent border-b-2 border-transparent hover:border-blue-300 focus:border-blue-500 focus:outline-none px-2 flex-1"
                                                            placeholder="Metric description"
                                                        />
                                                        <button
                                                            onClick={() => deleteItem(metric.id, 'metric')}
                                                            className="text-red-500 hover:text-red-700"
                                                        >
                                                            <Trash2 size={16} />
                                                        </button>
                                                    </div>
                                                    
                                                    <div className="grid grid-cols-2 gap-3">
                                                        <div>
                                                            <label className="block text-xs text-gray-600 mb-1">Value/Number</label>
                                                            <input
                                                                type="text"
                                                                value={metric.value}
                                                                onChange={(e) => setMetrics(metrics.map(m => 
                                                                    m.id === metric.id ? {...m, value: e.target.value} : m
                                                                ))}
                                                                placeholder="e.g., 23%, $4.2M, 10,000 users"
                                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 text-sm"
                                                            />
                                                        </div>
                                                        <div>
                                                            <label className="block text-xs text-gray-600 mb-1">Related Project</label>
                                                            <input
                                                                type="text"
                                                                value={metric.project}
                                                                onChange={(e) => setMetrics(metrics.map(m => 
                                                                    m.id === metric.id ? {...m, project: e.target.value} : m
                                                                ))}
                                                                placeholder="Project name"
                                                                className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 text-sm"
                                                            />
                                                        </div>
                                                    </div>
                                                    
                                                    <div className="mt-2">
                                                        <label className="block text-xs text-gray-600 mb-1">Business Impact</label>
                                                        <input
                                                            type="text"
                                                            value={metric.impact}
                                                            onChange={(e) => setMetrics(metrics.map(m => 
                                                                m.id === metric.id ? {...m, impact: e.target.value} : m
                                                            ))}
                                                            placeholder="How did this affect the business?"
                                                            className="w-full p-2 border border-gray-300 rounded focus:ring-2 focus:ring-blue-500 text-sm"
                                                        />
                                                    </div>
                                                </div>
                                            ))
                                        )}
                                    </div>
                                </div>
                            </div>
                        )}
                    </div>
                </div>
            );
        };

        ReactDOM.render(<PortfolioTracker />, document.getElementById('root'));
    </script>
</body>
</html>
