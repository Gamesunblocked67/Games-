<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Study Hub</title>
<style>
    body { font-family: Arial, sans-serif; margin:0; background:#f0f0f0; }
    header { background:#4CAF50; color:white; padding:10px; text-align:center; }
    #nav { display:flex; justify-content:space-around; background:#333; }
    #nav button { flex:1; padding:10px; color:white; background:#333; border:none; cursor:pointer; }
    #nav button:hover { background:#555; }
    #main { padding:20px; display:flex; flex-wrap:wrap; gap:20px; }
    .tool { background:white; padding:15px; border-radius:8px; flex:1 1 300px; box-shadow:0 0 10px rgba(0,0,0,0.1); }
    #settingsPanel { display:none; background:#fff; padding:15px; position:fixed; top:50px; right:50px; border:1px solid #ccc; border-radius:10px; width:300px; z-index:100; box-shadow:0 0 15px rgba(0,0,0,0.2); }
    #settingsPanel input, #settingsPanel select { width:100%; margin:5px 0; padding:5px; }
    .hidden { display:none; }
    #bookmarkList { list-style:none; padding:0; margin:0; }
    #bookmarkList li { padding:5px; margin:5px 0; background:#ddd; cursor:move; }
    iframe { width:100%; height:300px; border:1px solid #ccc; }
</style>
</head>
<body>

<header>
    <h1>AI Study Hub</h1>
</header>

<div id="nav">
    <button onclick="openTool('aiTutor')">AI Tutor</button>
    <button onclick="openTool('quizMaker')">Quiz Maker</button>
    <button onclick="openTool('flashcards')">Flashcards</button>
    <button onclick="openTool('musicPlayer')">Music Player</button>
    <button onclick="openTool('timer')">Timer</button>
    <button onclick="openTool('studyGames')">Study Games</button>
    <button onclick="openTool('miniBrowser')">Mini Browser</button>
    <button onclick="toggleSettings()">⚙️ Settings</button>
</div>

<div id="main">
    <!-- Tools will be dynamically shown here -->
    <div id="aiTutor" class="tool hidden">
        <h2>AI Tutor</h2>
        <textarea id="aiQuestion" placeholder="Ask a question..." rows="3" style="width:100%;"></textarea>
        <button onclick="askAITutor()">Ask AI</button>
        <pre id="aiAnswer" style="background:#eee; padding:10px;"></pre>
    </div>

    <div id="quizMaker" class="tool hidden">
        <h2>Interactive Quiz Maker</h2>
        <textarea id="quizPrompt" placeholder="Enter quiz questions or topic..." rows="3" style="width:100%;"></textarea>
        <button onclick="generateQuiz()">Generate Quiz</button>
        <div id="quizOutput"></div>
    </div>

    <div id="flashcards" class="tool hidden">
        <h2>Flashcards</h2>
        <textarea id="flashPrompt" placeholder="Enter topics or terms..." rows="3" style="width:100%;"></textarea>
        <button onclick="generateFlashcards()">Generate Flashcards</button>
        <div id="flashOutput"></div>
    </div>

    <div id="musicPlayer" class="tool hidden">
        <h2>Music Player</h2>
        <input type="text" id="musicLink" placeholder="YouTube link or local file URL">
        <button onclick="playMusic()">Play</button>
        <div id="musicContainer"></div>
    </div>

    <div id="timer" class="tool hidden">
        <h2>Timer / Pomodoro</h2>
        <input type="number" id="timerMinutes" placeholder="Minutes" style="width:60px;">
        <button onclick="startTimer()">Start</button>
        <div id="timerDisplay">00:00</div>
    </div>

    <div id="studyGames" class="tool hidden">
        <h2>Study Games</h2>
        <button onclick="startMemoryGame()">Memory Game</button>
        <div id="gameContainer"></div>
    </div>

    <div id="miniBrowser" class="tool hidden">
        <h2>Mini Browser</h2>
        <input type="text" id="browserURL" placeholder="Enter URL" style="width:80%;">
        <button onclick="loadBrowser()">Go</button>
        <iframe id="browserFrame"></iframe>
    </div>
</div>

<div id="settingsPanel">
    <h3>Admin Settings</h3>
    <input type="password" id="adminPassword" placeholder="Enter admin password">
    <button onclick="loginAdmin()">Login</button>

    <div id="apiSettings" class="hidden">
        <label>OpenAI API Key</label>
        <input type="text" id="openAIKey" value="YOUR_OPENAI_KEY_HERE">
        <label>Gemini API Key</label>
        <input type="text" id="geminiKey" value="YOUR_GEMINI_KEY_HERE">
    </div>

    <h4>Bookmarks</h4>
    <ul id="bookmarkList"></ul>
    <input type="text" id="bookmarkInput" placeholder="Bookmark URL">
    <button onclick="addBookmark()">Add Bookmark</button>
</div>

<script>
let adminLoggedIn = false;

function toggleSettings() {
    if (!adminLoggedIn) {
        alert('Enter admin password to access settings.');
        return;
    }
    const panel = document.getElementById('settingsPanel');
    panel.style.display = panel.style.display === 'block' ? 'none' : 'block';
    hideAllTools();
}

function loginAdmin() {
    const pw = document.getElementById('adminPassword').value;
    if (pw === 'owner123') {
        adminLoggedIn = true;
        document.getElementById('apiSettings').classList.remove('hidden');
        alert('Admin logged in.');
    } else {
        alert('Incorrect password.');
    }
}

function hideAllTools() {
    document.querySelectorAll('.tool').forEach(t => t.classList.add('hidden'));
}

function openTool(toolId) {
    hideAllTools();
    document.getElementById('settingsPanel').style.display = 'none';
    document.getElementById(toolId).classList.remove('hidden');
}

// AI Tutor
async function askAITutor() {
    const question = document.getElementById('aiQuestion').value;
    const openAIKey = document.getElementById('openAIKey').value;
    const geminiKey = document.getElementById('geminiKey').value;

    // Placeholder: Replace with actual OpenAI/Gemini API call
    document.getElementById('aiAnswer').textContent = `You asked: ${question}\n\nAI says: This is a simulated answer.`;
}

// Quiz Maker
function generateQuiz() {
    const prompt = document.getElementById('quizPrompt').value;
    document.getElementById('quizOutput').innerHTML = `<p>Quiz for: ${prompt}</p><ol><li>Sample Question 1?</li><li>Sample Question 2?</li></ol>`;
}

// Flashcards
function generateFlashcards() {
    const prompt = document.getElementById('flashPrompt').value;
    document.getElementById('flashOutput').innerHTML = `<p>Flashcards for: ${prompt}</p><div>Term 1 - Definition</div><div>Term 2 - Definition</div>`;
}

// Music Player
function playMusic() {
    const link = document.getElementById('musicLink').value;
    const container = document.getElementById('musicContainer');
    container.innerHTML = `<iframe width="100%" height="80" src="${link}" frameborder="0" allow="autoplay"></iframe>`;
}

// Timer
let timerInterval;
function startTimer() {
    clearInterval(timerInterval);
    let minutes = parseInt(document.getElementById('timerMinutes').value) || 0;
    let seconds = 0;
    timerInterval = setInterval(() => {
        document.getElementById('timerDisplay').textContent = `${String(minutes).padStart(2,'0')}:${String(seconds).padStart(2,'0')}`;
        if (seconds === 0) {
            if (minutes === 0) clearInterval(timerInterval);
            else { minutes--; seconds = 59; }
        } else { seconds--; }
    }, 1000);
}

// Bookmarks
function addBookmark() {
    const url = document.getElementById('bookmarkInput').value;
    const li = document.createElement('li');
    li.textContent = url;
    document.getElementById('bookmarkList').appendChild(li);
}

// Mini Browser
function loadBrowser() {
    const url = document.getElementById('browserURL').value;
    document.getElementById('browserFrame').src = url;
}

// Simple Memory Game
function startMemoryGame() {
    const container = document.getElementById('gameContainer');
    container.innerHTML = '<p>Memory game starts! (Simulated)</p>';
}
</script>

</body>
</html>
