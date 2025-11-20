<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>7th Grade Ultimate Hub — Local Build</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://unpkg.com/tone@14.9.15/build/Tone.js"></script>
<style>
  :root { --primary: #3b82f6; }
  body { background:#0b1220; color:#367588; font-family:Inter,system-ui,Segoe UI,Arial; height:100vh; margin:0; display:flex; flex-direction:column; }
  .hidden { display:none!important; }
  .tab-content { display:none; height:100%; overflow:auto; padding:1rem; }
  .active { display:block; }
  .accent { color:var(--primary); }
  .card { background:#367588; border:1px solid #367588; padding:1rem; border-radius:.5rem; }
  .truncate-1 { overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .notice { background:#7c2dff; color:white; padding:.5rem 1rem; border-radius:.5rem; }
  /* mini browser */
  #mini-browser-modal iframe { width:100%; height:60vh; border:0; border-radius:.375rem; }
</style>
</head>
<body class="p-4">

<div id="support-popup" class="fixed inset-0 bg-black bg-opacity-80 z-50 flex items-center justify-center hidden">
  <div class="bg-gray-900 p-6 rounded-xl w-full max-w-lg border border-gray-700 text-center relative">
    <button id="support-close-x" class="absolute right-3 top-3 text-gray-400 hover:text-white">✕</button>
    <h2 class="text-2xl font-bold mb-2">Support Me — Subscribe!</h2>
    <p class="text-gray-300 mb-4">Subscribe to my channel to support the project: <span class="font-semibold">@cursedgamer2</span></p>
    <div class="flex justify-center gap-3">
      <button id="subscribe-open" class="bg-red-600 hover:bg-red-700 px-4 py-2 rounded font-bold">Subscribe @cursedgamer2</button>
      <button id="support-dismiss" class="bg-gray-700 hover:bg-gray-600 px-4 py-2 rounded">Dismiss</button>
    </div>
  </div>
</div>

<header class="flex items-center justify-between mb-4">
  <div>
    <h1 class="text-2xl font-bold">7th Grade Ultimate Hub</h1>
    <p class="text-sm text-gray-400">Study • Focus • Achieve</p>
  </div>
  <div class="flex items-center gap-3">
    <button id="open-settings" class="p-2 rounded bg-gray-800 hover:bg-gray-700">⚙️</button>
    <button id="open-admin" class="p-2 rounded bg-gray-800 hover:bg-gray-700">🛠 Admin</button>
    <button id="open-mini-browser" class="p-2 rounded bg-gray-800 hover:bg-gray-700">🌐 Mini Browser</button>
  </div>
</header>

<div id="dec1-notice" class="mb-4 hidden">
  <div class="notice flex items-center justify-between">
    <div>
      <strong>Notice:</strong> Effective Dec 1 — you must be subscribed to @cursedgamer2 to use the AI Quiz Maker & AI Tutor.
      (Click "I Subscribed" after subscribing.) 
    </div>
    <div class="flex items-center gap-2">
      <button id="i-subscribed" class="bg-green-600 px-3 py-1 rounded font-bold">I Subscribed</button>
      <button id="dismiss-notice" class="bg-gray-800 px-3 py-1 rounded">Dismiss</button>
    </div>
  </div>
</div>

<nav class="flex gap-2 mb-4">
  <button data-tab="ai" class="nav-btn px-3 py-2 rounded bg-gray-800 hover:bg-gray-700">⚡ AI Tools</button>
  <button data-tab="resources" class="nav-btn px-3 py-2 rounded bg-gray-800 hover:bg-gray-700">📚 Resources</button>
  <button data-tab="games" class="nav-btn px-3 py-2 rounded bg-gray-800 hover:bg-gray-700">🕹️ Study Games</button>
  <button data-tab="focus" class="nav-btn px-3 py-2 rounded bg-gray-800 hover:bg-gray-700">🎧 Focus</button>
  <button data-tab="dashboard" class="nav-btn px-3 py-2 rounded bg-gray-800 hover:bg-gray-700">📂 Dashboard</button>
</nav>

<main id="main" class="flex-1 overflow-auto">

    <section id="tab-ai" class="tab-content active">
    <div class="grid md:grid-cols-2 gap-4">
            <div class="card">
        <h3 class="font-bold mb-2">AI Flashcard Maker</h3>
        <input id="flashcard-topic" placeholder="Topic (e.g. Cell Biology)" class="w-full mb-2 p-2 bg-gray-900 rounded" />
        <div class="flex gap-2">
          <button id="gen-flash" class="bg-indigo-600 px-3 py-2 rounded">Generate</button>
          <button id="save-flash" class="bg-gray-700 px-3 py-2 rounded">Save</button>
        </div>
        <div id="flash-result" class="mt-3 text-sm text-gray-200 max-h-48 overflow-auto"></div>
      </div>

            <div class="card">
        <h3 class="font-bold mb-2">AI Quiz Maker <span id="quiz-locked-badge" class="text-sm ml-2 text-yellow-400 hidden">Locked by Dec 1 rule</span></h3>
        <input id="quiz-topic" placeholder="Topic (e.g. American Revolution)" class="w-full mb-2 p-2 bg-gray-900 rounded" />
        <div class="flex gap-2">
          <button id="gen-quiz" class="bg-green-600 px-3 py-2 rounded">Generate</button>
          <button id="save-quiz" class="bg-gray-700 px-3 py-2 rounded">Save</button>
        </div>
        <div id="quiz-result" class="mt-3 text-sm text-gray-200 max-h-48 overflow-auto"></div>
      </div>

            <div class="card">
        <h3 class="font-bold mb-2">AI Tutor <span id="tutor-locked-badge" class="text-sm ml-2 text-yellow-400 hidden">Locked by Dec 1 rule</span></h3>
        <input id="tutor-topic" placeholder="Ask a question or enter a topic" class="w-full mb-2 p-2 bg-gray-900 rounded" />
        <div class="flex gap-2">
          <button id="gen-tutor" class="bg-purple-600 px-3 py-2 rounded">Ask Tutor</button>
          <button id="save-tutor" class="bg-gray-700 px-3 py-2 rounded">Save</button>
        </div>
        <div id="tutor-result" class="mt-3 text-sm text-gray-200 max-h-48 overflow-auto"></div>
      </div>

            <div class="card">
        <h3 class="font-bold mb-2">Quiz Study Helper</h3>
        <input id="helper-topic" placeholder="Quiz name or topic to study" class="w-full mb-2 p-2 bg-gray-900 rounded" />
        <div class="flex gap-2">
          <button id="gen-helper" class="bg-yellow-500 px-3 py-2 rounded">Get Study Tips</button>
          <button id="save-helper" class="bg-gray-700 px-3 py-2 rounded">Save</button>
        </div>
        <div id="helper-result" class="mt-3 text-sm text-gray-200 max-h-48 overflow-auto"></div>
      </div>
    </div>
  </section>

    <section id="tab-resources" class="tab-content">
    <div class="flex gap-3 mb-3">
      <button id="btn-Math" class="px-3 py-1 rounded bg-primary">Math</button>
      <button id="btn-Science" class="px-3 py-1 rounded bg-gray-800">Science</button>
      <button id="btn-History" class="px-3 py-1 rounded bg-gray-800">History</button>
      <button id="btn-ELA" class="px-3 py-1 rounded bg-gray-800">ELA</button>
    </div>
    <div id="resource-list" class="grid sm:grid-cols-2 md:grid-cols-3 gap-4"></div>
  </section>

    <section id="tab-games" class="tab-content">
    <h3 class="font-bold mb-3">Study Games (Open in new tab)</h3>
    <div id="game-list" class="grid sm:grid-cols-2 md:grid-cols-3 gap-4"></div>
  </section>

    <section id="tab-focus" class="tab-content">
    <div class="grid md:grid-cols-2 gap-4">
      <div class="card">
        <h3 class="font-bold mb-2">Focus Timer</h3>
        <div class="flex gap-2 mb-2">
          <button onclick="startTimer(25)" class="bg-blue-600 px-3 py-2 rounded">25m</button>
          <button onclick="startTimer(50)" class="bg-blue-600 px-3 py-2 rounded">50m</button>
          <button onclick="resetTimer()" class="bg-gray-700 px-3 py-2 rounded">Reset</button>
        </div>
        <div id="timer-display" class="text-3xl font-bold">00:00</div>
      </div>

      <div class="card">
        <h3 class="font-bold mb-2">Focus Noise (Tone.js)</h3>
        <button id="noise-btn" class="bg-gray-700 px-3 py-2 rounded">Start Focus Noise</button>
      </div>
    </div>
  </section>

    <section id="tab-dashboard" class="tab-content">
    <h3 class="font-bold mb-3">My Saved Study Items</h3>
    <div id="dashboard-list" class="space-y-3"></div>
    <div class="mt-4">
      <button id="export-data" class="bg-gray-700 px-3 py-2 rounded">Export JSON</button>
      <button id="import-data" class="bg-gray-700 px-3 py-2 rounded">Import JSON</button>
      <input id="import-file" type="file" accept="application/json" class="hidden"/>
    </div>
  </section>

</main>

<div id="settings-modal" class="fixed inset-0 bg-black bg-opacity-70 z-40 flex items-center justify-center hidden">
  <div class="bg-gray-900 p-6 rounded-xl w-full max-w-md border border-gray-700">
    <h3 class="text-xl font-bold mb-3">Settings</h3>
    <div class="mb-3">
      <label class="block text-sm text-gray-300 mb-1">Theme Color</label>
      <input id="color-picker" type="color" class="w-full h-10 rounded"/>
    </div>
    <div class="mb-3">
      <label class="block text-sm text-gray-300 mb-1">AI Provider</label>
      <select id="ai-provider-select-settings" class="w-full p-2 rounded bg-gray-800">
        <option value="OpenAI">OpenAI</option>
        <option value="Gemini">Gemini</option>
      </select>
    </div>

    <div class="border-t border-gray-800 pt-3">
      <h4 class="font-bold text-red-400 mb-2">Owner Zone (API Keys)</h4>
      <div id="owner-lock-msg">
        <p class="text-sm text-gray-400 mb-2">Enter password to edit global keys.</p>
        <div class="flex gap-2">
          <input id="owner-pass" type="password" placeholder="Password" class="flex-1 p-2 rounded bg-gray-800"/>
          <button id="unlock-owner-btn" class="bg-gray-700 px-3 py-2 rounded">Unlock</button>
        </div>
      </div>
      <div id="owner-controls" class="mt-3 hidden space-y-2">
        <div>
          <label class="text-sm text-gray-300">OpenAI Key</label>
          <input id="openai-key" type="password" placeholder="sk-..." class="w-full p-2 rounded bg-gray-800"/>
        </div>
        <div>
          <label class="text-sm text-gray-300">Gemini Key</label>
          <input id="gemini-key" type="password" placeholder="Gemini key" class="w-full p-2 rounded bg-gray-800"/>
        </div>
        <div class="flex gap-2">
          <button id="save-keys" class="bg-green-600 px-3 py-2 rounded">Save Keys</button>
          <button id="export-keys" class="bg-gray-700 px-3 py-2 rounded">Export Keys</button>
          <button id="import-keys" class="bg-gray-700 px-3 py-2 rounded">Import Keys</button>
        </div>
      </div>
    </div>

    <div class="mt-4 flex justify-end gap-2">
      <button id="settings-save" class="bg-primary px-4 py-2 rounded">Save & Close</button>
      <button id="settings-cancel" class="bg-gray-700 px-4 py-2 rounded">Cancel</button>
    </div>
  </div>
</div>

<div id="admin-modal" class="fixed inset-0 bg-black bg-opacity-70 z-40 flex items-center justify-center hidden">
  <div class="bg-gray-900 p-6 rounded-xl w-full max-w-lg border border-gray-700">
    <h3 class="text-xl font-bold mb-3">Admin Panel</h3>
    <p class="text-sm text-gray-300 mb-3">Owner: use password <code>owner123</code> to unlock settings in Settings panel.</p>
    <div class="grid md:grid-cols-2 gap-4">
      <div class="card">
        <h4 class="font-bold mb-2">Site Controls</h4>
        <button id="clear-dashboard" class="bg-red-600 px-3 py-2 rounded w-full">Clear All Dashboard Items</button>
      </div>
      <div class="card">
        <h4 class="font-bold mb-2">Quick Keys</h4>
        <button id="fill-demo-keys" class="bg-gray-700 px-3 py-2 rounded w-full">Fill Demo Keys (placeholder)</button>
      </div>
    </div>

    <div class="mt-4 flex justify-end">
      <button id="admin-close" class="bg-gray-700 px-3 py-2 rounded">Close</button>
    </div>
  </div>
</div>

<div id="mini-browser-modal" class="fixed inset-0 bg-black bg-opacity-60 z-50 flex items-start justify-center hidden">
  <div class="bg-gray-900 rounded-xl w-full max-w-3xl mt-8 p-4 border border-gray-700">
    <div class="flex gap-2 mb-3">
      <input id="mini-url" class="flex-1 p-2 rounded bg-gray-800" placeholder="https://example.com"/>
      <button id="mini-go" class="bg-primary px-3 py-2 rounded">Go</button>
      <button id="mini-close" class="bg-gray-700 px-3 py-2 rounded">Close</button>
    </div>
    <iframe id="mini-frame" src="about:blank" class="w-full h-[60vh] rounded"></iframe>
  </div>
</div>

<script>
/* ---------------------
   STATE & UTILITIES
   --------------------- */
const STORAGE_PREFIX = 'sg2_'; // study hub local prefix
const KEYS_STORE = STORAGE_PREFIX + 'keys';
const SETTINGS_STORE = STORAGE_PREFIX + 'settings';
const DASHBOARD_STORE = STORAGE_PREFIX + 'dashboard';
const SUBSCRIBED_FLAG = STORAGE_PREFIX + 'subscribed';

// default app state
const app = {
  aiProvider: 'OpenAI',
  keys: { openai: '', gemini: '' },
};
function saveSettingsToStorage(){ localStorage.setItem(SETTINGS_STORE, JSON.stringify({ aiProvider: app.aiProvider })); }
function loadSettingsFromStorage(){
  const s = localStorage.getItem(SETTINGS_STORE);
  if(s) {
    try{ const o=JSON.parse(s); if(o.aiProvider) app.aiProvider=o.aiProvider; document.getElementById('ai-provider-select-settings').value = app.aiProvider; }
    catch(e){}
  }
}
function saveKeysToStorage(){ localStorage.setItem(KEYS_STORE, JSON.stringify(app.keys)); }
function loadKeysFromStorage(){ const s = localStorage.getItem(KEYS_STORE); if(s){ try{ app.keys = JSON.parse(s); }catch(e){} } }

// Utility function to apply theme color from storage on load
function applyThemeFromPicker(){
  const color = localStorage.getItem(STORAGE_PREFIX + 'themeColor') || '#3b82f6';
  document.documentElement.style.setProperty('--primary', color);
  const picker = document.getElementById('color-picker');
  if(picker) picker.value = color;
}

/* ---------------------
   STARTUP
   --------------------- */
loadKeysFromStorage();
loadSettingsFromStorage();
document.addEventListener('DOMContentLoaded', ()=> {
  // show popup once per session unless dismissed earlier
  if(!sessionStorage.getItem('suppressedPopup')) {
    document.getElementById('support-popup').classList.remove('hidden');
  }
  setupNav();
  setupResourcesAndGames();
  setupDecNotice();
  applyThemeFromPicker();
  renderDashboard();
});

/* ---------------------
   NAV & TABS
   --------------------- */
function setupNav(){
  document.querySelectorAll('.nav-btn').forEach(b=>{
    b.addEventListener('click', ()=> {
      const tab = b.getAttribute('data-tab');
      showTab(tab);
    });
  });
  // show default 'ai'
  showTab('ai');

  // settings open
  document.getElementById('open-settings').addEventListener('click', ()=> openSettingsModal());
  document.getElementById('open-admin').addEventListener('click', ()=> openAdminModal());
  document.getElementById('open-mini-browser').addEventListener('click', ()=> openMiniBrowser());
}

/* show tab by id */
function showTab(name){
  document.querySelectorAll('.tab-content').forEach(s=>s.classList.remove('active'));
  const el = document.getElementById('tab-' + (name==='ai'?'ai':name));
  if(el) el.classList.add('active');
}

/* ---------------------
   SUBSCRIBE POPUP
   --------------------- */
document.getElementById('subscribe-open').addEventListener('click', ()=> {
  window.open('https://www.youtube.com/@cursedgamer2','_blank');
  // don't auto-mark subscribed - user must click I Subscribed banner
});
document.getElementById('support-dismiss').addEventListener('click', ()=> {
  sessionStorage.setItem('suppressedPopup','1');
  document.getElementById('support-popup').classList.add('hidden');
});
document.getElementById('support-close-x').addEventListener('click', ()=> {
  sessionStorage.setItem('suppressedPopup','1');
  document.getElementById('support-popup').classList.add('hidden');
});

/* ---------------------
   DEC 1 NOTICE / SUBSCRIPTION RULE
   --------------------- */
function setupDecNotice(){
  const now = new Date();
  const dec1 = new Date(now.getFullYear(), 11, 1); // Dec 1 (month is 0-indexed)
  const noticeEl = document.getElementById('dec1-notice');
  if(now >= dec1){
    // show notice if not dismissed permanently
    const dismissed = localStorage.getItem(STORAGE_PREFIX + 'noticeDismissed');
    if(!dismissed) noticeEl.classList.remove('hidden');
    // show locked badges
    document.getElementById('quiz-locked-badge').classList.remove('hidden');
    document.getElementById('tutor-locked-badge').classList.remove('hidden');
  }

  document.getElementById('dismiss-notice').addEventListener('click', ()=> {
    localStorage.setItem(STORAGE_PREFIX + 'noticeDismissed','1');
    noticeEl.classList.add('hidden');
  });
  document.getElementById('i-subscribed').addEventListener('click', ()=> {
    // can't verify; user confirms by clicking
    localStorage.setItem(SUBSCRIBED_FLAG,'1');
    alert('Thanks! You are marked as subscribed locally. You can now use the Quiz Maker and Tutor.');
    noticeEl.classList.add('hidden');
  });
}

/* check subscription enforcement */
function isSubscribedConfirmed(){
  return !!localStorage.getItem(SUBSCRIBED_FLAG);
}

/* ---------------------
   SETTINGS MODAL
   --------------------- */
function openSettingsModal(){
  // sync UI with state
  const currentColor = getComputedStyle(document.documentElement).getPropertyValue('--primary').trim() || '#3b82f6';
  document.getElementById('color-picker').value = currentColor;
  document.getElementById('ai-provider-select-settings').value = app.aiProvider;
  // load keys if exist
  loadKeysFromStorage();
  document.getElementById('openai-key').value = app.keys.openai || '';
  document.getElementById('gemini-key').value = app.keys.gemini || '';
  document.getElementById('settings-modal').classList.remove('hidden');
}
function closeSettingsModal(){ document.getElementById('settings-modal').classList.add('hidden'); }

document.getElementById('settings-cancel').addEventListener('click', ()=> closeSettingsModal());
document.getElementById('settings-save').addEventListener('click', ()=>{
  // apply theme
  const color = document.getElementById('color-picker').value;
  document.documentElement.style.setProperty('--primary', color);
  localStorage.setItem(STORAGE_PREFIX + 'themeColor', color); // save color
  // provider
  app.aiProvider = document.getElementById('ai-provider-select-settings').value;
  saveSettingsToStorage();
  // if owner unlocked, also save keys automatically when they click Save
  if(window.ownerUnlocked){
    app.keys.openai = document.getElementById('openai-key').value.trim();
    app.keys.gemini = document.getElementById('gemini-key').value.trim();
    saveKeysToStorage();
  }
  closeSettingsModal();
});

/* Owner unlock / keys */
document.getElementById('unlock-owner-btn').addEventListener('click', ()=>{
  const pass = document.getElementById('owner-pass').value;
  if(pass === 'owner123'){
    window.ownerUnlocked = true;
    document.getElementById('owner-controls').classList.remove('hidden');
    document.getElementById('owner-lock-msg').classList.add('hidden');
    // preload keys into fields
    loadKeysFromStorage();
    document.getElementById('openai-key').value = app.keys.openai || '';
    document.getElementById('gemini-key').value = app.keys.gemini || '';
  } else {
    alert('Incorrect password.');
  }
});
document.getElementById('save-keys').addEventListener('click', ()=>{
  if(!window.ownerUnlocked){ alert('Unlock with owner password first.'); return; }
  app.keys.openai = document.getElementById('openai-key').value.trim();
  app.keys.gemini = document.getElementById('gemini-key').value.trim();
  saveKeysToStorage();
  alert('Keys saved locally.');
});
document.getElementById('export-keys').addEventListener('click', ()=>{
  const blob = new Blob([JSON.stringify(app.keys,null,2)], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'studyhub-keys.json'; a.click(); URL.revokeObjectURL(url);
});
document.getElementById('import-keys').addEventListener('click', ()=>{
  const input = document.createElement('input'); input.type='file'; input.accept='application/json';
  input.onchange = e => {
    const f = e.target.files[0];
    if(!f) return;
    const reader = new FileReader();
    reader.onload = ev => {
      try{
        const obj = JSON.parse(ev.target.result);
        if(obj.openai) app.keys.openai = obj.openai;
        if(obj.gemini) app.keys.gemini = obj.gemini;
        saveKeysToStorage();
        alert('Keys imported.');
      }catch(err){ alert('Invalid file.'); }
    };
    reader.readAsText(f);
  };
  input.click();
});

/* ---------------------
   MINI BROWSER
   --------------------- */
function openMiniBrowser(){
  document.getElementById('mini-browser-modal').classList.remove('hidden');
  document.getElementById('mini-frame').src = 'about:blank'; // Clear previous content
}
document.getElementById('mini-close').addEventListener('click', ()=> document.getElementById('mini-browser-modal').classList.add('hidden'));
document.getElementById('mini-go').addEventListener('click', ()=>{
  let url = document.getElementById('mini-url').value.trim();
  if(!url) return;
  if(!/^https?:\/\//i.test(url)) url = 'https://' + url; // Enforce protocol
  document.getElementById('mini-frame').src = url;
});

/* ---------------------
   RESOURCES & GAMES
   --------------------- */
const resources = {
  Math: [
    {title:'Khan Academy — 7th Grade Math', url:'https://www.khanacademy.org/math/cc-seventh-grade-math', type:'link'},
    {title:'Desmos Graphing Calculator', url:'https://www.desmos.com/calculator', type:'tool'},
    {title:'Prodigy Math Game', url:'https://www.prodigygame.com/', type:'game'}
  ],
  Science: [
    {title:'PhET Simulations', url:'https://phet.colorado.edu/', type:'tool'},
    {title:'NASA Eyes on the Solar System', url:'https://eyes.nasa.gov/', type:'tool'},
    {title:'Science Kids', url:'https://www.sciencekids.co.nz/', type:'link'}
  ],
  History: [
    {title:'History for Kids — Ancient Civilizations', url:'https://www.historyforkids.net/ancient-history.html', type:'link'},
    {title:'iCivics (Gov Games)', url:'https://www.icivics.org/games', type:'game'}
  ],
  ELA: [
    {title:'Project Gutenberg', url:'https://www.gutenberg.org/', type:'link'},
    {title:'Quill.org', url:'https://www.quill.org/', type:'tool'}
  ]
};
const games = {
  Math: [
    {title:'Integer Warp (MathPlayground)', url:'https://www.mathplayground.com/ASB_IntegerWarp.html'},
    {title:'Fraction Matcher (Math is Fun Activities)', url:'https://www.mathsisfun.com/'},
  ],
  Science: [
    {title:'Build a Cell (Cells Alive)', url:'https://www.cellsalive.com/cells/cell_model.htm'},
    {title:'Switch Zoo', url:'https://www.switchzoo.com/'}
  ],
  History: [
    {title:'Mission US', url:'https://www.mission-us.org/'},
    {title:'Google Earth Voyager', url:'https://earth.google.com/web/voyager'}
  ],
  ELA: [
    {title:'Storybird / Storyboard That', url:'https://www.storyboardthat.com/'}
  ]
};

function setupResourcesAndGames(){
  // resource buttons
  ['Math','Science','History','ELA'].forEach(sub=>{
    const btn = document.getElementById('btn-' + sub);
    if(btn) btn.addEventListener('click', ()=> loadResources(sub));
  });
  loadResources('Math');

  // load games
  const gl = document.getElementById('game-list');
  gl.innerHTML = '';
  Object.keys(games).forEach(cat=>{
    games[cat].forEach(g=>{
      const d = document.createElement('div');
      d.className = 'card';
      d.innerHTML = `<h4 class="font-bold">${g.title}</h4><p class="text-sm text-gray-400 mb-2">${cat}</p><a class="text-primary underline" href="${g.url}" target="_blank">Open</a>`;
      gl.appendChild(d);
    });
  });
}
function loadResources(subject){
  const list = document.getElementById('resource-list');
  list.innerHTML = '';
  // Update active class on buttons
  document.querySelectorAll('#tab-resources button').forEach(btn => {
    btn.classList.remove('bg-primary', 'bg-gray-800');
    if (btn.id === 'btn-' + subject) {
      btn.classList.add('bg-primary');
    } else {
      btn.classList.add('bg-gray-800');
    }
  });
  resources[subject].forEach(r=>{
    const d = document.createElement('div');
    d.className = 'card';
    d.innerHTML = `<h4 class="font-bold">${r.title}</h4><p class="text-sm text-gray-400">${r.type}</p><a class="text-primary underline" href="${r.url}" target="_blank">Open</a>`;
    list.appendChild(d);
  });
}

/* ---------------------
   AI CALLS (OpenAI & Gemini)
   --------------------- */
/* Warning: these calls use client-side keys. For production, proxy via server. */
async function callAI(promptText, modelOpts = {}) {
  const provider = app.aiProvider || document.getElementById('ai-provider-select-settings').value || 'OpenAI';
  // ensure latest keys loaded from storage
  loadKeysFromStorage();

  if(provider === 'OpenAI') {
    const key = app.keys.openai || '';
    if(!key) return 'Error: OpenAI API key not set in Admin / Settings.';
    try{
      const resp = await fetch('https://api.openai.com/v1/chat/completions', {
        method: 'POST',
        headers: { 'Content-Type':'application/json', 'Authorization': 'Bearer ' + key },
        body: JSON.stringify({
          model: modelOpts.model || 'gpt-3.5-turbo',
          messages: [{role:'user', content: promptText}],
          temperature: modelOpts.temperature ?? 0.7,
          max_tokens: modelOpts.max_tokens ?? 600
        })
      });
      const data = await resp.json();
      if(data.error) return `Error: ${data.error.message || JSON.stringify(data.error)}`;
      return data.choices?.[0]?.message?.content || JSON.stringify(data);
    } catch(e){ return 'Error: ' + e.message; }
  } else if(provider === 'Gemini') {
    const key = app.keys.gemini || '';
    if(!key) return 'Error: Gemini API key not set in Admin / Settings.';
    try{
      // Using the publicly available endpoint
      const url = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=' + encodeURIComponent(key);
      const resp = await fetch(url, {
        method: 'POST',
        headers: { 'Content-Type':'application/json' },
        body: JSON.stringify({ contents: [{ parts: [{ text: promptText }] }] })
      });
      const data = await resp.json();
      if(data.error) return 'Error: ' + (data.error.message || JSON.stringify(data.error));
      return data.candidates?.[0]?.content?.parts?.[0]?.text || JSON.stringify(data);
    } catch(e){ return 'Error: ' + e.message; }
  } else {
    return 'Error: Unknown provider selected.';
  }
}

/* ---------------------
   GENERATORS: wiring UI
   --------------------- */
async function generateAndShow(idTopic, idResult, promptBuilder, lockedIfDec1=false){
  // enforce Dec 1 rule for certain features
  const now = new Date();
  const dec1 = new Date(now.getFullYear(), 11, 1);
  if(lockedIfDec1 && now >= dec1 && !isSubscribedConfirmed()){
    alert('This feature requires subscription to @cursedgamer2 (see notice). Click "I Subscribed" to enable.');
    return;
  }

  const topic = document.getElementById(idTopic).value.trim();
  if(!topic){ alert('Enter a topic or question.'); return; }
  const resultEl = document.getElementById(idResult);
  resultEl.innerHTML = '<em class="text-yellow-300">Thinking...</em>';
  const prompt = promptBuilder(topic);
  const out = await callAI(prompt);
  resultEl.innerHTML = marked.parse(out || 'No response.');
  return out;
}

/* flash */
document.getElementById('gen-flash').addEventListener('click', async ()=>{
  const out = await generateAndShow('flashcard-topic', 'flash-result', t => `Create 5 flashcards for the topic: ${t}. Format as Question: Answer pairs.`);
});
document.getElementById('save-flash').addEventListener('click', ()=> saveGenerated('flash', 'flashcard-topic', 'flash-result'));

/* quiz (locked by Dec 1 rule) */
document.getElementById('gen-quiz').addEventListener('click', async ()=>{
  await generateAndShow('quiz-topic','quiz-result', t => `Create a 3-question multiple choice quiz about ${t}. Provide the answer key.`, true);
});
document.getElementById('save-quiz').addEventListener('click', ()=> saveGenerated('quiz','quiz-topic','quiz-result'));

/* tutor (locked by Dec 1 rule) */
document.getElementById('gen-tutor').addEventListener('click', async ()=>{
  await generateAndShow('tutor-topic','tutor-result', t => `Explain and teach this topic in simple terms: ${t}`, true);
});
document.getElementById('save-tutor').addEventListener('click', ()=> saveGenerated('tutor','tutor-topic','tutor-result'));

/* helper */
document.getElementById('gen-helper').addEventListener('click', async ()=>{
  await generateAndShow('helper-topic','helper-result', t => `Give concise study tips, a short summary, and 5 practice questions for: ${t}`);
});
document.getElementById('save-helper').addEventListener('click', ()=> saveGenerated('helper','helper-topic','helper-result'));

/* save generated content to local dashboard */
function saveGenerated(type, topicId, resultId){
  const topic = document.getElementById(topicId).value.trim();
  const content = document.getElementById(resultId).innerHTML.trim();
  if(!content){ alert('Nothing to save. Generate first.'); return; }
  const store = JSON.parse(localStorage.getItem(DASHBOARD_STORE) || '[]');
  store.unshift({ id: Date.now(), type, topic, content });
  localStorage.setItem(DASHBOARD_STORE, JSON.stringify(store));
  alert('Saved to Dashboard.');
  renderDashboard();
}

/* ---------------------
   DASHBOARD
   --------------------- */
function renderDashboard(){
  const listEl = document.getElementById('dashboard-list');
  listEl.innerHTML = '';
  const store = JSON.parse(localStorage.getItem(DASHBOARD_STORE) || '[]');
  if(store.length === 0){
    listEl.innerHTML = '<div class="text-gray-400">No saved items yet.</div>';
    return;
  }
  store.forEach(item=>{
    const d = document.createElement('div');
    d.className = 'card';
    d.innerHTML = `<div class="flex justify-between items-start"><div><strong class="capitalize">${item.type}</strong> · <span class="text-gray-400">${item.topic}</span></div>
      <div class="flex gap-2"><button data-id="${item.id}" class="preview-btn bg-gray-700 px-2 py-1 rounded">Preview</button><button data-id="${item.id}" class="del-btn bg-red-600 px-2 py-1 rounded">Delete</button></div></div>
      <div class="mt-2 text-sm text-gray-300 hidden content" id="content-${item.id}">${item.content}</div>`;
    listEl.appendChild(d);
  });

  // attach listeners
  document.querySelectorAll('.preview-btn').forEach(b=>{
    b.addEventListener('click', ev=>{
      const id = ev.target.getAttribute('data-id');
      const el = document.getElementById('content-'+id);
      if(el) el.classList.toggle('hidden');
    });
  });
  document.querySelectorAll('.del-btn').forEach(b=>{
    b.addEventListener('click', ev=>{
      const id = +ev.target.getAttribute('data-id');
      let store = JSON.parse(localStorage.getItem(DASHBOARD_STORE) || '[]');
      store = store.filter(x=> x.id !== id);
      localStorage.setItem(DASHBOARD_STORE, JSON.stringify(store));
      renderDashboard();
    });
  });
}

/* export / import dashboard */
document.getElementById('export-data').addEventListener('click', ()=>{
  const blob = new Blob([localStorage.getItem(DASHBOARD_STORE) || '[]'], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'studyhub-data.json'; a.click(); URL.revokeObjectURL(url);
});
document.getElementById('import-data').addEventListener('click', ()=> document.getElementById('import-file').click());
document.getElementById('import-file').addEventListener('change', (e)=>{
  const f = e.target.files[0]; if(!f) return;
  const r = new FileReader(); r.onload = ev=>{
    try{
      const arr = JSON.parse(ev.target.result);
      if(!Array.isArray(arr)) throw new Error('Invalid');
      localStorage.setItem(DASHBOARD_STORE, JSON.stringify(arr.concat(JSON.parse(localStorage.getItem(DASHBOARD_STORE) || '[]'))));
      renderDashboard();
      alert('Imported.');
    }catch(err){ alert('Invalid file.'); }
  }; r.readAsText(f);
});

/* ---------------------
   ADMIN PANEL events
   --------------------- */
function openAdminModal(){ document.getElementById('admin-modal').classList.remove('hidden'); }
document.getElementById('admin-close').addEventListener('click', ()=> document.getElementById('admin-modal').classList.add('hidden'));

document.getElementById('clear-dashboard').addEventListener('click', ()=>{
  if(confirm('Are you sure you want to clear ALL dashboard items? This cannot be undone.')){
    localStorage.removeItem(DASHBOARD_STORE);
    renderDashboard();
    alert('Dashboard cleared.');
    document.getElementById('admin-modal').classList.add('hidden');
  }
});

document.getElementById('fill-demo-keys').addEventListener('click', ()=>{
  // Placeholder logic for filling demo keys
  alert('Demo keys filled. Note: These are placeholders and may not work with live APIs.');
});

/* ---------------------
   FOCUS TIMER & NOISE (TONE.js)
   --------------------- */
let timerInterval;
let timerSeconds = 0;
let isTimerRunning = false;
let noiseSynth;
const timerDisplay = document.getElementById('timer-display');
const noiseButton = document.getElementById('noise-btn');

function updateTimerDisplay(){
  const minutes = String(Math.floor(timerSeconds / 60)).padStart(2, '0');
  const seconds = String(timerSeconds % 60).padStart(2, '0');
  timerDisplay.textContent = `${minutes}:${seconds}`;
}

// Global functions for timer buttons in HTML
window.startTimer = function(durationMinutes){
  if(isTimerRunning) return; 
  
  timerSeconds = durationMinutes * 60;
  isTimerRunning = true;
  updateTimerDisplay();

  timerInterval = setInterval(()=>{
    timerSeconds--;
    updateTimerDisplay();
    if(timerSeconds <= 0){
      clearInterval(timerInterval);
      isTimerRunning = false;
      timerDisplay.textContent = 'TIME UP!';
    }
  }, 1000);
}

window.resetTimer = function(){
  clearInterval(timerInterval);
  isTimerRunning = false;
  timerSeconds = 0;
  timerDisplay.textContent = '00:00';
}

// Tone.js Noise Generator (Pink Noise is good for focus)
noiseButton.addEventListener('click', async ()=>{
  if(!noiseSynth){
    await Tone.start();
    noiseSynth = new Tone.Noise('pink').toDestination();
    noiseSynth.volume.value = -20; // Lower the volume
  }

  if(noiseSynth.state !== 'started'){
    noiseSynth.start();
    noiseButton.textContent = 'Stop Focus Noise';
    noiseButton.classList.remove('bg-gray-700');
    noiseButton.classList.add('bg-red-600');
  } else {
    noiseSynth.stop();
    noiseButton.textContent = 'Start Focus Noise';
    noiseButton.classList.remove('bg-red-600');
    noiseButton.classList.add('bg-gray-700');
  }
});

</script>
</html>
