<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>7th Grade Ultimate Hub</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://unpkg.com/tone@14.9.15/build/Tone.js"></script>
<style>
:root {--primary-color:#3b82f6;}
.bg-primary {background-color: var(--primary-color);}
.text-primary {color: var(--primary-color);}
body{font-family:'Inter',sans-serif;background:#111827;color:#f3f4f6;height:100vh;display:flex;flex-direction:column;}
header, nav{display:flex;align-items:center;justify-content:space-between;}
nav button{padding:0.5rem 1rem;margin-right:0.25rem;border-radius:0.25rem;background:#1f2937;color:#f3f4f6;transition:0.2s;}
nav button:hover{background:var(--primary-color);}
.tab-content{flex-grow:1;overflow-y:auto;padding:1rem;}
.hidden{display:none;}
.resource-card{padding:0.5rem 1rem;margin:0.5rem 0;border:1px solid #374151;border-radius:0.5rem;transition:0.2s;}
.resource-card:hover{border-color:var(--primary-color);cursor:pointer;}
</style>
</head>
<body>

<header class="p-4 flex justify-between items-center bg-gray-800">
    <div>
        <h1 class="text-2xl font-bold text-white">7th Grade Hub</h1>
        <p class="text-gray-400 text-sm">Study, Play & Learn</p>
    </div>
    <div class="flex space-x-2">
        <button onclick="toggleSettings()">⚙️ Settings</button>
        <button onclick="unlockAdmin()">🔒 Admin</button>
    </div>
</header>

<nav class="p-2 bg-gray-900">
    <button onclick="openTab('resources', this)">Resources</button>
    <button onclick="openTab('ai-tools', this)">AI Tools</button>
    <button onclick="openTab('focus', this)">Focus</button>
</nav>

<div id="content-wrapper" class="flex-grow">
    <!-- Resources Tab -->
    <div id="resources" class="tab-content hidden">
        <div class="mb-2 flex space-x-2">
            <button id="btn-Math" class="subject-btn bg-primary" onclick="loadResources('Math')">Math</button>
            <button id="btn-Science" class="subject-btn" onclick="loadResources('Science')">Science</button>
            <button id="btn-History" class="subject-btn" onclick="loadResources('History')">History</button>
            <button id="btn-ELA" class="subject-btn" onclick="loadResources('ELA')">ELA</button>
        </div>
        <div id="resource-list"></div>
    </div>

    <!-- AI Tools Tab -->
    <div id="ai-tools" class="tab-content hidden">
        <div class="mb-2">
            <label class="block mb-1">Select AI Provider:</label>
            <select id="ai-provider-select" class="p-2 rounded bg-gray-800 text-white">
                <option value="Gemini">Gemini</option>
                <option value="OpenAI">OpenAI</option>
            </select>
        </div>

        <div class="mb-2">
            <label>Flashcard Topic:</label>
            <input id="flashcard-topic" class="p-2 w-full rounded bg-gray-800 text-white" placeholder="Enter topic">
            <button class="mt-1 p-2 bg-primary rounded" onclick="runGenerator('flashcard')">Generate Flashcards</button>
            <div id="flashcard-result" class="mt-2"></div>
        </div>

        <div class="mb-2">
            <label>Quiz Topic:</label>
            <input id="quiz-topic" class="p-2 w-full rounded bg-gray-800 text-white" placeholder="Enter topic">
            <button class="mt-1 p-2 bg-primary rounded" onclick="runGenerator('quiz')">Generate Quiz</button>
            <div id="quiz-result" class="mt-2"></div>
        </div>

        <div class="mb-2">
            <label>Tutor Question:</label>
            <textarea id="ai-input" class="p-2 w-full rounded bg-gray-800 text-white" placeholder="Ask a question"></textarea>
            <button class="mt-1 p-2 bg-primary rounded" onclick="callAI()">Ask AI Tutor</button>
            <div id="ai-output" class="mt-2"></div>
        </div>
    </div>

    <!-- Focus Tab -->
    <div id="focus" class="tab-content hidden">
        <div class="mb-2">
            <label>Timer (minutes):</label>
            <input id="timer-input" type="number" class="p-2 rounded bg-gray-800 text-white" placeholder="e.g. 25">
            <button class="p-2 bg-primary rounded" onclick="startTimer(document.getElementById('timer-input').value)">Start</button>
            <button class="p-2 bg-gray-700 rounded" onclick="resetTimer()">Reset</button>
            <div id="timer-display" class="text-xl mt-1">00:00</div>
        </div>

        <div class="mb-2">
            <button id="noise-btn" class="p-2 bg-primary rounded" onclick="toggleNoise('pink')">Start Focus Noise</button>
        </div>

        <div class="mb-2">
            <label>Music URL:</label>
            <input id="music-input" class="p-2 w-full rounded bg-gray-800 text-white" placeholder="Direct MP3 URL only">
            <button class="mt-1 p-2 bg-primary rounded" onclick="playMusic()">Play Music</button>
            <audio id="audio-player" controls class="mt-2 hidden"></audio>
        </div>
    </div>
</div>

<!-- Settings Panel -->
<div id="settings-panel" class="fixed top-10 right-10 p-4 bg-gray-800 rounded shadow-lg hidden w-80">
    <h2 class="text-lg font-bold mb-2">Settings</h2>
    <label>Theme Color:</label>
    <input id="color-picker" type="color" class="w-full h-8 mb-2" onchange="applyTheme(this.value)">
    <label>AI Provider:</label>
    <select id="ai-provider-select-settings" class="p-2 w-full rounded bg-gray-700 mb-2">
        <option value="Gemini">Gemini</option>
        <option value="OpenAI">OpenAI</option>
    </select>
    <h3 class="font-semibold mt-2 mb-1">API Keys:</h3>
    <label>Gemini API Key:</label>
    <input id="gemini-key" type="text" class="p-2 w-full rounded bg-gray-700 mb-1" placeholder="Enter Gemini API key">
    <label>OpenAI API Key:</label>
    <input id="openai-key" type="text" class="p-2 w-full rounded bg-gray-700 mb-2" placeholder="Enter OpenAI API key">
    <button class="p-2 bg-primary rounded w-full mt-2" onclick="saveLocalSettings()">Save Settings</button>
</div>

<!-- Admin Unlock -->
<div id="admin-panel" class="fixed top-40 left-1/2 transform -translate-x-1/2 p-4 bg-gray-900 rounded shadow-lg hidden w-80">
    <h2 class="text-lg font-bold mb-2">Admin Panel 🔑</h2>
    <label>Password:</label>
    <input id="admin-pass" type="password" class="p-2 w-full rounded bg-gray-700 mb-2">
    <button class="p-2 bg-primary rounded w-full" onclick="unlockAdminPanel()">Unlock Admin</button>
    <div id="admin-controls" class="hidden mt-2">
        <button class="p-2 bg-green-600 rounded w-full mb-1" onclick="addResource(currentSubject)">Add Resource</button>
        <button class="p-2 bg-blue-600 rounded w-full mb-1" onclick="addGame(currentSubject)">Add Game</button>
    </div>
</div>

<script>
let currentSubject='Math';
let timerInterval,timeLeft;
let noiseSynth=null;

// Theme
function applyTheme(color){document.documentElement.style.setProperty('--primary-color',color);}

// Tabs
function openTab(id,btn){
    document.querySelectorAll('.tab-content').forEach(el=>el.classList.add('hidden'));
    document.getElementById(id).classList.remove('hidden');
    if(btn){
        document.querySelectorAll('nav button').forEach(b=>b.classList.remove('bg-primary'));
        btn.classList.add('bg-primary');
    }
}

// Resources
const appSettings = {
    keys:{Gemini:'',OpenAI:''},
    resources:{
        Math:[
            {title:'Khan Academy Algebra',url:'https://www.khanacademy.org/math/algebra',type:'Video/Exercises'},
            {title:'Math Is Fun - Fractions',url:'https://www.mathsisfun.com/fractions.html',type:'Website'},
            {title:'IXL Geometry Practice',url:'https://www.ixl.com/math/geometry',type:'Exercises'}
        ],
        Science:[
            {title:'NASA Kids Club',url:'https://www.nasa.gov/kidsclub/index.html',type:'Website'},
            {title:'Crash Course Biology',url:'https://www.youtube.com/playlist?list=PL3EED4C1D684D3ADF',type:'Video'},
            {title:'PhET Interactive Simulations',url:'https://phet.colorado.edu',type:'Simulation'}
        ],
        History:[
            {title:'History for Kids',url:'https://www.historyforkids.net/',type:'Website'},
            {title:'Crash Course World History',url:'https://www.youtube.com/playlist?list=PLBDA2E52FB1EF80C9',type:'Video'},
            {title:'Smithsonian History Resources',url:'https://www.si.edu/spotlight/education',type:'Website'}
        ],
        ELA:[
            {title:'ReadWorks',url:'https://www.readworks.org/',type:'Reading/Exercises'},
            {title:'Project Gutenberg',url:'https://www.gutenberg.org/',type:'Books'},
            {title:'Vocabulary.com',url:'https://www.vocabulary.com/',type:'Exercises'}
        ]
    },
    games:{Math:[],Science:[],History:[],ELA:[]}
};
function loadResources(subject){
    currentSubject=subject;
    const list=document.getElementById('resource-list');
    list.innerHTML='';
    document.querySelectorAll('.subject-btn').forEach(b=>b.classList.remove('bg-primary'));
    document.getElementById(`btn-${subject}`).classList.add('bg-primary');
    appSettings.resources[subject].forEach(r=>{
        const div=document.createElement('div');
        div.className='resource-card';
        div.textContent=`[${r.type}] ${r.title}`;
        div.onclick=()=>window.open(r.url,'_blank');
        list.appendChild(div);
    });
    appSettings.games[subject].forEach(g=>{
        const div=document.createElement('div');
        div.className='resource-card';
        div.textContent=`[Game] ${g.title}`;
        div.onclick=()=>window.open(g.url,'_blank');
        list.appendChild(div);
    });
}

// Timer
function startTimer(min){clearInterval(timerInterval);timeLeft=min*60;updateTimer();timerInterval=setInterval(()=>{timeLeft--;updateTimer();if(timeLeft<=0){clearInterval(timerInterval);alert("Time's up!");}},1000);}
function updateTimer(){const m=Math.floor(timeLeft/60),s=timeLeft%60;document.getElementById('timer-display').textContent=`${m}:${s<10?'0':''}${s}`;}
function resetTimer(){clearInterval(timerInterval);document.getElementById('timer-display').textContent='00:00';}

// Noise
function toggleNoise(type){
    if(noiseSynth){noiseSynth.stop();noiseSynth.dispose();noiseSynth=null;document.getElementById('noise-btn').textContent='Start Focus Noise';return;}
    Tone.start();
    noiseSynth=new Tone.Noise(type).toDestination();
    noiseSynth.start();
    document.getElementById('noise-btn').textContent='Stop Noise';
}

// Music
function playMusic(){
    const input=document.getElementById('music-input').value;
    const audio=document.getElementById('audio-player');
    audio.src=input;
    audio.classList.remove('hidden');
    audio.play();
}

// AI Tools
async function callAI(){
    const prompt=document.getElementById('ai-input').value;
    const provider=document.getElementById('ai-provider-select').value;
    const output=document.getElementById('ai-output');
    if(!prompt){output.innerHTML='Enter a question'; return;}
    const key=appSettings.keys[provider];
    if(!key){output.innerHTML='Set API Key in settings';return;}
    output.innerHTML='Thinking...';
    try{
        let text='';
        if(provider==='OpenAI'){
            const res=await fetch('https://api.openai.com/v1/chat/completions',{
                method:'POST',
                headers:{'Content-Type':'application/json','Authorization':`Bearer ${key}`},
                body:JSON.stringify({model:'gpt-3.5-turbo',messages:[{role:'user',content:prompt}]})
            });
            const data=await res.json();
            text=data.choices[0].message.content;
        } else {text='Gemini API call placeholder';}
        output.innerHTML=text;
    }catch(e){output.innerHTML='Error:'+e.message;}
}
async function runGenerator(type){
    const topic=document.getElementById(type+'-topic').value;
    if(!topic) return alert('Enter topic');
    const provider=document.getElementById('ai-provider-select').value;
    const key=appSettings.keys[provider];
    const output=document.getElementById(type+'-result');
    if(!key){output.innerHTML='Set API Key';return;}
    output.innerHTML='Generating...';
    try{
        let prompt='';
        if(type==='flashcard') prompt=`Create 5 flashcards for ${topic} as Q/A.`;
        if(type==='quiz') prompt=`Create 3 multiple-choice questions for ${topic} with answers.`;
        let text='';
        if(provider==='OpenAI'){
            const res=await fetch('https://api.openai.com/v1/chat/completions',{
                method:'POST',
                headers:{'Content-Type':'application/json','Authorization':`Bearer ${key}`},
                body:JSON.stringify({model:'gpt-3.5-turbo',messages:[{role:'user',content:prompt}]})
            });
            const data=await res.json();
            text=data.choices[0].message.content;
        } else text='Gemini API call placeholder';
        output.innerHTML=text;
    }catch(e){output.innerHTML='Error:'+e.message;}
}

// Settings & Admin
function toggleSettings(){document.getElementById('settings-panel').classList.toggle('hidden');}
function saveLocalSettings(){
    appSettings.aiProvider=document.getElementById('ai-provider-select-settings').value;
    appSettings.keys.Gemini=document.getElementById('gemini-key').value;
    appSettings.keys.OpenAI=document.getElementById('openai-key').value;
    alert('Settings saved');
    document.getElementById('settings-panel').classList.add('hidden');
}

function unlockAdmin(){document.getElementById('admin-panel').classList.remove('hidden');}
function unlockAdminPanel(){if(document.getElementById('admin-pass').value==='admin123'){document.getElementById('admin-controls').classList.remove('hidden');alert('Admin unlocked');} else alert('Wrong password');}
function addResource(subject){
    const title=prompt('Resource title?'); const url=prompt('Resource URL?'); const type=prompt('Type?'); if(title && url && type){appSettings.resources[subject].push({title,url,type}); loadResources(subject);}
}
function addGame(subject){
    const title=prompt('Game title?'); const url=prompt('Game URL?'); if(title && url){appSettings.games[subject].push({title,url,type:'game'}); loadResources(subject);}
}

// Initialize
openTab('resources');
loadResources('Math');
</script>

</body>
</html>
