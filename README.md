import random

# This AI will try to guess if a number is greater than 50
class SimpleAI:
    def __init__(self):
        self.bias = 0
        self.learning_rate = 0.1

    def predict(self, number):
        # If the number plus our bias is greater than 50, predict 'greater'
        return number + self.bias > 50

    def learn(self, number, actual):
        # Adjust the bias based on whether we guessed correctly
        if self.predict(number) != actual:
            if actual:  # If the number is actually greater than 50
                self.bias += self.learning_rate
            else:
                self.bias -= self.learning_rate

# Training the AI
ai = SimpleAI()
for _ in range(1000):  # Train over 1000 iterations
    number = random.randint(0, 100)
    actual = number > 50
    ai.learn(number, actual)

# Testing the AI
correct = 0
total = 100
for _ in range(total):
    number = random.randint(0, 100)
    prediction = ai.predict(number)
    if prediction == (number > 50):
        correct += 1

print(f"AI Accuracy: {correct/total:.2f}")
// created by  Akins knupp
npm init -y
npm install express mongoose bcryptjs 
jsonwebtoken dotenv

// server.js
const express = require('express');
const mongoose = require('mongoose');
const dotenv = require('dotenv');

dotenv.config();
const app = express();
app.use(express.json());
// routes/userRoutes.js
const express = require('express');
const router = express.Router();
const User = require('../models/User');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

router.post('/register', async (req, res) => {
    try {
        const { username, email, password } = req.body;
        let user = await User.findOne({ email });
        if (user) return res.status(400).send('User already exists');

        user = new User({ username, email, password });
        await user.save();

        const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET);
        res.send({ user, token });
    } catch (err) {
        res.status(500).send(err.message);
    }
});

module.exports = router;
// Connect to MongoDB
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('MongoDB Connected'))
    .catch(err => console.log(err));
// src/components/Register.js
import React, { useState } from 'react';

const Register = () => {
    const [formData, setFormData] = useState({
        username: '',
        email: '',
        password: ''
    });

    const handleChange = (e) => {
        setFormData({ ...formData, [e.target.name]: e.target.value });
    };

    const handleSubmit = async (e) => {
        e.preventDefault();
        // Here you would typically send the data to your backend
        console.log(formData);
    };

    return (
        <form onSubmit={handleSubmit}>
            <input name="username" value={formData.username} onChange={handleChange} placeholder="Username" />
            <input name="email" value={formData.email} onChange={handleChange} placeholder="Email" />
            <input type="password" name="password" value={formData.password} onChange={handleChange} placeholder="Password" />
            <button type="submit">Register</button>
        </form>
    );
};

export default Register;
// models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const UserSchema = new mongoose.Schema({
    username: { type: String, required: true, unique: true },
    email: { type: String, required: true, unique: true },
    password: { type: String, required: true }
});

UserSchema.pre('save', async function(next) {
    if (!this.isModified('password')) return next();
    this.password = await bcrypt.hash(this.password, 10);
    next();
});

module.exports = mongoose.model('User', UserSchema);
// Middleware for CORS if needed

// Routes
app.use('/api/users', require('./routes/userRoutes'));
app.use('/api/posts', require('./routes/postRoutes'));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

from collections import defaultdict
import random

class Database:
    def __init__(self):
        self.users = {}
        self.posts = {}
        self.groups = defaultdict(list)

    def add_user(self, user_id, interests, happiness_level=5):
        self.users[user_id] = {"interests": interests, "friends": [], "happiness": happiness_level}

    def add_post(self, post_id, user_id, content):
        self.posts[post_id] = {"user_id": user_id, "content": content, "likes": 0}

    def add_group(self, group_name, user_id):
        self.groups[group_name].append(user_id)

class AI:
    def __init__(self, db):
        self.db = db

    def suggest_friends(self, user_id):
        # Suggest friends based on common interests and happiness levels
        user = self.db.users[user_id]
        matches = []
        for uid, data in self.db.users.items():
            if uid != user_id and set(user["interests"]) & set(data["interests"]) and abs(user["happiness"] - data["happiness"]) <= 1:
                matches.append(uid)
        return random.sample(matches, min(len(matches), 3))

    def recommend_content(self, user_id):
        # Recommend posts based on user interests and current mood
        user = self.db.users[user_id]
        relevant_posts = [post for post in self.db.posts.values() if any(interest in self.db.users[post["user_id"]]["interests"] for interest in user["interests"])]
        return random.choice(relevant_posts) if relevant_posts else None

    def suggest_activities(self, user_id):
        # Suggest activities based on happiness level (very simplistic)
        happiness = self.db.users[user_id]["happiness"]
        if happiness < 5:
            return ["Meditation", "Reading", "Nature Walk"]
        else:
            return ["Social Event", "Learning New Skill", "Sports"]

