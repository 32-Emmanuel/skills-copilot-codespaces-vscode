import React from 'react';
import Counter from './Counter';

function App() {
  return (
    <div className="App">
      <Counter />
    </div>
  );
}

import React, { useState } from 'react';
import './Counter.css'; // For styling

const Counter = () => {
  const [count, setCount] = useState(0);
  const LIMIT = 10;

  const increase = () => {
    if (count < LIMIT) {
      setCount(prevCount => prevCount + 1);
    }
  };

  const decrease = () => {
    if (count > 0) {
      setCount(prevCount => prevCount - 1);
    }
  };

  const getMessage = () => {
    if (count === LIMIT) {
      return "You've reached the limit!";
    }
    return '';
  };

  return (
    <div className="counter-container">
      <h1>Counter App</h1>
      <div className="count-display">{count}</div>
      <div className="button-group">
        <button onClick={increase} className="btn increase">Increase</button>
        <button onClick={decrease} className="btn decrease" disabled={count === 0}>Decrease</button>
      </div>
      <p className="message">{getMessage()}</p>
    </div>
  );
};

.counter-container {
  text-align: center;
  margin-top: 50px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.count-display {
  font-size: 48px;
  margin: 20px 0;
  color: #333;
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.btn {
  padding: 10px 20px;
  font-size: 18px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.3s ease;
}

.increase {
  background-color: #28a745;
  color: white;
}

.decrease {
  background-color: #dc3545;
  color: white;
}

.btn:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.message {
  margin-top: 20px;
  font-size: 18px;
  color: #ff8800;
}

