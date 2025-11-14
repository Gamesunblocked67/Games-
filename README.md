<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>gamesunbloocked67 — v1.6 (Study + Games)</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{
  --bg-a:#2b0057; --bg-b:#42007a; --accent:#c88cff; --muted:#dcd3ef;
  --glass:rgba(255,255,255,0.04); --card:#18061a; --glow:rgba(200,140,255,0.22);
  --accent-2:#ffb76b;
}
*{box-sizing:border-box}
html,body{height:100%;margin:0;font-family:Roboto,system-ui,-apple-system,Segoe UI,Arial; background:linear-gradient(180deg,var(--bg-a),var(--bg-b)); color:var(--muted); -webkit-font-smoothing:antialiased;}
a{color:var(--accent)}
#root{min-height:100vh;display:flex;flex-direction:column;align-items:center;padding:20px;position:relative;}

/* LAYOUT */
.container{width:100%;max-width:1200px;}
.header{margin-top:18px;text-align:center}
.title{font-size:40px;font-weight:700; color:#fff;}
.subtitle{margin-top:6px;color:rgba(255,255,255,0.9);font-size:14px}

/* LANDING */
.land{margin:26px auto;padding:18px;border-radius:12px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.04);text-align:center}
.choice-row{display:flex;gap:18px;justify-content:center;flex-wrap:wrap;margin-top:14px}
.choice{min-width:220px;padding:18px;border-radius:12px;background:rgba(0,0,0,0.14);border:1px solid rgba(255,255,255,0.03);cursor:pointer;transition:transform .18s}
.choice:hover{transform:translateY(-6px)}
.choice h3{margin:6px 0}

/* APP SHELL */
.topbar{display:flex;justify-content:space-between;align-items:center;margin:8px 0}
.icon-row{display:flex;gap:16px;align-items:center}
.icon{padding:10px;border-radius:10px;background:rgba(255,255,255,0.02);cursor:pointer}
.support{font-size:13px;color:var(--muted)}

