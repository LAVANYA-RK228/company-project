# company-project
// app.js

const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const userRoutes = require('./routes/userRoutes');
const blogRoutes = require('./routes/blogRoutes');
const commentRoutes = require('./routes/commentRoutes');

const app = express();

// Middleware
app.use(bodyParser.json());

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/blogDB', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});
const db = mongoose.connection;
db.on('error', console.error.bind(console, 'MongoDB connection error:'));

// Routes
app.use('/users', userRoutes);
app.use('/blogs', blogRoutes);
app.use('/comments', commentRoutes);

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

// models/user.js

const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  username: { type: String, required: true },
  email: { type: String, required: true },
  // Add more fields as needed
});

module.exports = mongoose.model('User', userSchema);


// models/blog.js

const mongoose = require('mongoose');

const blogSchema = new mongoose.Schema({
  title: { type: String, required: true },
  content: { type: String, required: true },
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  // Add more fields as needed
});

module.exports = mongoose.model('Blog', blogSchema);


// models/comment.js

const mongoose = require('mongoose');

const commentSchema = new mongoose.Schema({
  content: { type: String, required: true },
  author: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  blog: { type: mongoose.Schema.Types.ObjectId, ref: 'Blog', required: true },
  // Add more fields as needed
});

module.exports = mongoose.model('Comment', commentSchema);


// routes/userRoutes.js

const express = require('express');
const router = express.Router();
const UserController = require('../controllers/userController');

router.post('/', UserController.createUser);
router.get('/:userId', UserController.getUser);
// Add more routes as needed

module.exports = router;


// routes/blogRoutes.js

const express = require('express');
const router = express.Router();
const BlogController = require('../controllers/blogController');

router.post('/', BlogController.createBlog);
router.get('/:blogId', BlogController.getBlog);
// Add more routes as needed

module.exports = router;

// routes/commentRoutes.js

const express = require('express');
const router = express.Router();
const CommentController = require('../controllers/commentController');

router.post('/', CommentController.createComment);
router.get('/:commentId', CommentController.getComment);
// Add more routes as needed

module.exports = router;

// controllers/userController.js

const User = require('../models/user');

exports.createUser = async (req, res, next) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (error) {
    next(error);
  }
};

exports.getUser = async (req, res, next) => {
  try {
    const user = await User.findById(req.params.userId);
    res.json(user);
  } catch (error) {
    next(error);
  }
};

// Add more controller methods as needed
