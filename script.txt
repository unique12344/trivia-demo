// Dell trivia questions (easy)
const questions = {
    'A': { question: "What year was Dell founded?", options: ["1984", "1990", "2000"], answer: "1984" },
    'B': { question: "Who founded Dell?", options: ["Bill Gates", "Steve Jobs", "Michael Dell"], answer: "Michael Dell" },
    'C': { question: "What is Dell’s popular laptop series?", options: ["XPS", "ThinkPad", "MacBook"], answer: "XPS" },
    'D': { question: "What color is Dell’s logo?", options: ["Blue", "Red", "Green"], answer: "Blue" },
    'E': { question: "Which operating system is pre-installed on most Dell PCs?", options: ["Windows", "Linux", "MacOS"], answer: "Windows" },
    'F': { question: "Dell’s Alienware brand is known for?", options: ["Gaming", "Office Work", "Medical Use"], answer: "Gaming" }
};

// Game state
let correctAnswers = 0;
const totalQuestions = Object.keys(questions).length;
let playerName = prompt("Enter your name:");
if (!playerName) playerName = "Player";

// Load leaderboard
let leaderboard = JSON.parse(localStorage.getItem("leaderboard")) || [];

// Create puzzle grid dynamically
const puzzleGrid = document.getElementById("puzzle-grid");
const letters = Object.keys(questions);
letters.forEach(letter => {
    let tile = document.createElement("div");
    tile.classList.add("puzzle-tile");
    tile.textContent = letter;
    tile.addEventListener("click", () => showQuestion(letter, tile));
    puzzleGrid.appendChild(tile);
});

// Show question when a letter is clicked
function showQuestion(letter, tile) {
    if (!questions[letter]) return;

    document.getElementById("question-text").textContent = questions[letter].question;
    let optionsContainer = document.getElementById("options");
    optionsContainer.innerHTML = "";

    questions[letter].options.forEach(option => {
        let btn = document.createElement("button");
        btn.textContent = option;
        btn.onclick = () => checkAnswer(letter, option, tile, btn);
        optionsContainer.appendChild(btn);
    });
}

// Check answer and update state
function checkAnswer(letter, chosenOption, tile, btn) {
    let correct = questions[letter].answer === chosenOption;
    
    if (correct) {
        btn.classList.add("correct");
        tile.style.animation = "correctAnswer 0.5s forwards";
        document.getElementById("correct-sound").play();
        correctAnswers++;
    } else {
        btn.classList.add("wrong");
        tile.style.animation = "wrongAnswer 0.5s forwards";
        document.getElementById("wrong-sound").play();
    }

    tile.removeEventListener("click", () => showQuestion(letter, tile));
    delete questions[letter];

    setTimeout(() => {
        if (Object.keys(questions).length === 0) {
            showFinalImage();
        }
    }, 1000);
}

// Display final Dell image and leaderboard
function showFinalImage() {
    let questionBox = document.getElementById("question-box");
    questionBox.innerHTML = `
        <h2>Game Over!</h2>
        <p>${playerName}, you got ${correctAnswers} out of ${totalQuestions} correct!</p>
        <img id="final-image" src="https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Dell_Logo.svg/512px-Dell_Logo.svg.png" alt="Dell Logo">
    `;

    updateLeaderboard(playerName, correctAnswers);
}

// Update leaderboard
function updateLeaderboard(name, score) {
    leaderboard.push({ name, score });
    leaderboard.sort((a, b) => b.score - a.score);
    leaderboard = leaderboard.slice(0, 5); // Keep top 5 scores

    localStorage.setItem("leaderboard", JSON.stringify(leaderboard));

    let scoreList = document.getElementById("score-list");
    scoreList.innerHTML = leaderboard.map(entry => <li>${entry.name}: ${entry.score} </li>).join("");
}

// Load leaderboard on page load
document.addEventListener("DOMContentLoaded", () => {
    if (leaderboard.length > 0) {
        let scoreList = document.getElementById("score-list");
        scoreList.innerHTML = leaderboard.map(entry => <li>${entry.name}: ${entry.score}</li>).join("");
    }
});
