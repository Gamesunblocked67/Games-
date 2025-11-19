<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>7th Grade Ultimate Portal</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://unpkg.com/marked/marked.min.js"></script>
<script src="https://unpkg.com/tone/build/Tone.js"></script>

<!-- Firebase Setup -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
import { getFirestore, doc, setDoc, getDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
const appId = typeof __app_id !== 'undefined' ? __app_id : 'study-hub-v8';

let db, auth;
window.appSettings = { theme: '#3b82f6', aiProvider: 'OpenAI', keys: {} };
window.ownerUnlocked = false;

if(firebaseConfig){
  const app = initializeApp(firebaseConfig);
  db = getFirestore(app);
  auth = getAuth(app);
  await signInAnonymously(auth);

  onAuthStateChanged(auth, async (user)=>{
    if(user){
      const globalConfigRef = doc(db, 'artifacts', appId, 'public_data', 'global_config');
      onSnapshot(globalConfigRef, (doc)=>{
        if(doc.exists()){
          const data = doc.data();
          if(data.keys){ window.appSettings.keys = data.keys; }
        }
      });
      const userConfigRef = doc(db,'artifacts',appId,'users',user.uid,'settings','config');
      const snap = await getDoc(userConfigRef);
      if(snap.exists()){
        const data = snap.data();
        if(data.theme) applyTheme(data.theme);
        if(data.provider) window.appSettings.aiProvider = data.provider;
      }
    }
  });

  window.saveSettingsToDb = async (scope)=>{
    if(!auth.currentUser) return;
    if(scope==='local' || scope==='both'){
      const userDocRef = doc(db,'artifacts',appId,'users',auth.currentUser.uid,'settings','config');
      await setDoc(userDocRef,{theme: window.appSettings.theme, provider: window.appSettings.aiProvider},{merge:true});
    }
    if((scope==='global'||scope==='both') && window.ownerUnlocked){
      const globalDocRef = doc(db,'artifacts',appId,'public_data','global_config');
      await setDoc(globalDocRef,{keys: window.appSettings.keys},{merge:true});
      alert("Global API Keys Saved for ALL Users!");
    } else if(scope==='local'){ alert("User Preferences Saved."); }
  };
}
</script>

<style>
:root { --primary-color:#3b82f6; }
.bg-primary{background-color:var(--primary-color);}
.text-primary{color:var(--primary-color);}
body{font-family:'Inter',sans-serif;background:#111827;color:#f3f4f6;height:100vh;overflow:hidden;display:flex;flex-direction:column;}
::-webkit-scrollbar{width:8px;}
::-webkit-scrollbar-thumb{background:#4b5563;border-radius:4px;}
::-webkit-scrollbar-track{background:#1f2937;}
#content-wrapper{flex-grow:1;overflow:hidden;position:relative;}
.tab-content{height:100%;display:flex;flex-direction:column;overflow-y:auto;padding-bottom:2rem;}
.hidden{display:none!important;}
.resource-link:hover{transform:translateX(5px);}
.youtube-container{position:relative;padding-bottom:56.25%;height:0;overflow:hidden;border-radius:.5rem;}
.youtube-container iframe{position:absolute;top:0;left:0;width:100%;height:100%;}
</style>

<script>
function applyTheme(color){document.documentElement.style.setProperty('--primary-color',color);window.appSettings.theme=color;}
function openTab(tabId,btn){
  document.querySelectorAll('.tab-content').forEach(el=>el.classList.add('hidden'));
  document.getElementById(tabId).classList.remove('hidden');
  document.querySelectorAll('nav button').forEach(b=>b.classList.remove('bg-gray-700','text-white'));
  if(btn) btn.classList.add('bg-gray-700','text-white');
  if(tabId==='resources') loadResources('Math');
}
function toggleSettings(){document.getElementById('settings-panel').classList.toggle('hidden');}
function unlockOwner(){
  const pass=document.getElementById('owner-pass').value;
  if(pass==='owner123'){
    window.ownerUnlocked=true;
    document.getElementById('owner-controls').classList.remove('hidden');
    document.getElementById('owner-lock-msg').classList.add('hidden');
    document.getElementById('gemini-key').value=window.appSettings.keys.gemini||'';
    document.getElementById('openai-key').value=window.appSettings.keys.openai||'';
  } else alert('Incorrect password.');
}
function saveLocalSettings(){
  if(window.ownerUnlocked){
    window.appSettings.keys.gemini=document.getElementById('gemini-key').value;
    window.appSettings.keys.openai=document.getElementById('openai-key').value;
  }
  window.appSettings.aiProvider=document.getElementById('ai-provider-select-settings').value;
  const scope = window.ownerUnlocked ? 'both' : 'local';
  if(window.saveSettingsToDb) window.saveSettingsToDb(scope);
  document.getElementById('ai-provider-select').value=window.appSettings.aiProvider;
  document.getElementById('settings-panel').classList.add('hidden');
}

// --- AI FUNCTION ---
async function callAI(prompt){
  const provider=window.appSettings.aiProvider;
  const key=window.appSettings.keys[provider.toLowerCase()];
  if(!key) return "API Key missing!";
  let text="";
  try{
    if(provider==='Gemini'){
      const resp=await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${key}`,{
        method:'POST',headers:{'Content-Type':'application/json'},
        body:JSON.stringify({contents:[{parts:[{text:prompt}]}]})
      });
      const data=await resp.json();
      if(data.candidates && data.candidates.length>0) text=data.candidates[0].content.parts[0].text;
      else text="No response from Gemini.";
    } else if(provider==='OpenAI'){
      const resp=await fetch('https://api.openai.com/v1/chat/completions',{
        method:'POST',headers:{'Authorization':`Bearer ${key}`,'Content-Type':'application/json'},
        body:JSON.stringify({model:"gpt-3.5-turbo",messages:[{role:"user",content:prompt}]})
      });
      const data=await resp.json();
      text=data.choices[0].message.content;
    }
  }catch(e){text="Error: "+e.message;}
  return text;
}

// AI Generators
async function runGenerator(type){
  const topic=document.getElementById(type+'-topic').value;
  if(!topic) return alert("Enter topic!");
  const resultDiv=document.getElementById(type+'-result');
  resultDiv.innerHTML='<span class="text-yellow-400">Generating...</span>';
  let prompt="";
  if(type==='flashcard') prompt=`Create 5 flashcards for ${topic}. Format as Question: Answer pairs.`;
  if(type==='quiz') prompt=`Create 3-question multiple choice quiz for ${topic} with answers.`;
  if(type==='tutor') prompt=`Explain this topic in simple terms: ${topic}.`;
  if(type==='quiz-helper') prompt=`Give tips and summary to study for this quiz: ${topic}.`;
  const res=await callAI(prompt);
  resultDiv.innerHTML=marked.parse(res);
}

// --- RESOURCE LIBRARY ---
const resources={
'Math':[
{title:"Ratios Calculator",url:"https://www.calculatorsoup.com/calculators/math/ratios.php",type:"tool"},
{title:"Integer Operations Game",url:"https://www.mathplayground.com/ASB_IntegerWarp.html",type:"game"},
{title:"Equation Solver",url:"https://www.mathpapa.com/algebra-calculator.html",type:"tool"},
{title:"Khan Academy 7th Grade Math",url:"https://www.khanacademy.org/math/cc-seventh-grade-math",type:"link"},
{title:"Desmos Graphing Calculator",url:"https://www.desmos.com/calculator",type:"tool"},
{title:"Prodigy Math Game",url:"https://www.prodigygame.com/",type:"game"}
],
'Science':[
{title:"Periodic Table",url:"https://ptable.com/",type:"tool"},
{title:"Cell Model Visualizer",url:"https://www.cellsalive.com/cells/cell_model.htm",type:"tool"},
{title:"NASA Solar System",url:"https://eyes.nasa.gov/",type:"tool"},
{title:"PhET Simulations",url:"https://phet.colorado.edu/",type:"tool"},
{title:"National Geographic Kids",url:"https://kids.nationalgeographic.com/",type:"link"},
{title:"Switch Zoo Game",url:"https://www.switchzoo.com/",type:"game"}
],
'History':[
{title:"Ancient Civilizations",url:"https://www.historyforkids.net/ancient-history.html",type:"link"},
{title:"Google Earth Voyager",url:"https://earth.google.com/web/voyager",type:"tool"},
{title:"Mission US Game",url:"https://www.mission-us.org/",type:"game"},
{title:"iCivics Government Games",url:"https://www.icivics.org/games",type:"game"}
],
'ELA':[
{title:"Thesaurus.com",url:"https://www.thesaurus.com/",type:"tool"},
{title:"Hemingway Editor",url:"https://hemingwayapp.com/",type:"tool"},
{title:"Grammarly",url:"https://www.grammarly.com/",type:"tool"},
{title:"Project Gutenberg",url:"https://www.gutenberg.org/",type:"link"},
{title:"Quill.org",url:"https://www.quill.org/",type:"tool"}
]
};

function loadResources(subject){
  const list=document.getElementById('resource-list');
  list.innerHTML='';
  document.querySelectorAll('.subject-btn').forEach(b=>b.classList.remove('bg-primary','text-white'));
  document.getElementById(`btn-${subject}`).classList.add('bg-primary','text-white');
  resources[subject].forEach(res=>{
    const item=document.createElement('div');
    item.className='bg-gray-800 p-4 rounded-lg border border-gray-700 hover:border-primary transition cursor-pointer';
    item.innerHTML=`<div class="flex justify-between items-center"><div><h4 class="font-bold text-gray-200">${res.title}</h4><span class="text-xs uppercase font-semibold text-gray-500 tracking-wider">${res.type}</span></div><svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" /></svg></div>`;
    item.onclick=()=>window.open(res.url,'_blank');
    list.appendChild(item);
  });
}

// --- CHANNEL PROMO POPUP ---
function showPromo(){
  if(localStorage.getItem('promoShown')) return;
  localStorage.setItem('promoShown','1');
  const overlay=document.createElement('div');
  overlay.className='fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50';
  overlay.innerHTML=`<div class="bg-gray-800 p-6 rounded-xl text-center max-w-sm">
    <h2 class="text-xl font-bold mb-4 text-white">Support Me!</h2>
    <p class="text-gray-300 mb-4">Subscribe to my channel: <strong>@cursedgamer2</strong></p>
    <button onclick="window.open('https://www.youtube.com/@cursedgamer2','_blank');this.parentElement.parentElement.remove();" class="bg-red-600 text-white px-4 py-2 rounded font-bold">Subscribe Now</button>
  </div>`;
  document.body.appendChild(overlay);
}

// Run promo on load
window.addEventListener('load',()=>{showPromo(); loadResources('Math');});
</script>

</head>
<body class="p-4 sm:p-6">

<header class="mb-4 flex justify-between items-center">
<h1 class="text-3xl font-bold text-white">7th Grade Ultimate Hub</h1>
<p class="text-gray-400 text-sm">Study. Focus. Achieve.</p>
<div class="flex space-x-2">
  <button onclick="toggleSettings()" class="p-2 bg-gray-800 rounded-full hover:bg-gray-700 transition">
    ⚙️
  </button>
</div>
</header>

<!-- SETTINGS PANEL -->
<div id="settings-panel" class="hidden fixed inset-0 bg-black bg-opacity-90 z-50 flex items-center justify-center">
<div class="bg-gray-800 p-6 rounded-xl max-w-md w-full border border-gray-700">
<h2 class="text-xl font-bold mb-4 text-white">Settings</h2>
<div class="space-y-4">
  <div>
    <label class="block text-sm text-gray-400">Theme Color</label>
    <input type="color" id="color-picker" class="w-full h-10 rounded cursor-pointer" oninput="applyTheme(this.value)">
  </div>
  <div>
    <label class="block text-sm text-gray-400 mb-1">AI Provider</label>
    <select id="ai-provider-select-settings" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white">
      <option value="OpenAI">OpenAI</option>
      <option value="Gemini">Gemini</option>
    </select>
  </div>
  <div class="border-t border-gray-700 pt-4">
    <h3 class="text-lg font-bold text-red-400 mb-2">Owner Zone (API Keys)</h3>
    <div id="owner-lock-msg">
      <p class="text-sm text-gray-400 mb-2">Enter password to edit global keys.</p>
      <div class="flex space-x-2">
        <input type="password" id="owner-pass" class="flex-grow bg-gray-900 border border-gray-700 rounded p-2 text-white" placeholder="Password">
        <button onclick="unlockOwner()" class="bg-gray-700 hover:bg-gray-600 text-white px-4 py-2 rounded font-bold">Unlock</button>
      </div>
    </div>
    <div id="owner-controls" class="hidden space-y-3 mt-2">
      <div>
        <label class="block text-sm text-gray-400 mb-1">Gemini API Key</label>
        <input type="password" id="gemini-key" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white">
      </div>
      <div>
        <label class="block text-sm text-gray-400 mb-1">OpenAI API Key</label>
        <input type="password" id="openai-key" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white">
      </div>
    </div>
  </div>
  <button onclick="saveLocalSettings()" class="w-full bg-primary text-white py-2 rounded font-bold hover:opacity-90 mt-4">Save & Close</button>
</div>
</div>
</div>

<!-- NAV -->
<nav class="flex space-x-2 mb-4 overflow-x-auto pb-2 border-b border-gray-700">
  <button onclick="openTab('ai-generators', this)" class="px-4 py-2 rounded-lg bg-gray-700 text-white font-medium whitespace-nowrap">⚡ AI Generators</button>
  <button onclick="openTab('resources', this)" class="px-4 py-2 rounded-lg bg-gray-800 text-gray-300 font-medium hover:bg-gray-700 whitespace-nowrap">📚 Resources</button>
</nav>

<div id="content-wrapper">

<!-- AI GENERATORS -->
<div id="ai-generators" class="tab-content p-2">
  <div class="flex flex-col h-full">
    <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">

      <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
        <h3 class="text-lg font-bold text-primary mb-2">Flashcard Maker</h3>
        <input type="text" id="flashcard-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Topic (e.g. Cell Biology)">
        <button onclick="runGenerator('flashcard')" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-1 rounded">Generate Cards</button>
        <div id="flashcard-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
      </div>

      <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
        <h3 class="text-lg font-bold text-primary mb-2">Quiz Maker</h3>
        <input type="text" id="quiz-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Topic (e.g. American Revolution)">
        <button onclick="runGenerator('quiz')" class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-1 rounded">Generate Quiz</button>
        <div id="quiz-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
      </div>

      <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
        <h3 class="text-lg font-bold text-primary mb-2">AI Tutor</h3>
        <input type="text" id="tutor-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Topic (e.g. Fractions)">
        <button onclick="runGenerator('tutor')" class="w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-1 rounded">Teach Me</button>
        <div id="tutor-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
      </div>

      <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
        <h3 class="text-lg font-bold text-primary mb-2">Quiz Study Helper</h3>
        <input type="text" id="quiz-helper-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Quiz Name or Topic">
        <button onclick="runGenerator('quiz-helper')" class="w-full bg-yellow-600 hover:bg-yellow-700 text-white font-bold py-1 rounded">Get Study Tips</button>
        <div id="quiz-helper-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
      </div>

    </div>
  </div>
</div>

<!-- RESOURCES -->
<div id="resources" class="tab-content hidden p-2">
  <div class="flex space-x-2 mb-4 overflow-x-auto">
    <button id="btn-Math" onclick="loadResources('Math')" class="subject-btn px-3 py-1 rounded bg-primary text-white font-medium">Math</button>
    <button id="btn-Science" onclick="loadResources('Science')" class="subject-btn px-3 py-1 rounded bg-gray-700 text-white font-medium">Science</button>
    <button id="btn-History" onclick="loadResources('History')" class="subject-btn px-3 py-1 rounded bg-gray-700 text-white font-medium">History</button>
    <button id="btn-ELA" onclick="loadResources('ELA')" class="subject-btn px-3 py-1 rounded bg-gray-700 text-white font-medium">ELA</button>
  </div>
  <div id="resource-list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"></div>
</div>

</div> <!-- END content-wrapper -->

</body>
</html>
