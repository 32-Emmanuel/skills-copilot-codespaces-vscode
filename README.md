
my-react-app/
├── public/
│ └── vite.svg
├── srmy-react-app/
├── public/
│ └── vite.svg
├── src/
│ ├── components/
│ ├── App.jsx
│ ├── main.jsx
│ └── index.css
├── .gitignore
├── index.html
├── package.json
└── vite.config.js


// src/App.jsx
import React from 'react';

function App() {
  return (
    <div style={{ padding: '2rem', fontFamily: 'Arial, sans-serif' }}>
      <h1>Hello World</h1>
    </div>
  );
}


// src/main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