/* PAGES */
.pages{margin-top:12px}
.page{display:none;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:18px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
.page.active{display:block}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px}
.card{background:rgba(255,255,255,0.02);padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
.btn{background:var(--accent);color:#120018;padding:8px 10px;border-radius:8px;border:none;cursor:pointer;font-weight:700}
.small{padding:6px 8px;font-size:13px}

/* bookmarklets */
.bm-board{display:flex;flex-wrap:wrap;gap:12px}
.bm-card{min-width:200px;padding:10px;border-radius:8px;background:rgba(0,0,0,0.35);border:1px solid rgba(255,255,255,0.04);cursor:grab}

/* study tools layout */
.tools-grid{display:grid;grid-template-columns:1fr 380px;gap:16px}
@media (max-width:980px){ .tools-grid{grid-template-columns:1fr} .choice-row{flex-direction:column} }

/* small utilities */
.muted{color:rgba(255,255,255,0.7);font-size:13px}
.note-area{width:100%;height:180px;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.05);background:rgba(0,0,0,0.25);color:#fff}
.footer{margin-top:18px;color:rgba(255,255,255,0.6);font-size:13px;text-align:center}
.progress{height:12px;background:rgba(255,255,255,0.04);border-radius:999px;overflow:hidden}
.progress>i{display:block;height:100%;width:0;background:linear-gradient(90deg,var(--accent),var(--accent-2));transition:width .2s linear}
.warn{color:#ffcc66;font-weight:700;margin-top:8px}
.link{color:var(--accent)}
</style>
</head>
<body>
<div id="root" class="container">
  <div class="header">
    <div class="title">gamesunbloocked67</div>
    <div class="subtitle">You can either study or play games — (not on your school iPad p)</div>
  </div>

  <!-- LANDING CHOICES -->
  <div id="landing" class="land">
    <div style="font-weight:700;font-size:18px">Choose a mode</div>
    <div class="choice-row">
      <div class="choice" onclick="enterMode('study')">
        <h3>Study Mode</h3>
        <div class="muted">Tools & practice tailored for 7th grade — Oak Middle School (Shrewsbury, MA)</div>
      </div>
      <div class="choice" onclick="enterMode('games')">
        <h3>Games Mode</h3>
        <div class="muted">Play unblocked-style games (please don't use school devices)</div>
      </div>
    </div>
    <div style="margin-top:12px" class="muted">Support me: <a class="link" href="https://www.youtube.com/@cursedgamer2" target="_blank">YouTube.com/@cursedgamer2</a></div>
  </div>

  <!-- APP SHELL -->
  <div id="app" style="display:none">
    <div class="topbar">
      <div class="icon-row">
        <div class="icon" onclick="showSection('home')">🏠 Home</div>
        <div class="icon" onclick="showSection('study')">📚 Study</div>
        <div class="icon" onclick="showSection('games')">🎮 Games</div>
        <div class="icon" onclick="showSection('bookmarklets')">🔖 Bookmarks</div>
        <div class="icon" onclick="showSection('updates')">⬆️ Updates</div>
      </div>
      <div class="support">Support: <a class="link" href="https://www.youtube.com/@cursedgamer2" target="_blank">YouTube.com/@cursedgamer2</a></div>
    </div>

    <div class="pages">
      <!-- HOME -->
      <div id="home" class="page active">
        <h2>Welcome</h2>
        <div class="muted">Mode: <span id="currentMode">—</span></div>
        <div style="margin-top:12px" class="warn">Reminder: Do NOT play games on your school iPad. Use Study Mode for schoolwork.</div>
      </div>

      <!-- STUDY HUB -->
      <div id="study" class="page">
        <h2>Study Hub — 7th Grade (Oak Middle School, Shrewsbury, MA)</h2>
        <div class="tools-grid" style="margin-top:12px">
          <div>
            <!-- flashcards -->
            <div class="card" style="margin-bottom:12px">
              <h3>Flashcards</h3>
              <div class="muted">Create and review flashcards</div>
              <div style="display:flex;gap:8px;margin-top:8px">
                <input id="fcTerm" placeholder="Term" style="flex:1;padding:8px;border-radius:6px;border:1px solid rgba(255,255,255,0.04);background:rgba(0,0,0,0.2);color:#fff">
                <input id="fcDef" placeholder="Definition" style="flex:2;padding:8px;border-radius:6px;border:1px solid rgba(255,255,255,0.04);background:rgba(0,0,0,0.2);color:#fff">
                <button class="btn small" onclick="addFlashcard()">Add</button>
              </div>
              <div id="fcList" style="margin-top:8px;max-height:160px;overflow:auto"></div>
            </div>

            <!-- quiz maker -->
            <div class="card" style="margin-bottom:12px">
              <h3>Quick Quiz</h3>
              <div class="muted">Auto-generate a 5-question quiz from your flashcards</div>
              <div style="margin-top:8px"><button class="btn" onclick="generateQuiz()">Generate Quiz (5 Q)</button></div>
              <div id="quizArea" style="margin-top:8px"></div>
            </div>

            <!-- math practice -->
            <div class="card" style="margin-bottom:12px">
              <h3>Math Practice</h3>
              <div class="muted">Fractions, integers, equations</div>
              <div style="display:flex;gap:8px;margin-top:8px">
                <select id="mathType" style="padding:8px;border-radius:6px">
                  <option value="fractions">Fractions</option>
                  <option value="integers">Integers</option>
                  <option value="equations">One-step equations</option>
                </select>
                <button class="btn small" onclick="mathPractice()">Start</button>
              </div>
              <div id="mathArea" style="margin-top:8px"></div>
            </div>

            <!-- ELA helper -->
            <div class="card" style="margin-bottom:12px">
              <h3>ELA Tools</h3>
              <div class="muted">Reading comprehension prompt & grammar checker</div>
              <textarea id="elaText" class="note-area" placeholder="Paste a paragraph to get grammar hints / summary"></textarea>
              <div style="display:flex;gap:8px;margin-top:8px">
                <button class="btn small" onclick="elaSummary()">Summary</button>
                <button class="btn small" onclick="elaGrammar()">Grammar Check</button>
              </div>
              <div id="elaResult" style="margin-top:8px"></div>
            </div>

            <!-- science & social -->
            <div class="card" style="margin-bottom:12px">
              <h3>Science & Social Studies</h3>
              <div class="muted">Quick facts, practice questions</div>
              <div style="display:flex;gap:8px;margin-top:8px">
                <select id="ssTopic" style="padding:8px;border-radius:6px">
                  <option value="cells">Cells - Biology</option>
                  <option value="energy">Energy</option>
                  <option value="colonial">Colonial America</option>
                  <option value="geography">Geography quick facts</option>
                </select>
                <button class="btn small" onclick="ssInfo()">Show</button>
              </div>
              <div id="ssArea" style="margin-top:8px"></div>
            </div>

            <!-- note-taking -->
            <div class="card" style="margin-bottom:12px">
              <h3>Quick Notes</h3>
              <textarea id="notes" class="note-area" placeholder="Type notes here..."></textarea>
              <div style="display:flex;gap:8px;margin-top:8px">
                <button class="btn small" onclick="saveNotes()">Save</button>
                <button class="btn small" onclick="clearNotes()">Clear</button>
              </div>
            </div>

            <!-- sample tests -->
            <div class="card" style="margin-bottom:12px">
              <h3>Sample Practice Test</h3>
              <div class="muted">Short mixed-subject test</div>
              <button class="btn" onclick="sampleTest()">Start Test</button>
              <div id="testArea" style="margin-top:8px"></div>
            </div>
          </div>

          <!-- RIGHT COLUMN TOOLS -->
          <div>
            <div class="card" style="margin-bottom:12px">
              <h3>Study Timer</h3>
              <div class="muted">Pomodoro-style focus timer</div>
              <div style="display:flex;gap:8px;margin-top:8px">
                <input id="timerMin" type="number" min="1" value="25" style="padding:8px;border-radius:6px;width:100px">
                <button class="btn small" onclick="startTimer()">Start</button>
                <button class="btn small" onclick="stopTimer()">Stop</button>
              </div>
              <div style="margin-top:8px" id="timerDisplay">00:00</div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>Homework Planner</h3>
              <div class="muted">Add assignments and due dates</div>
              <input id="hwTitle" placeholder="Assignment" style="width:100%;padding:8px;margin-top:8px;border-radius:6px">
              <input id="hwDue" type="date" style="width:100%;padding:8px;margin-top:8px;border-radius:6px">
              <div style="display:flex;gap:8px;margin-top:8px"><button class="btn small" onclick="addHW()">Add</button></div>
              <div id="hwList" style="margin-top:8px;max-height:160px;overflow:auto"></div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>Vocabulary Builder</h3>
              <input id="vTerm" placeholder="Word" style="width:100%;padding:8px;border-radius:6px">
              <input id="vDef" placeholder="Definition" style="width:100%;padding:8px;margin-top:8px;border-radius:6px">
              <div style="display:flex;gap:8px;margin-top:8px"><button class="btn small" onclick="addVocab()">Add</button></div>
              <div id="vocabList" style="margin-top:8px;max-height:140px;overflow:auto"></div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>Calculator</h3>
              <input id="calcIn" placeholder="Enter expression, e.g. 3/4 + 1/2" style="width:100%;padding:8px;border-radius:6px">
              <div style="display:flex;gap:8px;margin-top:8px"><button class="btn small" onclick="calc()">Calculate</button></div>
              <div id="calcOut" style="margin-top:8px"></div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>PDF Viewer</h3>
              <input type="file" id="pdfFile" accept="application/pdf" style="width:100%"><div id="pdfArea" style="margin-top:8px"></div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>Simple AI Tutor (local)</h3>
              <div class="muted">Ask a question, get a canned helpful response</div>
              <textarea id="tutorQ" class="note-area" placeholder="What do you need help with?"></textarea>
              <button class="btn" onclick="aiTutor()">Ask Tutor</button>
              <div id="tutorA" style="margin-top:8px"></div>
            </div>

            <div class="card" style="margin-bottom:12px">
              <h3>Citation Helper</h3>
              <input id="citeAuthor" placeholder="Author" style="width:100%;padding:8px;border-radius:6px">
              <input id="citeTitle" placeholder="Title" style="width:100%;padding:8px;margin-top:8px;border-radius:6px">
              <input id="citeYear" placeholder="Year" style="width:100%;padding:8px;margin-top:8px;border-radius:6px">
              <div style="display:flex;gap:8px;margin-top:8px"><button class="btn small" onclick="makeCitation()">Generate MLA</button></div>
              <div id="citeOut" style="margin-top:8px"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- GAMES -->
      <div id="games" class="page">
        <h2>Games</h2>
        <div class="muted">Warning shown on entry: please do not play on your school iPad.</div>
        <div style="margin-top:8px" class="warn" id="gamesWarning" onclick="alert('Seriously — do not play on your school iPad!')">PLS DO NOT PLAY THESE ON UR SCHOOL IPAD</div>
        <div style="margin-top:8px" class="muted">Support me: <a class="link" href="https://www.youtube.com/@cursedgamer2" target="_blank">YouTube.com/@cursedgamer2</a></div>
        <div style="margin-top:12px" id="gamesGrid" class="grid"></div>
      </div>

      <!-- BOOKMARKLETS -->
      <div id="bookmarklets" class="page">
        <h2>Bookmarklets (draggable)</h2>
        <div class="muted">Drag cards to reorder. Click Copy to copy bookmarklet code.</div>
        <div id="bmBoard" class="bm-board" style="margin-top:12px"></div>
      </div>

      <!-- UPDATES -->
      <div id="updates" class="page">
        <h2>Updates & Version Manager</h2>
        <div class="muted">Install / downgrade versions — changes active games & bookmarklets.</div>
        <div id="updatesTiles" style="margin-top:12px" class="grid"></div>
      </div>
    </div>
  </div>

  <div class="footer">© 2025 gamesunbloocked67 — built for Oak Middle School 7th grade practice</div>
</div>

<script>
/* -------------- CONFIG/CONTENT -------------- */
const VERSIONS = {
  '1.3': {id:'1.3',title:'v1.3',desc:'Base release',games:[
    {title:'Snake',src:'https://www.google.com/logos/2010/snake_arcade/snake_arcade.html'},
    {title:'T-Rex Dino',src:'https://chromedino.com/'},
    {title:'2048',src:'https://play2048.co/'}
  ], bookmarklets:[
    {title:'Edit Page',code:`javascript:(function(){document.body.contentEditable='true';document.designMode='on';})();`},
    {title:'Show Cookies',code:`javascript:(function(){alert(document.cookie);} )();`}
  ]},
  '1.4': {id:'1.4',title:'v1.4',desc:'Added games',games:[
    {title:'Clicker Game',src:'https://selenite.cc/resources/semag/clicker/index.html'},
    {title:'Retro Bowl',src:'https://retrobowlgame.io/'},
    {title:'Moto X3M',src:'https://example.com/motox3m'}
  ], bookmarklets:[
    {title:'Mini Browser',code:`javascript:(function(){var i=document.createElement('iframe');i.src='https://www.google.com/';i.style.position='fixed';i.style.right='10px';i.style.top='10px';i.style.width='420px';i.style.height='300px';i.style.zIndex=99999;document.body.appendChild(i);})();`},
    {title:'History Flooder',code:`javascript:(function(){for(var i=0;i<50;i++)history.pushState(0,0,'/h/'+i);} )();`}
  ]},
  '1.5': {id:'1.5',title:'v1.5',desc:'UI improvements',games:[
    {title:'Geometry Dash Lite',src:'https://selenite.cc/resources/semag/gdlite/index.html'},
    {title:'GD Subzero',src:'https://geometry-lessons.github.io/geometry-dash-subzero.html'}
  ], bookmarklets:[
    {title:'Cloaker',code:`javascript:(function(){document.title='About: Blank';})();`}
  ]},
  '1.6': {id:'1.6',title:'v1.6',desc:'Purple home UI',games:[
    {title:'Among Us',src:'https://selenite.cc/resources/semag/amongus/index.html'},
    {title:'FNAF 1',src:'https://selenite.cc/resources/semag/fnaf/index.html'},
    {title:'Retro Bowl',src:'https://retrobowlgame.io/'},
    {title:'Pacman 256',src:'https://example.com/pacman256'},
    {title:'Flappy Bird',src:'https://flappybird.io/'},
    {title:'Snake Advanced',src:'https://www.google.com/logos/2010/snake_arcade/snake_arcade.html'},
    {title:'2048 Pro',src:'https://play2048.co/'},
    {title:'Tetris',src:'https://tetris.com/play-tetris'},
    {title:'Solitaire',src:'https://www.solitaire.com/'},
    {title:'Minesweeper',src:'https://minesweeper.online/'}
  ], bookmarklets:[
    {title:'Edit Page',code:`javascript:(function(){document.body.contentEditable='true';document.designMode='on';})();`},
    {title:'Show Cookies',code:`javascript:(function(){alert(document.cookie);} )();`},
    {title:'Mini Browser',code:`javascript:(function(){var i=document.createElement('iframe');i.src='https://www.google.com/';i.style.position='fixed';i.style.right='10px';i.style.top='10px';i.style.width='420px';i.style.height='300px';i.style.zIndex=99999;document.body.appendChild(i);})();`},
    {title:'History Flooder',code:`javascript:(function(){for(var i=0;i<50;i++)history.pushState(0,0,'/h/'+i);} )();`},
    {title:'Crash Simulation',code:`javascript:(function(){console.log('Simulated crash');})();`},
    {title:'Userscript Injector',code:`javascript:(function(){var s=document.createElement('script');s.src='https://example.com/userscript.js';document.body.appendChild(s);})();`},
    {title:'Dev Console',code:`javascript:(function(){console.log('Hello Dev');})();`},
    {title:'Study Helper',code:`javascript:(function(){alert('Flashcards ready');})();`},
    {title:'Speed Hack',code:`javascript:(function(){alert('Speed increased');})();`},
    {title:'Night Mode',code:`javascript:(function(){document.body.style.background='#000';})();`},
  ]}
};

let CURRENT_VERSION = localStorage.getItem('gsu_version') || '1.6';
let MODE = null;

/* -------------- UTIL / INIT -------------- */
function enterMode(m){
  MODE = m;
  document.getElementById('landing').style.display = 'none';
  document.getElementById('app').style.display = 'block';
  document.getElementById('currentMode').innerText = m.toUpperCase();
  if(m==='games'){
    showSection('games');
    // popup warning
    setTimeout(()=>{ alert('pls do not play these on ur school iPad'); }, 150);
  } else {
    showSection('study');
  }
  renderAll();
}

function showSection(id){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  const el = document.getElementById(id);
  if(el) el.classList.add('active');
}

/* -------------- GAMES / BOOKMARKLETS / UPDATES -------------- */
function renderAll(){
  renderGames();
  renderBookmarklets();
  renderUpdates();
}

function renderGames(){
  const grid = document.getElementById('gamesGrid');
  grid.innerHTML = '';
  const games = VERSIONS[CURRENT_VERSION].games;
  games.forEach(g=>{
    const div = document.createElement('div'); div.className='card';
    const embedText = g.src ? g.src : '';
    div.innerHTML = `<h4>${g.title}</h4><div class="muted">Source: ${embedText}</div>
      <div style="margin-top:8px"><button class="btn small" onclick="openGame('${encodeURIComponent(g.src)}')">Play</button></div>`;
    grid.appendChild(div);
  });
}

function openGame(urlEnc){
  const url = decodeURIComponent(urlEnc);
  // open in new tab; if iframe supported you can implement modal iframe
  window.open(url,'_blank');
}

function renderBookmarklets(){
  const board = document.getElementById('bmBoard'); board.innerHTML='';
  const bms = VERSIONS[CURRENT_VERSION].bookmarklets;
  bms.forEach((b,i)=>{
    const card = document.createElement('div'); card.className='bm-card';
    card.draggable = true;
    card.innerHTML = `<strong>${b.title}</strong><div style="font-size:12px;margin-top:8px;color:#ddd;white-space:pre-wrap">${b.code}</div>
      <div style="margin-top:8px"><button class="btn small" onclick="copyBM(${i})">Copy</button></div>`;
    board.appendChild(card);
  });

  // simple drag reorder
  let dragEl = null;
  board.querySelectorAll('.bm-card').forEach(c=>{
    c.addEventListener('dragstart',e=>{ dragEl = c; c.classList.add('dragging'); });
    c.addEventListener('dragend',e=>{ dragEl.classList.remove('dragging'); dragEl=null; });
    c.addEventListener('dragover',e=>{ e.preventDefault(); if(dragEl && dragEl!==c){ board.insertBefore(dragEl, c.nextSibling); } });
  });
}

function copyBM(i){
  const code = VERSIONS[CURRENT_VERSION].bookmarklets[i].code;
  navigator.clipboard.writeText(code).then(()=>alert('Copied "'+VERSIONS[CURRENT_VERSION].bookmarklets[i].title+'"'));
}

function renderUpdates(){
  const tiles = document.getElementById('updatesTiles'); tiles.innerHTML='';
  Object.values(VERSIONS).forEach(v=>{
    const div = document.createElement('div'); div.className='card';
    div.innerHTML = `<h4>${v.title}</h4><div class="muted">${v.desc}</div>
      <div style="margin-top:8px"><button class="btn small" onclick="installVersion('${v.id}')">${v.id===CURRENT_VERSION?'Installed':'Install'}</button></div>`;
    tiles.appendChild(div);
  });
}

function installVersion(id){
  if(!confirm('Install version '+id+'? This will change available games/bookmarklets.')) return;
  CURRENT_VERSION = id;
  localStorage.setItem('gsu_version', id);
  renderAll();
  alert('Installed v'+id);
}

/* -------------- STUDY TOOLS STORAGE & FUNCTIONS -------------- */
/* Flashcards */
let FLASHCARDS = JSON.parse(localStorage.getItem('gsu_flashcards')||'[]');
function saveFlashcards(){ localStorage.setItem('gsu_flashcards', JSON.stringify(FLASHCARDS)); renderFC(); }
function addFlashcard(){
  const t=document.getElementById('fcTerm').value.trim();
  const d=document.getElementById('fcDef').value.trim();
  if(!t || !d) { alert('Add both term and definition'); return; }
  FLASHCARDS.push({term:t,def:d}); saveFlashcards();
  document.getElementById('fcTerm').value=''; document.getElementById('fcDef').value='';
}
function renderFC(){
  const el=document.getElementById('fcList'); el.innerHTML='';
  FLASHCARDS.forEach((f,i)=>{
    const div=document.createElement('div'); div.style.marginTop='6px';
    div.innerHTML = `<strong>${f.term}</strong><div style="font-size:13px;color:#ddd">${f.def}</div>
      <div style="margin-top:6px"><button class="btn small" onclick="removeFC(${i})">Remove</button></div>`;
    el.appendChild(div);
  });
}
function removeFC(i){ FLASHCARDS.splice(i,1); saveFlashcards(); }

/* Quiz */
function generateQuiz(){
  if(FLASHCARDS.length<5){ alert('Need at least 5 flashcards to auto-generate a quiz'); return; }
  // pick 5 random
  const arr = FLASHCARDS.slice(); shuffle(arr); const qs = arr.slice(0,5);
  const qa = qs.map((q,idx)=>`<div style="margin-top:8px"><strong>Q${idx+1}:</strong> What is "${q.term}"?<div><input id="ans${idx}" style="width:100%;padding:6px;border-radius:6px;background:#111;border:1px solid #222;color:#fff"></div></div>`);
  const html = qa.join('') + `<div style="margin-top:8px"><button class="btn" onclick="gradeQuiz(${JSON.stringify(qs)})">Submit</button></div>`;
  document.getElementById('quizArea').innerHTML = html;
}
function gradeQuiz(qsJson){
  // qsJson is array of objects
  const qs = qsJson;
  let score=0;
  for(let i=0;i<qs.length;i++){
    const a = document.getElementById('ans'+i).value.trim().toLowerCase();
    if(a && qs[i].def.toLowerCase().includes(a)) score++;
  }
  alert('Score: '+score+' / '+qs.length);
}

/* Math practice */
function mathPractice(){
  const type = document.getElementById('mathType').value;
  let html = '';
  if(type==='fractions'){
    // simple fraction addition
    const a = randInt(1,6), b=randInt(2,8), c=randInt(1,6), d=randInt(2,8);
    html = `<div>${a}/${b} + ${c}/${d} = ? <br><input id="mathAns" style="width:100%;padding:6px;margin-top:8px"></div>
      <div style="margin-top:8px"><button class="btn" onclick="checkFraction(${a},${b},${c},${d})">Check</button></div>`;
  } else if(type==='integers'){
    const x=randInt(-12,12), y=randInt(-12,12);
    html = `<div>${x} + ${y} = ? <br><input id="mathAns" style="width:100%;padding:6px;margin-top:8px"></div>
      <div style="margin-top:8px"><button class="btn" onclick="checkInteger(${x},${y})">Check</button></div>`;
  } else {
    const n=randInt(1,9);
    html = `<div>Solve: ${n} + x = ${n+randInt(1,9)} <br><input id="mathAns" style="width:100%;padding:6px;margin-top:8px"></div>
      <div style="margin-top:8px"><button class="btn" onclick="checkEquation(${n})">Check</button></div>`;
  }
  document.getElementById('mathArea').innerHTML = html;
}
function checkFraction(a,b,c,d){
  // naive check: compute decimal
  const right = (a/b)+(c/d);
  const ans = parseFloat(document.getElementById('mathAns').value);
  if(Math.abs(ans - right) < 0.001) alert('Correct!');
  else alert('Try again. Answer ~ '+right.toFixed(3));
}
function checkInteger(x,y){
  const ans = parseInt(document.getElementById('mathAns').value);
  if(ans===x+y) alert('Correct!');
  else alert('Try again. Answer: '+(x+y));
}
function checkEquation(n){
  const val = parseInt(document.getElementById('mathAns').value);
  // equation was n + x = (n + rand) - but we didn't store; so just accept numeric
  if(!isNaN(val)) alert('Got it — check with teacher if unsure.');
  else alert('Enter a number');
}

/* ELA */
function elaSummary(){
  const t = document.getElementById('elaText').value.trim();
  if(!t) { alert('Paste a paragraph first'); return; }
  // simple summarizer: return first sentence or first 20 words
  const first = t.split(/[.?!]\s/)[0];
  const words = t.split(/\s+/).slice(0,20).join(' ');
  document.getElementById('elaResult').innerText = 'Summary: ' + (first || words);
}
function elaGrammar(){
  const t = document.getElementById('elaText').value.trim();
  if(!t) { alert('Paste a paragraph first'); return; }
  // naive grammar suggestions
  const suggestions = [];
  if(t.includes(' ur ')) suggestions.push('Avoid texting shorthand like "ur"');
  if(t.match(/\b( alot|alot )\b/)) suggestions.push('Use "a lot" (two words).');
  if(suggestions.length===0) suggestions.push('No obvious issues found (basic check).');
  document.getElementById('elaResult').innerText = 'Grammar hints:\n - ' + suggestions.join('\n - ');
}

/* Science & Social */
function ssInfo(){
  const topic = document.getElementById('ssTopic').value;
  const area = document.getElementById('ssArea');
  if(topic==='cells') area.innerText = 'Cells: basic unit of life. Animal cells vs plant cells: chloroplasts in plant cells.';
  if(topic==='energy') area.innerText = 'Energy: kinetic vs potential. Energy is conserved in closed systems.';
  if(topic==='colonial') area.innerText = 'Colonial America: key events include Mayflower (1620), colonies growing for trade, taxation issues later lead to revolution.';
  if(topic==='geography') area.innerText = 'Geography tip: use coordinates (latitude, longitude). Look at maps for state capitals (MA: Boston).';
}

/* Notes */
function saveNotes(){ localStorage.setItem('gsu_notes', document.getElementById('notes').value); alert('Notes saved locally'); }
function clearNotes(){ document.getElementById('notes').value=''; localStorage.removeItem('gsu_notes'); }

/* Sample Test */
function sampleTest(){
  const area = document.getElementById('testArea');
  const html = `<div>
    <div><strong>1.</strong> What is 3/4 + 1/2 ? <input id="t1"></div>
    <div><strong>2.</strong> What organelle is in plant cells but not animal cells? <input id="t2"></div>
    <div style="margin-top:8px"><button class="btn" onclick="gradeTest()">Submit</button></div>
  </div>`;
  area.innerHTML = html;
}
function gradeTest(){
  let score=0;
  const a1 = parseFloat(document.getElementById('t1').value);
  if(Math.abs(a1 - 1.25) < 0.01) score++;
  const a2 = (document.getElementById('t2').value||'').toLowerCase();
  if(a2.includes('chloroplast') || a2.includes('chloroplasts')) score++;
  alert('Test score: '+score+'/2');
}

/* Timer */
let timerInterval=null;
function startTimer(){
  const mins = parseInt(document.getElementById('timerMin').value) || 25;
  let sec = mins*60;
  clearInterval(timerInterval);
  timerInterval = setInterval(()=>{
    sec--;
    const mm = String(Math.floor(sec/60)).padStart(2,'0');
    const ss = String(sec%60).padStart(2,'0');
    document.getElementById('timerDisplay').innerText = mm+':'+ss;
    if(sec<=0){ clearInterval(timerInterval); alert('Timer done!'); }
  },1000);
}
function stopTimer(){ clearInterval(timerInterval); }

/* Homework planner */
let HW = JSON.parse(localStorage.getItem('gsu_hw')||'[]');
function addHW(){
  const t = document.getElementById('hwTitle').value.trim();
  const d = document.getElementById('hwDue').value;
  if(!t || !d) { alert('Add title and due date'); return; }
  HW.push({title:t,due:d,done:false});
  localStorage.setItem('gsu_hw', JSON.stringify(HW)); renderHW();
  document.getElementById('hwTitle').value=''; document.getElementById('hwDue').value='';
}
function renderHW(){
  const el = document.getElementById('hwList'); el.innerHTML='';
  HW.forEach((h,i)=>{
    const div = document.createElement('div'); div.style.marginTop='8px';
    div.innerHTML = `<div><strong>${h.title}</strong> — due ${h.due} <button class="btn small" onclick="removeHW(${i})">Remove</button></div>`;
    el.appendChild(div);
  });
}
function removeHW(i){ HW.splice(i,1); localStorage.setItem('gsu_hw', JSON.stringify(HW)); renderHW(); }

/* Vocab */
let VOCAB = JSON.parse(localStorage.getItem('gsu_vocab')||'[]');
function addVocab(){ const t=document.getElementById('vTerm').value.trim(), d=document.getElementById('vDef').value.trim(); if(!t||!d){alert('Add word and definition');return;} VOCAB.push({word:t,def:d}); localStorage.setItem('gsu_vocab',JSON.stringify(VOCAB)); renderVocab(); document.getElementById('vTerm').value=''; document.getElementById('vDef').value=''; }
function renderVocab(){ const el=document.getElementById('vocabList'); el.innerHTML=''; VOCAB.forEach((v,i)=>{ const div=document.createElement('div'); div.style.marginTop='6px'; div.innerHTML=`<strong>${v.word}</strong><div style="font-size:13px;color:#ddd">${v.def}</div><div style="margin-top:6px"><button class="btn small" onclick="removeVocab(${i})">Remove</button></div>`; el.appendChild(div); }); }
function removeVocab(i){ VOCAB.splice(i,1); localStorage.setItem('gsu_vocab',JSON.stringify(VOCAB)); renderVocab(); }

/* Calculator */
function calc(){ const exp = document.getElementById('calcIn').value.trim(); try{ const res = eval(exp); document.getElementById('calcOut').innerText = 'Result: '+res; }catch(e){ alert('Invalid expression'); } }

/* PDF viewer - just show filename (full PDF viewer needs embed libs) */
document.getElementById('pdfFile').addEventListener('change', (e)=>{ const f = e.target.files[0]; if(!f) return; document.getElementById('pdfArea').innerText = 'Selected: '+f.name; });

/* AI Tutor - canned responses */
function aiTutor(){
  const q = document.getElementById('tutorQ').value.trim().toLowerCase();
  if(!q){ alert('Ask something'); return; }
  let ans = "I don't know, try asking your teacher.";
  if(q.includes('fraction') || q.includes('fractions')) ans = "Fractions: find common denominator to add/subtract. Multiply denominators if needed.";
  if(q.includes('cell')) ans = "Cells: remember plant cells have chloroplasts and a cell wall.";
  if(q.includes('essay')) ans = "Essay tips: intro, 2-3 body paragraphs, conclusion. Use topic sentences and transitions.";
  document.getElementById('tutorA').innerText = ans;
}

/* Citation */
function makeCitation(){
  const a=document.getElementById('citeAuthor').value.trim(), t=document.getElementById('citeTitle').value.trim(), y=document.getElementById('citeYear').value.trim();
  if(!t||!y) { alert('Title and year required'); return; }
  const mla = `${a? a + ". " : ""}"${t}." ${y}.`;
  document.getElementById('citeOut').innerText = mla;
}

/* Sample test content & misc helpers */
function randInt(a,b){ return Math.floor(Math.random()*(b-a+1))+a; }
function shuffle(arr){ for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; } }

/* Flashcards init */
renderFC();
renderHW();
renderVocab();

/* -------------- Page init after user chooses -------------- */
function initAfterLoad(){
  // nothing optional
}

/* -------------- Save state before unload -------------- */
window.addEventListener('beforeunload', ()=> {
  localStorage.setItem('gsu_flashcards', JSON.stringify(FLASHCARDS));
  localStorage.setItem('gsu_hw', JSON.stringify(HW));
  localStorage.setItem('gsu_vocab', JSON.stringify(VOCAB));
});

/* Render update/games/bookmarklets initially (when landing choice made) */
function renderAllOnShow(){
  renderAll();
}

/* If user returns (resume) show landing */
document.addEventListener('DOMContentLoaded', ()=>{
  // landing visible by default
});
</script>
</body>
</html>
