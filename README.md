const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

// In-memory data storage
let items = [
  { name: "Tomato", marketPrice: 30, farmerPrice: 25 },
  { name: "Potato", marketPrice: 20, farmerPrice: 18 },
  { name: "Apple", marketPrice: 100, farmerPrice: 90 },
  { name: "Wheat Seeds", marketPrice: 50, farmerPrice: 45 },
  { name: "Chickpeas", marketPrice: 60, farmerPrice: 55 }
];

// API to get items
app.get('/api/items', (req, res) => {
  res.json(items);
});

// API to update/add item
app.post('/api/update', (req, res) => {
  const { name, marketPrice, farmerPrice } = req.body;
  const index = items.findIndex(i => i.name.toLowerCase() === name.toLowerCase());

  if (index !== -1) {
    items[index] = { name, marketPrice, farmerPrice };
    res.json({ message: "Item updated" });
  } else {
    items.push({ name, marketPrice, farmerPrice });
    res.json({ message: "Item added" });
  }
});

// Frontend HTML served directly
app.get('/', (req, res) => {
  res.send(`
<!DOCTYPE html>
<html>
<head>
  <title>Minimal Farmers Market Price Tracker</title>
  <style>
    body { font-family: Arial; padding: 20px; }
    .hidden { display: none; }
    input, select, button { margin: 10px 0; padding: 8px; width: 100%; }
    #results { margin-top: 20px; }
  </style>
</head>
<body>

  <div id="login">
    <h1>MINIMAL FARMERS MARKET PRICE TRACKER</h1>
    <h2>Login</h2>
    <input type="text" id="username" placeholder="Username" />
    <input type="password" id="password" placeholder="Password" />
    <button onclick="handleLogin()">Login</button>
  </div>

  <div id="selection" class="hidden">
    <h2>Select Language and Area</h2>
    <select id="language">
      <option>English</option>
      <option>Hindi</option>
      <option>Kannada</option>
    </select>
    <select id="area">
      <option>Bengaluru</option>
      <option>Mysuru</option>
      <option>Hubli</option>
    </select>
    <button onclick="showDashboard()">Continue</button>
  </div>

  <div id="dashboard" class="hidden">
    <h2>Grocery Price Dashboard</h2>
    <input type="text" id="search" placeholder="Search for items..." oninput="searchItems()" />
    <div id="results"></div>
  </div>

<script>
  let allItems = [];

  function handleLogin() {
    const user = document.getElementById('username').value;
    const pass = document.getElementById('password').value;

    if (user && pass) {
      document.getElementById('login').classList.add('hidden');
      document.getElementById('selection').classList.remove('hidden');
    } else {
      alert("Enter username and password");
    }
  }

  function showDashboard() {
    document.getElementById('selection').classList.add('hidden');
    document.getElementById('dashboard').classList.remove('hidden');

    fetch('/api/items')
      .then(res => res.json())
      .then(data => {
        allItems = data;
        searchItems();
      });
  }

  function searchItems() {
    const query = document.getElementById('search').value.toLowerCase();
    const filtered = allItems.filter(item =>
      item.name.toLowerCase().includes(query)
    );

    const html = filtered.map(item =>
      '<div><strong>' + item.name + '</strong><br>' +
      'Market: ₹' + item.marketPrice +
      ' | Farmer: ₹' + item.farmerPrice +
      '</div>'
    ).join('');

    document.getElementById('results').innerHTML =
      html || 'No results found';
  }
</script>

</body>
</html>
  `);
});

app.listen(PORT, () => {
  console.log("Server running at http://localhost:" + PORT);
});


