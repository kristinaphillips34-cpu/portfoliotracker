[portfolio_tracker_html (3).html](https://github.com/user-attachments/files/23027977/portfolio_tracker_html.3.html)
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

        const App = () => {
            const [message, setMessage] = useState("Loading...");
            
            useEffect(() => {
                setMessage("Portfolio Tracker Loaded Successfully!");
            }, []);

            return (
                <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-6">
                    <div className="max-w-4xl mx-auto">
                        <div className="bg-white rounded-lg shadow-lg p-8">
                            <h1 className="text-3xl font-bold text-gray-800 mb-4">
                                Executive Portfolio Builder
                            </h1>
                            <p className="text-lg text-gray-600">
                                {message}
                            </p>
                            <div className="mt-6 p-4 bg-blue-50 rounded-lg">
                                <p className="text-sm text-gray-700">
                                    If you see this message, the app is loading correctly. 
                                    The full version is being prepared...
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            );
        };

        ReactDOM.render(<App />, document.getElementById('root'));
    </script>
</body>
</html>
