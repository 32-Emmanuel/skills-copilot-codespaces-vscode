nodejs-chat-app/
├── server.js
├── public/
│   └── index.html
└── README.md


const express = require('express');
const http = require('http');
const cors = require('cors');
const { Server } = require('socket.io');

const app = express();
app.use(cors());
app.use(express.json());

const server = http.createServer(app);
const io = new Server(server, {
cors: {
origin: '*',
}
});

// In-memory user store
let users = [];
let messages = [];

// Socket.IO events
io.on('connection', (socket) => {
console.log('A user connected:', socket.id);

socket.on('join', (username) => {
users.push({ id: socket.id, username });
io.emit('userList', users);
});

socket.on('chatMessage', (msg) => {
const message = { id: socket.id, msg, time: new Date() };
messages.push(message);
io.emit('message', message);
});

socket.on('disconnect', () => {
users = users.filter(user => user.id !== socket.id);
io.emit('userList', users);
console.log('User disconnected:', socket.id);
});
});

// REST API endpoints
app.get('/api/users', (req, res) => {
res.json(users);
});

app.get('/api/messages', (req, res) => {
res.json(messages.slice(-50)); // Last 50 messages
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
console.log(`Server running on port ${PORT}`);
});

<!DOCTYPE html>
<html>
<head>
<title>Node.js Chat</title>
</head>
<body>
<h2>Real-time Chat</h2>
<input id="username" placeholder="Enter username" />
<button onclick="joinChat()">Join</button>
<div id="chat" style="display:none">
<div id="messages"></div>
<input id="msg" placeholder="Type message..." />
<button onclick="sendMessage()">Send</button>
</div>
<script src="https://cdn.socket.io/4.0.1/socket.io.min.js"></script>
<script>
const socket = io("http://localhost:3000");
let username = "";

function joinChat() {
username = document.getElementById("username").value;
socket.emit("join", username);
document.getElementById("chat").style.display = "block";
}

function sendMessage() {
const msg = document.getElementById("msg").value;
socket.emit("chatMessage", msg);
document.getElementById("msg").value = "";
}

socket.on("message", (data) => {
const msgDiv = document.getElementById("messages");
const newMsg = document.createElement("p");
newMsg.textContent = `${data.msg}`;
msgDiv.appendChild(newMsg);
});

socket.on("userList", (users) => {
console.log("Active users:", users);
});
</script>
</body>
</html>


Node.js Scalable Real-Time Chat Application

Overview

This project demonstrates the power of **Node.js** for scalable, real-time applications. It includes:

- A real-time WebSocket-based chat (via Socket.IO)
- A REST API for fetching active users and messages
- Support for many concurrent connections

How to Run

```bash
npm install
node server.js

Open multiple browser windows at http://localhost:3000 with public/index.html.

Features

Real-time bidirectional communication using Socket.IO

Express.js-based REST API

In-memory storage of users and messages

Concurrent connection support


API Endpoints

GET /api/users – Returns active users

GET /api/messages – Returns last 50 messages


Node.js Scalability Explained

Node.js excels at I/O-bound, concurrent operations. This app showcases:

1. Event-Driven Non-blocking I/O:

Socket.IO and Express run on a single-threaded event loop.

Thousands of WebSocket clients can be handled with minimal CPU usage.


Concurrent Connections:

Node.js doesn't spawn new threads per request; it handles concurrent users efficiently.

Socket.IO queues events in the loop without blocking the server.


Horizontal Scalability (optional):

Easily deployable using multiple instances behind a load balancer.

Socket.IO supports clustering with Redis pub/sub if needed.


Resource Efficiency:

Ideal for lightweight microservices, chat servers, and real-time dashboards.



