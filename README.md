# hello-world
This repository is for practicing the GitHub Flow.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sports Skill Tracker</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Sports Skill Tracker</h1>
        <nav>
            <a href="#">Home</a>
            <a href="#">Track Progress</a>
            <a href="#">Training Plans</a>
        </nav>
    </header>

    <section id="input-form">
        <h2>Track Your Progress</h2>
        <form id="progress-form">
            <label for="sport">Sport:</label>
            <select id="sport">
                <option value="soccer">Soccer</option>
                <option value="basketball">Basketball</option>
                <option value="running">Running</option>
            </select>
            <br>
            <label for="metric">Metric:</label>
            <input type="text" id="metric" placeholder="e.g., Running Time in Seconds">
            <br>
            <label for="value">Value:</label>
            <input type="number" id="value" placeholder="e.g., 15.5">
            <br>
            <button type="submit">Submit</button>
        </form>
    </section>

    <section id="progress">
        <h2>Your Progress</h2>
        <div id="progress-display"></div>
    </section>

    <footer>
        <p>&copy; 2025 Sports Skill Tracker. All rights reserved.</p>
    </footer>

    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
}

header {
    background-color: #0073e6;
    color: white;
    padding: 20px;
    text-align: center;
}

nav a {
    color: white;
    padding: 10px 20px;
    text-decoration: none;
    margin: 0 10px;
}

nav a:hover {
    background-color: #005bb5;
}

#input-form, #progress {
    margin: 20px;
    padding: 20px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

form input, form select {
    margin: 10px 0;
    padding: 8px;
    width: 200px;
}

button {
    background-color: #0073e6;
    color: white;
    padding: 10px 20px;
    border: none;
    cursor: pointer;
}

button:hover {
    background-color: #005bb5;
}

footer {
    text-align: center;
    padding: 10px;
    background-color: #333;
    color: white;
    position: fixed;
    width: 100%;
    bottom: 0;
}
document.getElementById("progress-form").addEventListener("submit", function(event) {
    event.preventDefault();

    // Get values from the form
    const sport = document.getElementById("sport").value;
    const metric = document.getElementById("metric").value;
    const value = document.getElementById("value").value;

    // Create a progress entry
    const progressEntry = {
        sport: sport,
        metric: metric,
        value: value,
        date: new Date().toLocaleDateString()
    };

    // Call backend API to save data (later we'll implement this in the backend)
    saveProgressToDatabase(progressEntry);

    // Display progress on the front-end
    displayProgress(progressEntry);
});

// Function to display progress
function displayProgress(progressEntry) {
    const progressDisplay = document.getElementById("progress-display");
    const entry = document.createElement("div");
    entry.classList.add("progress-entry");
    entry.innerHTML = `<p><strong>${progressEntry.sport}</strong> - ${progressEntry.metric}: ${progressEntry.value} (Date: ${progressEntry.date})</p>`;
    progressDisplay.appendChild(entry);
}

// Function to save progress (this will eventually interact with backend)
function saveProgressToDatabase(progressEntry) {
    // Placeholder: In a real app, you would make a POST request to a backend server
    console.log("Saving progress:", progressEntry);
}
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const app = express();

// Middlewares
app.use(express.json());
app.use(cors());

// Connect to MongoDB
mongoose.connect("mongodb://localhost:27017/sportstracker", { useNewUrlParser: true, useUnifiedTopology: true });

// Define a schema and model for tracking sports progress
const progressSchema = new mongoose.Schema({
    sport: String,
    metric: String,
    value: Number,
    date: String
});

const Progress = mongoose.model("Progress", progressSchema);

// Route to get all progress data
app.get("/progress", async (req, res) => {
    try {
        const progressData = await Progress.find();
        res.json(progressData);
    } catch (err) {
        res.status(400).send("Error retrieving progress data.");
    }
});

// Route to post new progress data
app.post("/progress", async (req, res) => {
    const { sport, metric, value, date } = req.body;

    const newProgress = new Progress({
        sport,
        metric,
        value,
        date
    });

    try {
        await newProgress.save();
        res.status(201).send("Progress saved.");
    } catch (err) {
        res.status(400).send("Error saving progress.");
    }
});

// Start server
app.listen(5000, () => {
    console.log("Server is running on http://localhost:5000");
});
function saveProgressToDatabase(progressEntry) {
    fetch("http://localhost:5000/progress", {
        method: "POST",
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify(progressEntry)
    })
    .then(response => response.json())
    .then(data => {
        console.log("Data saved:", data);
    })
    .catch(error => {
        console.error("Error saving progress:", error);
    });
}
npm init -y
npm install express mongoose cors
node server.js

