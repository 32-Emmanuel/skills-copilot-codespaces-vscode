project/
├── package.json
├── server.js
└── README.md

mkdir express-api-project
cd express-api-project
npm init -y
npm install express


const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json()); // Middleware to parse JSON bodies

// In-memory data store
let items = [
  { id: 1, name: "Item One", description: "This is the first item." },
  { id: 2, name: "Item Two", description: "This is the second item." },
];

// Root route
app.get('/', (req, res) => {
  res.send('Hello, world!');
});

// GET /items - Retrieve all items
app.get('/items', (req, res) => {
  res.json(items);
});

// GET /items/:id - Retrieve item by ID
app.get('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const item = items.find(i => i.id === id);
  if (!item) {
    return res.status(404).json({ error: 'Item not found' });
  }
  res.json(item);
});

// POST /items - Create a new item
app.post('/items', (req, res) => {
  const { name, description } = req.body;
  if (!name || !description) {
    return res.status(400).json({ error: 'Name and description are required' });
  }
  const newItem = {
    id: items.length + 1,
    name,
    description
  };
  items.push(newItem);
  res.status(201).json(newItem);
});

// PUT /items/:id - Update an item by ID
app.put('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const item = items.find(i => i.id === id);
  if (!item) {
    return res.status(404).json({ error: 'Item not found' });
  }

  const { name, description } = req.body;
  if (!name || !description) {
    return res.status(400).json({ error: 'Name and description are required' });
  }

  item.name = name;
  item.description = description;

  res.json(item);
});

// DELETE /items/:id - Delete an item by ID
app.delete('/items/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = items.findIndex(i => i.id === id);
  if (index === -1) {
    return res.status(404).json({ error: 'Item not found' });
  }

  items.splice(index, 1);
  res.status(204).send();
});

// Error handling for invalid routes
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Start the server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});


Example Testing (Postman or Curl)

GET All Items

GET http://localhost:3000/items

GET Item by ID

GET http://localhost:3000/items/1

POST New Item

POST http://localhost:3000/items
Content-Type: application/json
Body:
{
  "name": "New Item",
  "description": "A new item added."
}

PUT Update Item

PUT http://localhost:3000/items/1
Content-Type: application/json
Body:
{
  "name": "Updated Item",
  "description": "This item was u
