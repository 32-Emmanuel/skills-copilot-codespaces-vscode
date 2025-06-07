src/
├── api/              # API utility functions
├── assets/           # Images, icons, etc.
├── components/       # Reusable UI components
├── context/          # Context providers for global state
├── hooks/            # Custom hooks
├── pages/            # Page components
├── routes/           # Routing configuration
├── services/         # API interaction services
├── styles/           # Global and component-specific styles
└── App.js            # Main application component

const Navbar = () => (
  <nav>
    <ul>
      <li><Link to="/">Home</Link></li>
      <li><Link to="/search">Search</Link></li>
      <li><Link to="/watchlist">Watchlist</Link></li>
      <li><Link to="/profile">Profile</Link></li>
    </ul>
  </nav>
);


const MovieCard = ({ movie }) => (
  <div>
    <img src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`} alt={movie.title} />
    <h3>{movie.title}</h3>
    <p>{movie.vote_average} ⭐</p>
  </div>
);


const MovieList = () => {
  const [movies, setMovies] = useState([]);

  useEffect(() => {
    const getMovies = async () => {
      const data = await fetchMovies();
      setMovies(data.results);
    };
    getMovies();
  }, []);

  return (
    <div>
      {movies.map(movie => <MovieCard key={movie.id} movie={movie} />)}
    </div>
  );
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
      <div>
        {results.map(movie => <MovieCard key={movie.id} movie={movie} />)}
      </div>
    </div>
  );
};


const Profile = () => {
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    const getProfile = async () => {
      const data = await fetchUserProfile();
      setProfile(data);
    };
    getProfile();
  }, []);

  return (
    <div>
      {profile && (
        <>
          <h2>{profile.username}</h2>
          <p>Email: {profile.email}</p>
          <h3>Favorites</h3>
          <div>
            {profile.favorites.map(movie => <MovieCard key={movie.id} movie={movie} />)}
          </div>
        </>
      )}
    </div>
  );
};


const App = () => (
  <Router>
    <Navbar />
    <Switch>
      <Route path="/" exact component={MovieList} />
      <Route path="/search" component={Search} />
      <Route path="/profile" component={Profile} />
    </Switch>
  </Router>
);


<div className="max-w-sm rounded overflow-hidden shadow-lg">
  <img className="w-full" src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`} alt={movie.title} />
  <div className="px-6 py-4">
    <div className="font-bold text-xl mb-2">{movie.title}</div>
    <p className="text-gray-700 text-base">{movie.overview}</p>
  </div>
</div>



server/
├── config/
│   └── db.js
├── models/
│   └── User.js
├── routes/
│   ├── auth.js
│   ├── movies.js
│   └── users.js
├── controllers/
│   └── authController.js
├── middleware/
│   └── authMiddleware.js
├── .env
├── server.js


const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI);
    console.log('MongoDB connected');
  } catch (err) {
    console.error(err.message);
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

module.exports = mongoose.model('User', UserSchema);

const User = require('../models/User');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

exports.register = async (req, res) => {
  const { email, password } = req.body;
  try {
    const existing = await User.findOne({ email });
    if (existing) return res.status(400).json({ msg: 'User exists' });

    const hashed = await bcrypt.hash(password, 10);
    const user = await User.create({ email, password: hashed });
    res.status(201).json(user);
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

    const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, {
      expiresIn: '1d',
    });

    res.json({ token, user: { id: user._id, email: user.email } });
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
};


const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  const token = req.header('Authorization');
  if (!token) return res.status(401).json({ msg: 'No token, access denied' });

  try {
    const verified = jwt.verify(token, process.env.JWT_SECRET);
    req.user = verified.id;
    next();
  } catch {
    res.status(400).json({ msg: 'Token is not valid' });
  }
};


const express = require('express');
const router = express.Router();
const { register, login } = require('../controllers/authController');

router.post('/register', register);
router.post('/login', login);

const express = require('express');
const axios = require('axios');
const router = express.Router();
const auth = require('../middleware/authMiddleware');

const TMDB_URL = 'https://api.themoviedb.org/3';
const TMDB_KEY = process.env.TMDB_API_KEY;

router.get('/search', async (req, res) => {
  try {
    const { query } = req.query;
    const result = await axios.get(`${TMDB_URL}/search/movie`, {
      params: { api_key: TMDB_KEY, query },
    });
    res.json(result.data);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
});

router.get('/details/:id', async (req, res) => {
  try {
    const { id } = req.params;
    const result = await axios.get(`${TMDB_URL}/movie/${id}`, {
      params: { api_key: TMDB_KEY },
    });
    res.json(result.data);
  } catch (err) {
    res.status(500).json({ msg: err.message });
  }
});


const express = require('express');
const router = express.Router();
const auth = require('../middleware/authMiddleware');
const User = require('../models/User');

router.get('/me', auth, async (req, res) => {
  const user = await User.findById(req.user);
  res.json(user);
});

router.post('/favorites', auth, async (req, res) => {
  const user = await User.findById(req.user);
  user.favorites.push(req.body.movie);
  await user.save();
  res.json(user.favorites);
});

router.post('/watchlist', auth, async (req, res) => {
  const user = await User.findById(req.user);
  user.watchlist.push(req.body.movie);
  await user.save();
  res.json(user.watchlist);
});


MONGO_URI=your_mongo_uri
JWT_SECRET=your_jwt_secret
TMDB_API_KEY=your_tmdb_api_key


const express = require('express');
const connectDB = require('./config/db');
const cors = require('cors');
require('dotenv').config();

const app = express();
connectDB();

app.use(cors());
app.use(express.json());

app.use('/api/auth', require('./routes/auth'));
app.use('/api/movies', require('./routes/movies'));
app.use('/api/users', require('./routes/users'));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));


// services/movieService.js
const API_KEY = 'your_tmdb_api_key';
const BASE_URL = 'https://api.themoviedb.org/3';

export const fetchMovies = async () => {
  const response = await fetch(`${BASE_URL}/movie/popular?api_key=${API_KEY}`);
  const data = await response.json();
 67
