<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Study Hub</title>
<style>
body { font-family: Arial, sans-serif; margin: 0; padding: 0; background:#f0f0f0; }
header { background:#333; color:#fff; padding:10px; text-align:center; }
nav { display:flex; justify-content:center; gap:10px; background:#444; padding:5px; }
nav button { padding:5px 10px; cursor:pointer; }
#settingsPanel, .tool { display:none; padding:20px; background:#fff; margin:20px; border-radius:5px; }
input, select, textarea { padding:5px; margin:5px 0; width:100%; }
.flashcard, .quiz-question { margin-bottom:15px; padding:10px; border:1px solid #ccc; border-radius:5px; background:#fafafa; }
.correct { color:green; }
.wrong { color:red; }
</style>
</head>
<body>

<header><h1>Study Hub</h1></header>

<nav>
  <button onclick="showTool('flashcards')">Flashcards</button>
  <button onclick="showTool('quizMaker')">Quiz Maker</button>
  <button onclick="showTool('quizStudy')">Quiz Study Tool</button>
  <button onclick="showTool('musicPlayer')">Music Player</button>
  <button onclick="showTool('timer')">Timer</button>
  <button onclick="showSettings()">Settings</button>
</nav>

<div id="settingsPanel">
  <h2>Settings (Admin)</h2>
  <label>Password:</label>
  <input type="password" id="adminPass" placeholder="Enter password"/>
  <button onclick="checkPassword()">Enter</button>
  <div id="apiKeysPanel" style="display:none;">
    <label>OpenAI API Key:</label>
    <input type="text" id="openaiKey" placeholder="Paste OpenAI Key Here"/>
    <label>Gemini API Key:</label>
    <input type="text" id="geminiKey" placeholder="Paste Gemini Key Here"/>
    <button onclick="saveApiKeys()">Save Keys</button>
  </div>
</div>

<!-- Flashcards Tool -->
<div id="flashcards" class="tool">
  <h2>Flashcards</h2>
  <div id="flashcardContainer"></div>
  <textarea id="flashcardInput" placeholder="Enter flashcards: term - definition, one per line"></textarea>
  <button onclick="generateFlashcards()">Generate Flashcards</button>
</div>

<!-- Quiz Maker Tool -->
<div id="quizMaker" class="tool">
  <h2>Quiz Maker</h2>
  <textarea id="quizInput" placeholder="Enter questions: question | option1,option2,option3,option4 | correctOptionNumber"></textarea>
  <button onclick="generateQuiz()">Generate Quiz</button>
  <div id="quizContainer"></div>
</div>

<!-- Quiz Study Tool -->
<div id="quizStudy" class="tool">
  <h2>Quiz Study Tool</h2>
  <textarea id="studyQuizInput" placeholder="Enter study questions: question | option1,option2,option3,option4 | correctOptionNumber"></textarea>
  <button onclick="generateStudyQuiz()">Generate Study Quiz</button>
  <div id="studyQuizContainer"></div>
</div>

<!-- Music Player -->
<div id="musicPlayer" class="tool">
  <h2>Music Player</h2>
  <input type="text" id="musicURL" placeholder="YouTube URL or leave blank for local file">
  <input type="file" id="musicFile">
  <button onclick="playMusic()">Play</button>
  <audio id="audioPlayer" controls></audio>
</div>

<!-- Timer -->
<div id="timer" class="tool">
  <h2>Timer</h2>
  <input type="number" id="timerMinutes" placeholder="Minutes"/>
  <button onclick="startTimer()">Start Timer</button>
  <div id="timerDisplay">00:00</div>
</div>

<script>
// ---------- GLOBAL API KEYS ----------
window.THEO_API_KEYS = {
  openai: "", // <-- Paste OpenAI key here
  gemini: ""  // <-- Paste Gemini key here
};

// ---------- TOOL SWITCH ----------
function showTool(toolId){
  document.querySelectorAll('.tool').forEach(t=>t.style.display='none');
  document.getElementById('settingsPanel').style.display='none';
  document.getElementById(toolId).style.display='block';
}

// ---------- SETTINGS ----------
function showSettings(){
  document.querySelectorAll('.tool').forEach(t=>t.style.display='none');
  document.getElementById('settingsPanel').style.display='block';
  document.getElementById('apiKeysPanel').style.display='none';
}

function checkPassword(){
  if(document.getElementById('adminPass').value==='owner123'){
    document.getElementById('apiKeysPanel').style.display='block';
    alert("Access granted");
  } else { alert("Wrong password"); }
}

function saveApiKeys(){
  window.THEO_API_KEYS.openai = document.getElementById('openaiKey').value;
  window.THEO_API_KEYS.gemini = document.getElementById('geminiKey').value;
  alert("API keys saved globally!");
}

// ---------- FLASHCARDS ----------
function generateFlashcards(){
  const container = document.getElementById('flashcardContainer');
  container.innerHTML='';
  const lines = document.getElementById('flashcardInput').value.split('\n');
  lines.forEach(line=>{
    const [term,def] = line.split('-');
    if(term && def){
      const div = document.createElement('div');
      div.className='flashcard';
      div.innerHTML=`<b>${term.trim()}</b><br>${def.trim()}`;
      container.appendChild(div);
    }
  });
}

// ---------- QUIZ MAKER ----------
function generateQuiz(){
  const container = document.getElementById('quizContainer');
  container.innerHTML='';
  const lines = document.getElementById('quizInput').value.split('\n');
  lines.forEach((line,i)=>{
    const parts = line.split('|');
    if(parts.length===3){
      const question = parts[0].trim();
      const options = parts[1].split(',').map(o=>o.trim());
      const correct = parseInt(parts[2].trim());
      const div = document.createElement('div');
      div.className='quiz-question';
      div.innerHTML=`<b>${i+1}. ${question}</b><br>`;
      options.forEach((opt,j)=>{
        const btn = document.createElement('button');
        btn.textContent=opt;
        btn.onclick=()=>{ btn.className = (j+1===correct)? 'correct':'wrong'; }
        div.appendChild(btn);
      });
      container.appendChild(div);
    }
  });
}

// ---------- QUIZ STUDY ----------
function generateStudyQuiz(){
  const container = document.getElementById('studyQuizContainer');
  container.innerHTML='';
  const lines = document.getElementById('studyQuizInput').value.split('\n');
  lines.forEach((line,i)=>{
    const parts = line.split('|');
    if(parts.length===3){
      const question = parts[0].trim();
      const options = parts[1].split(',').map(o=>o.trim());
      const correct = parseInt(parts[2].trim());
      const div = document.createElement('div');
      div.className='quiz-question';
      div.innerHTML=`<b>${i+1}. ${question}</b><br>`;
      options.forEach((opt,j)=>{
        const btn = document.createElement('button');
        btn.textContent=opt;
        btn.onclick=()=>{ btn.className = (j+1===correct)? 'correct':'wrong'; }
        div.appendChild(btn);
      });
      container.appendChild(div);
    }
  });
}

// ---------- MUSIC PLAYER ----------
function playMusic(){
  const player = document.getElementById('audioPlayer');
  const url = document.getElementById('musicURL').value;
  const file = document.getElementById('musicFile').files[0];
  if(file){
    player.src=URL.createObjectURL(file);
  } else if(url){
    player.src=url;
  }
  player.play();
}

// ---------- TIMER ----------
let timerInterval;
function startTimer(){
  clearInterval(timerInterval);
  let mins = parseInt(document.getElementById('timerMinutes').value);
  let secs = mins*60;
  const display = document.getElementById('timerDisplay');
  timerInterval = setInterval(()=>{
    let m = Math.floor(secs/60);
    let s = secs%60;
    display.textContent = `${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
    if(secs<=0) clearInterval(timerInterval);
    secs--;
  },1000);
}

</script>

</body>
</html>
