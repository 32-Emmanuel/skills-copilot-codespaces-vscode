/src
  ├── App.js
  ├── ListComponent.js
  └── index.js


import React from 'react';

const ListComponent = ({ items, renderItem, emptyMessage = "No items to display." }) => {
  if (!items || items.length === 0) {
    return <div>{emptyMessage}</div>;
  }

  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>
          {renderItem ? renderItem(item) : <div>{JSON.stringify(item)}</div>}
        </li>
      ))}
    </ul>
  );
};

export default ListComponent;

import React, { useState, useEffect } from 'react';
import ListComponent from './ListComponent';

const App = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  // Example: Fetching data from JSONPlaceholder API
  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        const result = await response.json();
        setData(result.slice(0, 10)); // Just the first 10 for brevity
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      <h1>Post List</h1>

      {loading && <div>Loading...</div>}
      {error && <div style={{ color: 'red' }}>Error: {error}</div>}

      {!loading && !error && (
        <ListComponent
          items={data}
          renderItem={(item) => (
            <div>
              <strong>{item.title}</strong>
              <p>{item.body}</p>
            </div>
          )}
          emptyMessage="No posts available."
        />
      )}
    </div>
  );
};

export default App;


import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);

