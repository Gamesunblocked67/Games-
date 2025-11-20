<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>StudyHub Ultra v3.0</title>
    <style>
        body { font-family: Arial; margin: 0; background: #111; color: #eee; }
        header { background: #222; padding: 15px; display: flex; justify-content: space-between; }
        .btn { padding: 10px 15px; background: #333; margin: 5px; cursor: pointer; border-radius: 6px; }
        input, textarea, select { width: 100%; padding: 10px; border-radius: 5px; margin: 5px 0; }
        iframe { width: 100%; height: 450px; border: none; background: #000; }
        .hidden { display: none; }
        .tool-box { background: #222; padding: 15px; border-radius: 10px; margin-top: 20px; }
    </style>
</head>

<body>
<header>
    <h2>StudyHub Ultra</h2>
    <div>
        <button class="btn" onclick="openTool('home')">Home</button>
        <button class="btn" onclick="openTool('flashcards')">Flashcards</button>
        <button class="btn" onclick="openTool('quizMaker')">Quiz Maker</button>
        <button class="btn" onclick="openTool('quizStudy')">Quiz Study</button>
        <button class="btn" onclick="openTool('tutor')">AI Tutor</button>
        <button class="btn" onclick="openTool('browser')">Mini Browser</button>
        <button class="btn" onclick="openTool('music')">Music</button>
        <button class="btn" onclick="openTool('timer')">Timer</button>
        <button class="btn" onclick="openTool('settings')">Settings</button>
    </div>
</header>

<div id="content"></div>

<script>
// ==========================
// THEO API KEY PLACEHOLDER
// ==========================
window.THEO_API_KEYS = {
    openai: "PUT-OPENAI-KEY-HERE",
    gemini: "PUT-GEMINI-KEY-HERE"
};

// ==========================
// GLOBAL PROVIDER SELECTION
// ==========================
let provider = localStorage.getItem("provider") || "openai";

// ==========================
// SIMPLE TOOL LOADER
// ==========================
function openTool(tool) {
    const c = document.getElementById("content");

    // Hide settings unless user clicks settings
    if (tool !== "settings") {
        document.getElementById("adminUnlocked")?.classList?.add("hidden");
    }

    // Home
    if (tool === "home") {
        c.innerHTML = `
            <h2>Welcome to StudyHub Ultra</h2>
            <p>Select a tool from the top menu to begin.</p>
        `;
    }

    // =======================
    // FLASHCARD MAKER
    // =======================
    if (tool === "flashcards") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Interactive Flashcard Maker</h3>
            <textarea id="fcText" placeholder="Enter topic or content"></textarea>
            <button class="btn" onclick="generateFlashcards()">Generate</button>
            <div id="flashOutput"></div>
        </div>`;
    }

    // =======================
    // QUIZ MAKER
    // =======================
    if (tool === "quizMaker") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Interactive Quiz Maker</h3>
            <textarea id="quizPrompt" placeholder="Enter topic"></textarea>
            <button class="btn" onclick="generateQuiz()">Generate Quiz</button>
            <div id="quizOutput"></div>
        </div>`;
    }

    // =======================
    // QUIZ STUDY TOOL
    // =======================
    if (tool === "quizStudy") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Quiz Study Tool</h3>
            <textarea id="studyText" placeholder="Enter questions or topic"></textarea>
            <button class="btn" onclick="generateStudyQuiz()">Start Study Quiz</button>
            <div id="studyOutput"></div>
        </div>`;
    }

    // =======================
    // AI TUTOR
    // =======================
    if (tool === "tutor") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>AI Tutor</h3>
            <select id="tutorSubject">
                <option>Math</option>
                <option>Science</option>
                <option>History</option>
                <option>English</option>
            </select>
            <textarea id="tutorQuestion" placeholder="Enter your question"></textarea>
            <button class="btn" onclick="askTutor()">Ask</button>
            <div id="tutorOutput"></div>
        </div>`;
    }

    // =======================
    // MINI BROWSER
    // =======================
    if (tool === "browser") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Mini Browser</h3>
            <input id="browseURL" placeholder="Enter a website URL">
            <button class="btn" onclick="loadBrowser()">Go</button>
            <iframe id="browserFrame"></iframe>
        </div>`;
    }

    // =======================
    // MUSIC PLAYER
    // =======================
    if (tool === "music") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Music Player</h3>
            <input id="ytLink" placeholder="YouTube link">
            <button class="btn" onclick="playYouTube()">Play YT</button>
            <br><br>
            <input type="file" id="localFile" accept="audio/*" onchange="playLocalFile()">
            <audio id="audioPlayer" controls></audio>
        </div>`;
    }

    // =======================
    // TIMER
    // =======================
    if (tool === "timer") {
        c.innerHTML = `
        <div class="tool-box">
            <h3>Timer</h3>
            <input id="timerMin" placeholder="Minutes">
            <button class="btn" onclick="startTimer()">Start</button>
            <h2 id="timerDisplay"></h2>
        </div>`;
    }

    // =======================
    // SETTINGS (ADMIN)
    // =======================
    if (tool === "settings") {
        c.innerHTML = `
        <div class="tool-box" id="adminLogin">
            <h3>Enter Admin Password</h3>
            <input id="adminPass" type="password" placeholder="Password">
            <button class="btn" onclick="verifyAdmin()">Unlock</button>
        </div>

        <div id="adminUnlocked" class="hidden">
            <div class="tool-box">
                <h3>Settings</h3>
                <label>AI Provider</label>
                <select id="providerSel" onchange="changeProvider()">
                    <option value="openai" ${provider==="openai"?"selected":""}>OpenAI</option>
                    <option value="gemini" ${provider==="gemini"?"selected":""}>Gemini</option>
                </select>
                <p>API Keys are stored in theo.js</p>
            </div>
        </div>`;
    }
}

// ============================
// ADMIN LOGIN
// ============================
function verifyAdmin() {
    const pass = document.getElementById("adminPass").value;
    if (pass === "owner123") {
        document.getElementById("adminLogin").classList.add("hidden");
        document.getElementById("adminUnlocked").classList.remove("hidden");
    } else {
        alert("Wrong password");
    }
}

// ============================
// PROVIDER CHANGE
// ============================
function changeProvider() {
    provider = document.getElementById("providerSel").value;
    localStorage.setItem("provider", provider);
}

// ============================
// MUSIC PLAYER CODE
// ============================
function playYouTube() {
    let link = document.getElementById("ytLink").value.trim();
    if (!link.includes("youtube.com") && !link.includes("youtu.be")) {
        alert("Invalid YouTube link");
        return;
    }
    let audio = document.getElementById("audioPlayer");
    audio.src = link;
    audio.play();
}
function playLocalFile() {
    const file = document.getElementById("localFile").files[0];
    if (file) {
        document.getElementById("audioPlayer").src = URL.createObjectURL(file);
    }
}

// ============================
// BROWSER LOADER
// ============================
function loadBrowser() {
    let url = document.getElementById("browseURL").value;
    if (!url.startsWith("http")) url = "https://" + url;
    document.getElementById("browserFrame").src = url;
}

// ============================
// TIMER
// ============================
function startTimer() {
    let min = parseInt(document.getElementById("timerMin").value);
    let sec = min * 60;
    let display = document.getElementById("timerDisplay");
    let timer = setInterval(() => {
        let m = Math.floor(sec / 60);
        let s = sec % 60;
        display.innerHTML = `${m}:${s.toString().padStart(2,"0")}`;
        sec--;
        if (sec < 0) { clearInterval(timer); display.innerHTML = "DONE"; }
    }, 1000);
}

// ============================
// AI REQUEST HANDLER
// ============================
async function ai(prompt) {
    let key = provider === "openai"
        ? window.THEO_API_KEYS.openai
        : window.THEO_API_KEYS.gemini;

    if (provider === "openai") {
        let r = await fetch("https://api.openai.com/v1/chat/completions", {
            method:"POST",
            headers:{ "Content-Type":"application/json", "Authorization":"Bearer "+key },
            body: JSON.stringify({
                model:"gpt-4o-mini",
                messages:[{role:"user",content:prompt}]
            })
        });
        let j = await r.json();
        return j.choices?.[0]?.message?.content || "Error.";
    }

    if (provider === "gemini") {
        let r = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${key}`, {
            method:"POST",
            headers:{ "Content-Type":"application/json" },
            body: JSON.stringify({
                contents:[{parts:[{text:prompt}]}]
            })
        });
        let j = await r.json();
        return j.candidates?.[0]?.content?.parts?.[0]?.text || "Error.";
    }
}

// ============================
// FLASHCARD MAKER
// ============================
async function generateFlashcards() {
    let text = document.getElementById("fcText").value;
    let out = document.getElementById("flashOutput");
    out.innerHTML = "Generating...";
    let res = await ai(`Make flashcards with term then answer: ${text}`);
    out.innerHTML = `<h3>Flashcards</h3>${res}`;
}

// ============================
// QUIZ MAKER
// ============================
async function generateQuiz() {
    let text = document.getElementById("quizPrompt").value;
    let out = document.getElementById("quizOutput");
    out.innerHTML = "Generating...";
    let res = await ai(`Make a multiple choice quiz with answers hidden at bottom. Topic: ${text}`);
    out.innerHTML = res;
}

// ============================
// QUIZ STUDY
// ============================
async function generateStudyQuiz() {
    let text = document.getElementById("studyText").value;
    let out = document.getElementById("studyOutput");
    out.innerHTML = "Generating...";
    let res = await ai(`Create a study quiz one question at a time. Allow user to answer. Format: Q:, Choices:, Correct:, Explanation:. Topic: ${text}`);
    out.innerHTML = res;
}

// ============================
// AI TUTOR
// ============================
async function askTutor() {
    let subject = document.getElementById("tutorSubject").value;
    let q = document.getElementById("tutorQuestion").value;
    let out = document.getElementById("tutorOutput");
    out.innerHTML = "Thinking...";
    let res = await ai(`You are an AI tutor for ${subject}. Explain simply: ${q}`);
    out.innerHTML = res;
}

// Load home by default
openTool("home");
</script>

</body>
</html>
