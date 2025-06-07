src/
├── api/                # Raw API calls (axios or fetch)
│   └── tmdb.js
├── assets/             # Images, icons, logos
├── components/         # Reusable UI components
│   ├── Navbar.jsx
│   ├── MovieCard.jsx
│   └── MovieGrid.jsx
├── context/            # React Context for global states (auth, theme, etc.)
├── hooks/              # Custom hooks like useAuth, useFetchMovies
├── layouts/            # Layout components like MainLayout.jsx
├── pages/              # Pages for routes
│   ├── Home.jsx
│   ├── Search.jsx
│   ├── Profile.jsx
│   └── Watchlist.jsx
├── routes/             # Routing config (optional)
├── services/           # Abstracted services wrapping API (e.g., fetchMovies)
│   └── movieService.js
├── styles/             # CSS / Tailwind / Sass modules
│   └── App.css
└── App.jsx             # Entry component with routing


const Navbar = () => (
  <nav className="navbar">
    <ul>
      <li><Link to="/">Home</Link></li>
      <li><Link to="/search">Search</Link></li>
      <li><Link to="/watchlist">Watchlist</Link></li>
      <li><Link to="/profile">Profile</Link></li>
    </ul>
  </nav>
);


const MovieCard = ({ movie }) => (
  <div className="movie-card">
    <img
      srcSet={`https://image.tmdb.org/t/p/w500${movie.poster_path} 500w,
               https://image.tmdb.org/t/p/w780${movie.poster_path} 780w`}
      sizes="(max-width: 600px) 500px, 780px"
      src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`}
      alt={movie.title}
    />
    <div className="info">
      <h3>{movie.title}</h3>
      <p>{movie.vote_average} ⭐</p>
    </div>
  </div>
);


const MovieGrid = ({ movies }) => (
  <div className="container">
    {movies.map(movie => (
      <MovieCard key={movie.id} movie={movie} />
    ))}
  </div>
);


const Home = () => {
  const [movies, setMovies] = useState([]);

  useEffect(() => {
    const getMovies = async () => {
      const data = await fetchMovies();
      setMovies(data.results);
    };
    getMovies();
  }, []);

  return <MovieGrid movies={movies} />;
};


const Search = () => {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  const handleSearch = async () => {
    const data = await fetchSearchResults(query);
    setResults(data.results);
  };

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={e => setQuery(e.target.value)}
        placeholder="Search movies..."
      />
      <button onClick={handleSearch}>Search</button>
      <MovieGrid movies={results} />
    </div>
  );
};


const Profile = () => {
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    const loadProfile = async () => {
      const data = await fetchUserProfile();
      setProfile(data);
    };
    loadProfile();
  }, []);

  return profile ? (
    <div>
      <h2>{profile.username}</h2>
      <p>Email: {profile.email}</p>
      <h3>Favorites</h3>
      <MovieGrid movies={profile.favorites} />
    </div>
  ) : <p>Loading...</p>;
};


const App = () => (
  <Router>
    <Navbar />
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/search" element={<Search />} />
      <Route path="/profile" element={<Profile />} />
    </Routes>
  </Router>
);


export const fetchMovies = async () => {
  const res = await fetch(`${BASE_URL}/movie/popular?api_key=${API_KEY}`);
  return res.json();
};

export const fetchSearchResults = async (query) => {
  const res = await fetch(`${BASE_URL}/search/movie?api_key=${API_KEY}&query=${query}`);
  return res.json();
};


const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadData = async () => {
      const res = await fetch(url);
      const json = await res.json();
      setData(json);
      setLoading(false);
    };
    loadData();
  }, [url]);

  return { data, loading };
};



server/
├── config/
│   └── db.js
├── controllers/
│   ├── authController.js
│   ├── movieController.js
│   └── userController.js
├── middleware/
│   └── auth.js
├── models/
│   └── User.js
├── routes/
│   ├── auth.js
│   ├── movies.js
│   └── users.js
├── utils/
│   └── passwordValidator.js
├── .env
└── server.js

backend/
├── src/
│   ├── config/          # Database config, constants
│   ├── controllers/     # Request handling logic
│   ├── middleware/      # Auth, error handlers, etc.
│   ├── models/          # Mongoose schemas or SQL models
│   ├── routes/          # Route definitions
│   ├── services/        # Business logic and DB interaction
│   ├── utils/           # Helper functions (e.g., password, token)
│   ├── index.js         # Entry point (replaces server.js)
│   └── app.js           # Express app (for separation of concerns)
├── .env
├── package.json
└── README.md



require('dotenv').config();
const app = require('./app');
const connectDB = require('./config/db');

const PORT = process.env.PORT || 5000;

connectDB().then(() => {
  app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
  });
});


const express = require('express');
const cors = require('cors');

const authRoutes = require('./routes/authRoutes');
const userRoutes = require('./routes/userRoutes');
const movieRoutes = require('./routes/movieRoutes');

const app = express();

app.use(cors());
app.use(express.json());

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/users', userRoutes);
app.use('/api/movies', movieRoutes);


const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log('MongoDB connected');
  } catch (err) {
    console.error('DB Connection Error:', err.message);
    process.exit(1);
  }
};

const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  favorites: [Object],
  watchlist: [Object],
});
'

const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const { passwordStrength } = require('check-password-strength');
const User = require('../models/User');

exports.register = async (req, res) => {
  const { email, password } = req.body;
  const strength = passwordStrength(password);

  if (strength.id < 2) {
    return res.status(400).json({ msg: 'Password is too weak' });
  }

  try {
    const existingUser = await User.findOne({ email });
    if (existingUser) return res.status(400).json({ msg: 'User already exists' });

    const hashedPassword = await bcrypt.hash(password, 10);
    const user = await User.create({ email, password: hashedPassword });

    const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, { expiresIn: '1d' });

    res.status(201).json({ token, user: { id: user._id, email: user.email } });
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};

exports.login = async (req, res) => {
  const { email, password } = req.body;
  try {
    const user = await User.findOne({ email });
    if (!user) return res.status(400).json({ msg: 'Invalid credentials' });

    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) return res.status(400).json({ msg: 'Invalid credentials' });

    const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, { expiresIn: '1d' });

    res.json({ token, user: { id: user._id, email: user.email } });
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};


const User = require('../models/User');

exports.getProfile = async (req, res) => {
  try {
    const user = await User.findById(req.user);
    res.json(user);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};

exports.addFavorite = async (req, res) => {
  try {
    const user = await User.findById(req.user);
    user.favorites.push(req.body.movie);
    await user.save();
    res.json(user.favorites);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};

exports.addToWatchlist = async (req, res) => {
  try {
    const user = await User.findById(req.user);
    user.watchlist.push(req.body.movie);
    await user.save();
    res.json(user.watchlist);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};


const axios = require('axios');
const TMDB_URL = 'https://api.themoviedb.org/3';
const TMDB_KEY = process.env.TMDB_API_KEY;

exports.searchMovies = async (req, res) => {
  try {
    const { query } = req.query;
    const response = await axios.get(`${TMDB_URL}/search/movie`, {
      params: { api_key: TMDB_KEY, query },
    });
    res.json(response.data);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};

exports.getMovieDetails = async (req, res) => {
  try {
    const { id } = req.params;
    const response = await axios.get(`${TMDB_URL}/movie/${id}`, {
      params: { api_key: TMDB_KEY },
    });
    res.json(response.data);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};


const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  const token = req.header('Authorization');

  if (!token) return res.status(401).json({ msg: 'No token, access denied' });

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded.id;
    next();
  } catch {
    res.status(401).json({ msg: 'Invalid token' });
  }
};


const express = require('express');
const router = express.Router();
const { register, login } = require('../controllers/authController');

router.post('/register', register);
router.post('/login', login);

module.exports = router;


const express = require('express');
const router = express.Router();
const auth = require('../middleware/authMiddleware');
const {
  getProfile,
  addFavorite,
  addToWatchlist,
} = require('../controllers/userController');

router.get('/me', auth, getProfile);
router.post('/favorites', auth, addFavorite);
router.post('/watchlist', auth, addToWatchlist);

module.exports = router;


const express = require('express');
const router = express.Router();
const { searchMovies, getMovieDetails } = require('../controllers/movieController');

router.get('/search', searchMovies);
router.get('/details/:id', getMovieDetails);



styles/App.css

.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}

.navbar ul {
  display: flex;
  gap: 1rem;
  list-style: none;
}

.movie-card {
  max-width: 250px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  overflow: hidden;
}

@media (max-width: 768px) {
  .navbar ul {
    flex-direction: column;
  }
}

