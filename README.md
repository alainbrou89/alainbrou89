

// Import des modules nécessaires
const express = require('express');
const mongoose = require('mongoose');
const app = express();
const PORT = 3000;

// Connexion à la base de données MongoDB
mongoose.connect('mongodb://localhost/ivoirematch', {
   useNewUrlParser: true,
   useUnifiedTopology: true
});

// Schéma de l'utilisateur
const userSchema = new mongoose.Schema({
   name: String,
   age: Number,
   gender: String,
   bio: String,
   location: String,
   interests: [String],
   likedUsers: [String]
});

const User = mongoose.model('User', userSchema);

// Middleware
app.use(express.json());

// Inscription d'un utilisateur
app.post('/register', async (req, res) => {
   const { name, age, gender, bio, location, interests } = req.body;
   const newUser = new User({ name, age, gender, bio, location, interests });
   await newUser.save();
   res.json(newUser);
});

// Récupération des profils à proximité
app.get('/users', async (req, res) => {
   const users = await User.find();
   res.json(users);
});

// Envoi d'un "like" (geste vers la droite)
app.post('/like', async (req, res) => {
   const { userId, likedUserId } = req.body;
   const user = await User.findById(userId);
   user.likedUsers.push(likedUserId);
   await user.save();
   res.json({ message: 'User liked!' });
});

// Lancement du serveur
app.listen(PORT, () => {
   console.log(`Server is running on http://localhost:${PORT}`);
});