class Him:
    def __init__(self):
        self.db = Database()
        self.ai = AI(self.db)

    def register_user(self, user_id, interests):
        self.db.add_user(user_id, interests)

    def update_user_happiness(self, user_id, happiness_level):
        if user_id in self.db.users:
            self.db.users[user_id]["happiness"] = happiness_level

    def post_content(self, user_id, content):
        post_id = len(self.db.posts) + 1  # Simple ID generation
        self.db.add_post(post_id, user_id, content)

    def join_group(self, user_id, group_name):
        self.db.add_group(group_name, user_id)

    def get_suggestions(self, user_id):
        friends = self.ai.suggest_friends(user_id)
        content = self.ai.recommend_content(user_id)
        activities = self.ai.suggest_activities(user_id)
        return {
            "friends": friends,
            "content": content,
            "activities": activities
        }

# Usage Example
him = Him()
him.register_user("user1", ["yoga", "meditation", "philosophy"])
him.register_user("user2", ["meditation", "art"])
him.register_user("user3", ["philosophy", "reading"])

him.update_user_happiness("user1", 4)  # User 1 is slightly unhappy
him.update_user_happiness("user2", 6)  # User 2 is moderately happy

him.post_content("user1", "Finding peace in nature today")
him.join_group("user1", "Mindfulness")
him.join_group("user2", "Mindfulness")

suggestions = him.get_suggestions("user1")
print(f"Suggestions for user1 in 'Him': {suggestions}")
import numpy as np
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, Embedding, Flatten, Dense, concatenate

# Example of a simple hybrid model
user_input = Input(shape=[1], name="User-Input")
user_embedding = Embedding(input_dim=10000, output_dim=50, name="User-Embedding")(user_input)
user_vec = Flatten(name="Flatten-Users")(user_embedding)

video_input = Input(shape=[1], name="Video-Input")
video_embedding = Embedding(input_dim=5000, output_dim=50, name="Video-Embedding")(video_input)
video_vec = Flatten(name="Flatten-Videos")(video_embedding)

# Concatenate user and video vectors
concat = concatenate([user_vec, video_vec])
dense1 = Dense(20, name="FullyConnected")(concat)
output = Dense(1, activation='relu', name="Activation")(dense1)

model = Model([user_input, video_input], output)
model.compile(optimizer='adam', loss='mean_squared_error')
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

# Example of loading data
df = pd.read_csv('video_data.csv')

# Normalize data for trend analysis
scaler = MinMaxScaler()
df[['views', 'likes', 'comments', 'shares']] = scaler.fit_transform(df[['views', 'likes', 'comments', 'shares']])

# Calculate trend score (simple example)
df['trend_score'] = df['views'] * 0.4 + df['likes'] * 0.3 + df['comments'] * 0.2 + df['shares'] * 0.1

# Sort by trend score
trendy_videos = df.sort_values('trend_score', ascending=False)
# Composite score
df['composite_score'] = df['trend_score'] * 0.5 + df['sentiment'] * 0.3 + df['personalization_score'] * 0.2

# Sort by composite score
recommendations = df.sort_values('composite_score', ascending=False).head(10)
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/recommend_videos', methods=['GET'])
def recommend_videos():
    # Here, fetch recommendations from your data processing
    recommended_videos = recommendations[['video_id', 'title', 'trend_score', 'sentiment']].to_dict(orient='records')
    return jsonify(recommended_videos)

if __name__ == '__main__':
    app.run(debug=True)
		import random

# This AI will try to guess if a number is greater than 50
class SimpleAI:
    def __init__(self):
        self.bias = 0
        self.learning_rate = 0.1

    def predict(self, number):
        # If the number plus our bias is greater than 50, predict 'greater'
        return number + self.bias > 50

    def learn(self, number, actual):
        # Adjust the bias based on whether we guessed correctly
        if self.predict(number) != actual:
            if actual:  # If the number is actually greater than 50
                self.bias += self.learning_rate
            else:
                self.bias -= self.learning_rate

# Training the AI
ai = SimpleAI()
for _ in range(1000):  # Train over 1000 iterations
    number = random.randint(0, 100)
    actual = number > 50
    ai.learn(number, actual)

# Testing the AI
correct = 0
total = 100
for _ in range(total):
    number = random.randint(0, 100)
    prediction = ai.predict(number)
    if prediction == (number > 50):
        correct += 1

print(f"AI Accuracy: {correct/total:.2f}")
