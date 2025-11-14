<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>gamesunbloocked67 — v1.8 (Study + Games)</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{
  --bg-a:#13021b; --bg-b:#38104a; --accent:#ff7a38; --accent-2:#c88cff;
  --muted:#dcd3ef; --glass:rgba(255,255,255,0.04);
  --card:#18061a;
}
*{box-sizing:border-box}
html,body{height:100%;margin:0;font-family:Roboto,system-ui,-apple-system,Segoe UI,Arial; background: linear-gradient(180deg,var(--bg-a),var(--bg-b)); color:var(--muted); -webkit-font-smoothing:antialiased;}
a{color:var(--accent-2)}
.container{max-width:1180px;margin:18px auto;padding:18px}
.header{display:flex;align-items:center;justify-content:space-between;gap:12px}
.brand{font-size:24px;font-weight:700}
.tag{font-size:13px;color:rgba(255,255,255,0.75)}

/* landing */
.landing{margin-top:18px;padding:18px;border-radius:12px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.03);text-align:center}
.choices{display:flex;gap:18px;justify-content:center;margin-top:12px}
.choice{width:260px;padding:18px;border-radius:12px;background:rgba(255,255,255,0.02);cursor:pointer;border:1px solid rgba(255,255,255,0.03);transition:transform .18s}
.choice:hover{transform:translateY(-6px);box-shadow:0 8px 30px rgba(0,0,0,0.5)}

/* pages */
.topnav{display:flex;gap:12px;margin-top:18px;align-items:center}
.topnav button{background:transparent;border:1px solid rgba(255,255,255,0.05);padding:8px 10px;border-radius:8px;color:var(--muted);cursor:pointer}
.page{display:none;margin-top:18px}
.page.active{display:block}

/* study grid */
.study-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px}
.study-card{background:linear-gradient(180deg, rgba(255,255,255,0.01),rgba(255,255,255,0.00));padding:14px;border-radius:10px;border:1px solid rgba(255,255,255,0.03);cursor:pointer}
.study-card h3{margin:0 0 6px 0}
.small-muted{font-size:13px;color:rgba(255,255,255,0.7)}

/* tools area */
.tools-wrap{display:grid;grid-template-columns:1fr 360px;gap:14px;margin-top:12px}
@media (max-width:980px){ .tools-wrap{grid-template-columns:1fr} .choices{flex-direction:column} }

.card{background:rgba(0,0,0,0.25);padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}

/* games grid */
.games-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px}

/* bookmarklets */
.bm-board{display:flex;flex-wrap:wrap;gap:12px}

/* update modal new style */
.update-modal{position:fixed;left:50%;top:50%;transform:translate(-50%,-50%);background:#242424;border:3px solid var(--accent);border-radius:12px;padding:20px 24px;z-index:9999;display:none;width:420px;box-shadow:0 12px 40px rgba(0,0,0,0.6)}
.update-title{color:#fff;font-weight:700;font-size:18px;text-align:center}
.update-desc{color:#ddd;font-size:13px;text-align:center;margin-top:8px}
.update-bar-wrap{margin-top:14px;background:#1a1a1a;border-radius:10px;height:14px;overflow:hidden;border:1px solid rgba(255,255,255,0.04)}
.update-bar{height:100%;width:0%;background:linear-gradient(90deg,var(--accent),#ffb76b);transition:width .18s linear}

/* full-screen overlay modal used for game iframe */
.full-screen{position:fixed;inset:0;background:rgba(0,0,0,0.85);display:none;align-items:center;justify-content:center;z-index:9998}
.modal-card{background:#0f0f10;border-radius:10px;padding:12px;max-width:92%;width:100%;max-height:90vh;overflow:auto}

/* footer */
.footer{margin-top:18px;text-align:center;color:rgba(255,255,255,0.6);font-size:13px}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <div class="brand">gamesunbloocked67 <span class="small-muted">v1.8</span></div>
    <div class="tag">Study & Games — Oak Middle School (7th grade) • <a href="https://www.youtube.com/@cursedgamer2" target="_blank">Subscribe</a></div>
  </div>

  <div id="landing" class="landing">
    <div style="font-weight:700;font-size:18px">Choose — Study or Play</div>
    <div class="choices">
      <div class="choice" onclick="enterMode('study')"><h3>Study Mode</h3><div class="small-muted">Tools, AI Tutor, flashcards, quizzes, and more</div></div>
      <div class="choice" onclick="enterMode('games')"><h3>Games Mode</h3><div class="small-muted">Play games (please avoid school devices)</div></div>
    </div>
    <div style="margin-top:10px" class="small-muted">Support: <a href="https://www.youtube.com/@cursedgamer2" target="_blank">YouTube.com/@cursedgamer2</a></div>
  </div>

  <div id="app" style="display:none">
    <div class="topnav" role="navigation">
      <button onclick="showSection('home')">Home</button>
      <button onclick="showSection('study')">Study</button>
      <button onclick="showSection('games')">Games</button>
      <button onclick="showSection('bookmarklets')">Bookmarks</button>
      <button onclick="showSection('updates')">Updates</button>
    </div>

    <!-- HOME -->
    <div id="home" class="page active">
      <div class="card"><h3>Welcome</h3><div class="small-muted">Select Study for learning tools or Games for fun. Reminder: don't play on school iPads.</div></div>
    </div>

    <!-- STUDY -->
    <div id="study" class="page">
      <h2>Study Hub — AI-powered</h2>
      <div class="small-muted">v1.8 adds new AI Real Tutor and subject-specific helpers. Use the Update page to install new features.</div>

      <div class="study-grid" id="studyGrid" style="margin-top:12px">
        <!-- Cards added dynamically -->
      </div>

      <div class="tools-wrap" style="margin-top:12px">
        <div class="card" style="min-height:350px">
          <h3>AI Tutor — Real Tutor</h3>
          <div class="small-muted">Ask study questions — math, science, history, reading, or writing. Get step-by-step hints and practice prompts.</div>
          <input id="aiInput" placeholder="Ask me... e.g. Solve 3/4 + 1/2 or Explain photosynthesis" style="width:100%;padding:8px;margin-top:10px;border-radius:6px;background:#0b0b0b;border:1px solid rgba(255,255,255,0.03);color:#fff">
          <div style="margin-top:8px;display:flex;gap:8px">
            <button class="btn" onclick="runAITutor()">Ask</button>
            <button class="btn" onclick="clearAI()">Clear</button>
            <button class="btn" onclick="openSpacedRep()">Start Spaced Rep</button>
          </div>
          <div id="aiResult" style="margin-top:12px;background:rgba(0,0,0,0.2);padding:10px;border-radius:8px;min-height:80px;color:#fff"></div>
        </div>

        <div style="display:flex;flex-direction:column;gap:12px">
          <div class="card">
            <h4>Flashcards</h4>
            <div style="display:flex;gap:8px;margin-top:8px">
              <input id="fcTerm" placeholder="Term" style="flex:1;padding:8px;border-radius:6px;background:#0b0b0b;border:1px solid rgba(255,255,255,0.03);color:#fff">
              <input id="fcDef" placeholder="Definition" style="flex:2;padding:8px;border-radius:6px;background:#0b0b0b;border:1px solid rgba(255,255,255,0.03);color:#fff">
              <button class="btn small" onclick="addFlashcard()">Add</button>
            </div>
            <div style="display:flex;gap:6px;margin-top:8px">
              <button class="btn small" onclick="exportFlashcards()">Export</button>
              <button class="btn small" onclick="importFlashcards()">Import</button>
              <button class="btn small" onclick="shuffleFlashcards()">Shuffle</button>
            </div>
            <div id="fcList" style="margin-top:8px;max-height:220px;overflow:auto"></div>
          </div>

          <div class="card">
            <h4>Study Timer</h4>
            <div style="display:flex;gap:8px;align-items:center">
              <input id="timerMin" type="number" value="25" style="width:80px;padding:6px;border-radius:6px;background:#0b0b0b;border:1px solid rgba(255,255,255,0.03);color:#fff">
              <button class="btn small" onclick="startTimer()">Start</button>
              <button class="btn small" onclick="stopTimer()">Stop</button>
            </div>
            <div id="timerDisplay" style="margin-top:8px">00:00</div>
          </div>

          <div class="card">
            <h4>Vocabulary Builder</h4>
            <input id="vTerm" placeholder="word" style="width:100%;padding:6px;border-radius:6px;margin-top:6px;background:#0b0b0b;color:#fff">
            <input id="vDef" placeholder="definition" style="width:100%;padding:6px;border-radius:6px;margin-top:6px;background:#0b0b0b;color:#fff">
            <div style="margin-top:8px"><button class="btn small" onclick="addVocab()">Add</button></div>
            <div id="vocabList" style="margin-top:8px;max-height:120px;overflow:auto"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- GAMES -->
    <div id="games" class="page">
      <h2>Games Library</h2>
      <div class="small-muted">Please avoid using school devices. New games arrive in updates.</div>
      <div id="gamesGrid" class="games-grid" style="margin-top:12px"></div>

      <div class="card" style="margin-top:12px">
        <h4>Game Update Simulator</h4>
        <div style="display:flex;gap:8px;align-items:center">
          <select id="simGameSelect" style="padding:8px;border-radius:6px;background:#0b0b0b;color:#fff"></select>
          <button class="btn small" onclick="simulateUpdate(simGameSelect.value,'games')">Simulate Update</button>
        </div>
      </div>
    </div>

    <!-- BOOKMARKLETS -->
    <div id="bookmarklets" class="page">
      <h2>Bookmarklets</h2>
      <div class="small-muted">Drag to reorder. Click Copy to copy code.</div>
      <div id="bmBoard" class="bm-board" style="margin-top:12px"></div>
    </div>

    <!-- UPDATES -->
    <div id="updates" class="page">
      <h2>Updates & Version Manager</h2>
      <div id="updatesTiles" style="display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:12px;margin-top:12px"></div>
      <div style="margin-top:16px" class="card"><h4>Changelog</h4><div id="changelog" style="margin-top:8px"></div></div>
    </div>
  </div>

  <div id="updateModal" class="update-modal">
    <div id="updateTitle" class="update-title">Downloading Update v1.4.0...</div>
    <div id="updateDesc" class="update-desc">Installing core features...</div>
    <div class="update-bar-wrap"><div id="updateBar" class="update-bar"></div></div>
  </div>

  <div id="fs" class="full-screen">
    <div class="modal-card" id="fsCard"></div>
  </div>

  <div class="footer">© 2025 gamesunbloocked67 — v1.8</div>
</div>

<script>
/* ----------------- Versioned data ----------------- */
const VERSIONS = {
  '1.0': {id:'1.0',title:'v1.0',desc:'Base: flashcards, basic games', games:[{title:'Snake',src:'https://www.google.com/logos/2010/snake_arcade/snake_arcade.html'},{title:'T-Rex Dino',src:'https://chromedino.com/'}], studyTools:['Flashcards','Timer']},
  '1.2': {id:'1.2',title:'v1.2',desc:'Added 2048, basic AI tutor', games:[{title:'2048',src:'https://play2048.co/'}], studyTools:['Vocabulary Builder']},
  '1.4': {id:'1.4',title:'v1.4',desc:'Retro Bowl + update simulator', games:[{title:'Retro Bowl',src:'https://retrobowlgame.io/'}], studyTools:['Quiz Maker']},
  '1.6': {id:'1.6',title:'v1.6',desc:'UI improvements, bookmarklets', games:[{title:'FNAF 1',src:'https://selenite.cc/resources/semag/fnaf/index.html'},{title:'Tetris',src:'https://tetris.com/play-tetris'}], studyTools:['Spaced Rep']},
  '1.8': {id:'1.8',title:'v1.8',desc:'AI Real Tutor & Subject AIs, Study Games', games:[{title:'Vocabulary Quest (Study)',src:'https://example.com/vocabquest'},{title:'Math Mini-Golf (Study)',src:'https://example.com/mathminigolf'}], studyTools:['AI Real Tutor','Subject Helpers','Study Games'], planned:false}
};

let CURRENT_VERSION = localStorage.getItem('gs_version') || '1.6';
/* ----------------- Mode flow ----------------- */
function enterMode(m){
  document.getElementById('landing').style.display='none';
  document.getElementById('app').style.display='block';
  showSection(m==='games'?'games':'study');
  populateAll();
  if(m==='games') setTimeout(()=>alert('pls do not play these on ur school iPad'),120);
}

/* show sections */
function showSection(id){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  const el = document.getElementById(id);
  if(el) el.classList.add('active');
}

/* ----------------- Render content ----------------- */
function populateAll(){
  renderStudyGrid();
  renderGamesGrid();
  renderBookmarklets();
  renderUpdates();
  populateSimSelects();
}

/* study grid */
function renderStudyGrid(){
  const grid = document.getElementById('studyGrid');
  grid.innerHTML='';
  const cards = [
    {id:'aiReal',title:'AI Real Tutor',desc:'Step-by-step help & practice',action:()=>openAITutor()},
    {id:'mathAI',title:'Math Solver',desc:'Work through equations with steps',action:()=>openMath()},
    {id:'scienceAI',title:'Science Explainer',desc:'Clear science concepts',action:()=>openScience()},
    {id:'historyAI',title:'History Helper',desc:'Summaries and timelines',action:()=>openHistory()},
    {id:'readingAI',title:'Reading Helper',desc:'Passage breakdowns',action:()=>openReading()},
    {id:'writingAI',title:'Writing Coach',desc:'Essay structure & grammar',action:()=>openWriting()},
    {id:'flashcards',title:'Flashcards',desc:'Create & review sets',action:()=>openFlashcards()},
    {id:'quizMaker',title:'Quiz Builder',desc:'Create practice quizzes',action:()=>openQuizMaker()},
    {id:'studyGames',title:'Study Games',desc:'Fun games that teach',action:()=>openStudyGames()},
    {id:'conceptMap',title:'Concept Map',desc:'Quick mind maps',action:()=>openConceptMap()}
  ];
  cards.forEach(c=>{
    const d=document.createElement('div'); d.className='study-card card'; d.onclick=c.action;
    d.innerHTML = `<h3>${c.title}</h3><div class="small-muted">${c.desc}</div>`;
    grid.appendChild(d);
  });
}

/* games grid */
function renderGamesGrid(){
  const ggrid = document.getElementById('gamesGrid');
  ggrid.innerHTML='';
  const games = [].concat(VERSIONS['1.0'].games || [], VERSIONS['1.2'].games || [], VERSIONS['1.4'].games || [], VERSIONS['1.6'].games || [], VERSIONS['1.8'].games || []);
  games.forEach(g=>{
    const el=document.createElement('div'); el.className='card';
    el.innerHTML = `<h4>${escapeHtml(g.title)}</h4><div class="small-muted">${escapeHtml(g.src)}</div><div style="margin-top:8px"><button class="btn small" onclick="openGame('${encodeURIComponent(g.src)}')">Play</button></div>`;
    ggrid.appendChild(el);
  });
}

/* open game modal */
function openGame(urlEnc){
  const url = decodeURIComponent(urlEnc);
  const fs = document.getElementById('fs'); const card = document.getElementById('fsCard');
  card.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><strong>Game</strong><button class="btn small" onclick="closeFS()">Close</button></div><div style="height:72vh;margin-top:8px;"><iframe src="${escapeHtml(url)}" style="width:100%;height:100%;border:none"></iframe></div>`;
  fs.style.display='flex';
}
function closeFS(){ document.getElementById('fs').style.display='none'; document.getElementById('fsCard').innerHTML=''; }

/* bookmarklets */
function renderBookmarklets(){
  const board = document.getElementById('bmBoard'); board.innerHTML='';
  const bms = [
    {title:'Edit Page',code:"javascript:(function(){document.body.contentEditable='true';})();"},
    {title:'Mini Browser',code:"javascript:(function(){var i=document.createElement('iframe');i.src='https://www.google.com/';i.style.position='fixed';i.style.right='10px';i.style.top='10px';i.style.width='420px';i.style.height='300px';i.style.zIndex=99999;document.body.appendChild(i);})();"},
    {title:'Show Cookies',code:"javascript:(function(){alert(document.cookie);})();"}
  ];
  bms.forEach((b,i)=>{
    const c=document.createElement('div'); c.className='bm-card card';
    c.innerHTML = `<strong>${b.title}</strong><div style="font-size:12px;margin-top:8px;color:#ddd;white-space:pre-wrap">${escapeHtml(b.code)}</div><div style="margin-top:8px"><button class="btn small" onclick="copyBM(${i})">Copy</button></div>`;
    board.appendChild(c);
  });
}

/* updates */
function renderUpdates(){
  const out = document.getElementById('updatesTiles'); out.innerHTML='';
  Object.values(VERSIONS).forEach(v=>{
    const d=document.createElement('div'); d.className='card';
    d.innerHTML = `<h4>${escapeHtml(v.title)}</h4><div class="small-muted">${escapeHtml(v.desc)} ${v.planned? '(planned)':''}</div>
      <div style="margin-top:8px;display:flex;gap:8px"><button class="btn small" onclick="startUpdate('${v.id}','games')">Install (Games)</button><button class="btn small" onclick="startUpdate('${v.id}','study')">Install (Study)</button></div>
      <div style="margin-top:10px"><div class="small-muted">Progress</div><div class="update-bar-wrap"><div id="bar-${v.id}" class="update-bar"></div></div></div>`;
    out.appendChild(d);

    // changelog list
    const ch = document.getElementById('changelog');
    const li = document.createElement('div'); li.style.marginBottom='8px';
    li.innerHTML = `<strong>${escapeHtml(v.title)}</strong> — ${escapeHtml(v.desc)} <div class="small-muted">Adds: ${escapeHtml((v.games||[]).map(g=>g.title).join(', '))}; Tools: ${escapeHtml((v.studyTools||[]).join(', '))}</div>`;
    ch.appendChild(li);
  });
}

/* populate simulator select */
function populateSimSelects(){
  const sel = document.getElementById('simGameSelect'); sel.innerHTML='';
  Object.values(VERSIONS).forEach(v=>{ const o=document.createElement('option'); o.value=v.id; o.text = v.title; sel.appendChild(o); });
}

/* start update with modal & progress */
function startUpdate(versionId, type){
  // show modal
  const modal = document.getElementById('updateModal');
  const title = document.getElementById('updateTitle');
  const desc = document.getElementById('updateDesc');
  const bar = document.getElementById('updateBar');
  title.innerText = `Downloading Update ${versionId}...`;
  desc.innerText = `Installing ${type} features...`;
  bar.style.width='0%';
  modal.style.display='block';
  let p=0;
  const inter = setInterval(()=>{
    p += Math.random()*14 + 3;
    if(p>100) p = 100;
    bar.style.width = p + '%';
    // reflect also in small progress bars per tile
    const tileBar = document.getElementById(`bar-${versionId}`);
    if(tileBar) tileBar.style.width = Math.min(100, p) + '%';
    if(p>=100){
      clearInterval(inter);
      setTimeout(()=>{
        modal.style.display='none';
        // "apply" update: for demo we'll set CURRENT_VERSION
        CURRENT_VERSION = versionId;
        localStorage.setItem('gs_version', CURRENT_VERSION);
        alert(`Installed ${versionId} (${type})`);
        renderGamesGrid();
        renderStudyGrid();
        renderUpdates();
      },700);
    }
  },150);
}

/* simulate update (from games page) */
function simulateUpdate(versionId,type){
  startUpdate(versionId,type);
}

/* ----------------- AI Tutor & Subject helpers ----------------- */
function runAITutor(){
  const q = document.getElementById('aiInput').value.trim();
  const out = document.getElementById('aiResult');
  if(!q){ out.innerText = 'Type a question or problem to get help.'; return; }
  const reply = aiResponder(q);
  out.innerHTML = `<strong>Q:</strong> ${escapeHtml(q)}<hr><strong>Hint:</strong> ${escapeHtml(reply.hint)}<div style="margin-top:10px"><strong>Example / Steps:</strong><div>${escapeHtml(reply.example)}</div></div>`;
}
function clearAI(){ document.getElementById('aiInput').value=''; document.getElementById('aiResult').innerText=''; }

function aiResponder(q){
  const s = q.toLowerCase();
  if(s.match(/\d/)){
    const mathRes = tryMathSolve(s);
    if(mathRes.found) return {hint: mathRes.hint, example: mathRes.example};
  }
  if(s.includes('fraction')||s.includes('/')){
    return {hint:'Find a common denominator or convert to decimals to compare.', example:'e.g. 1/2 + 1/3 = 3/6 + 2/6 = 5/6'};
  }
  if(s.includes('photosynthesis')||s.includes('cell')){
    return {hint:'Describe inputs, process, and outputs. For photosynthesis: sunlight + CO2 + water → glucose + O2.', example:'Plants use chloroplasts to capture light energy.'};
  }
  if(s.includes('revolution')||s.includes('colonial')||s.includes('history')){
    return {hint:'Think cause → event → effect. List key dates and main people.', example:'E.g., taxation without representation led to colonial protest.'};
  }
  if(s.includes('essay')||s.includes('paragraph')||s.includes('grammar')){
    return {hint:'Start with a clear topic sentence, add 2-3 supporting details, end with a conclusion.', example:'Topic sentence: The industrial revolution changed how goods were produced.'};
  }
  return {hint:'Break the problem into smaller steps; identify key vocabulary; try an example.', example:'Summarize the main idea in one sentence.'};
}
function tryMathSolve(s){
  try{
    const cleaned = s.replace(/[^0-9+\-*/().\/ ]/g,'');
    if(cleaned.match(/[0-9]/) && cleaned.match(/[\+\-\*\/]/)){
      const val = Function('"use strict";return ('+cleaned+')')();
      return {found:true, hint:'Compute step-by-step and check your arithmetic.', example:`Result ≈ ${val}`};
    }
  }catch(e){}
  return {found:false};
}

/* helpers */
function openAITutor(){ showSection('study'); document.getElementById('aiInput').focus(); }
function openMath(){ showSection('study'); document.getElementById('aiInput').value='Solve: 3/4 + 1/2'; runAITutor(); }
function openScience(){ showSection('study'); document.getElementById('aiInput').value='Explain photosynthesis'; runAITutor(); }
function openHistory(){ showSection('study'); document.getElementById('aiInput').value='Causes of the American Revolution'; runAITutor(); }
function openReading(){ showSection('study'); document.getElementById('aiInput').value='How to summarize a paragraph'; runAITutor(); }
function openWriting(){ showSection('study'); document.getElementById('aiInput').value='Help me write an introduction paragraph'; runAITutor(); }
function openFlashcards(){ showSection('study'); document.getElementById('fcTerm').focus(); }
function openQuizMaker(){ alert('Quiz builder: create multiple choice or short answer quizzes (coming soon)'); }
function openStudyGames(){ showSection('games'); alert('Study Games area — use Updates to install more.'); }
function openConceptMap(){ alert('Concept Map tool opens (simple text nodes for now).'); }

/* ----------------- Flashcards & vocab ----------------- */
let FLASHCARDS = JSON.parse(localStorage.getItem('gs_flashcards')||'[]');
function addFlashcard(){ const t=document.getElementById('fcTerm').value.trim(); const d=document.getElementById('fcDef').value.trim(); if(!t||!d){alert('Add term and definition');return;} FLASHCARDS.push({term:t,def:d}); localStorage.setItem('gs_flashcards',JSON.stringify(FLASHCARDS)); renderFlashcards(); document.getElementById('fcTerm').value=''; document.getElementById('fcDef').value=''; }
function renderFlashcards(){ const el=document.getElementById('fcList'); el.innerHTML=''; FLASHCARDS.forEach((f,i)=>{ const r=document.createElement('div'); r.style.marginTop='6px'; r.innerHTML=`<strong>${escapeHtml(f.term)}</strong><div style="font-size:13px;color:#ddd">${escapeHtml(f.def)}</div><div style="margin-top:6px"><button class="btn small" onclick="removeFlash(${i})">Remove</button></div>`; el.appendChild(r); }); }
function removeFlash(i){ FLASHCARDS.splice(i,1); localStorage.setItem('gs_flashcards',JSON.stringify(FLASHCARDS)); renderFlashcards(); }
function exportFlashcards(){ prompt('Copy JSON', JSON.stringify(FLASHCARDS)); }
function importFlashcards(){ const s=prompt('Paste JSON'); try{ const arr=JSON.parse(s||'[]'); if(Array.isArray(arr)) { FLASHCARDS = FLASHCARDS.concat(arr); localStorage.setItem('gs_flashcards',JSON.stringify(FLASHCARDS)); renderFlashcards(); alert('Imported'); } }catch(e){alert('Invalid JSON');} }
function shuffleFlashcards(){ shuffle(FLASHCARDS); renderFlashcards(); }

/* vocab */
let VOC = JSON.parse(localStorage.getItem('gs_vocab')||'[]');
function addVocab(){ const w=document.getElementById('vTerm').value.trim(), d=document.getElementById('vDef').value.trim(); if(!w||!d){alert('Add word & def');return;} VOC.push({w:d? w:'' ,d:d}); localStorage.setItem('gs_vocab',JSON.stringify(VOC)); renderVocab(); document.getElementById('vTerm').value=''; document.getElementById('vDef').value=''; }
function renderVocab(){ const el=document.getElementById('vocabList'); el.innerHTML=''; VOC.forEach((v,i)=>{ const r=document.createElement('div'); r.style.marginTop='6px'; r.innerHTML=`<strong>${escapeHtml(v.w)}</strong><div style="font-size:13px;color:#ddd">${escapeHtml(v.d)}</div><div style="margin-top:6px"><button class="btn small" onclick="removeVocab(${i})">Remove</button></div>`; el.appendChild(r); }); }
function removeVocab(i){ VOC.splice(i,1); localStorage.setItem('gs_vocab',JSON.stringify(VOC)); renderVocab(); }

/* spaced repetition stub */
function openSpacedRep(){ alert('Spaced repetition: starts a session reviewing flashcards one by one. (Simple version)'); }

/* ----------------- Timer ----------------- */
let timerInt=null;
function startTimer(){ const m=parseInt(document.getElementById('timerMin').value)||25; let s=m*60; clearInterval(timerInt); timerInt=setInterval(()=>{ s--; const mm=String(Math.floor(s/60)).padStart(2,'0'); const ss=String(s%60).padStart(2,'0'); document.getElementById('timerDisplay').innerText=mm+':'+ss; if(s<=0){ clearInterval(timerInt); alert('Time up!'); } },1000); }
function stopTimer(){ clearInterval(timerInt); }

/* ----------------- Utilities ----------------- */
function copyBM(i){ alert('Copied bookmarklet code to clipboard (demo)'); }
function escapeHtml(s){ if(!s) return ''; return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;'); }
function shuffle(arr){ for(let i=arr.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [arr[i],arr[j]]=[arr[j],arr[i]]; } }

/* ----------------- init on load ----------------- */
document.addEventListener('DOMContentLoaded', ()=>{
  renderFlashcards(); renderVocab(); populateAll(); populateSimSelects();
  const sim = document.getElementById('simGameSelect');
  Object.values(VERSIONS).forEach(v=>{ const o=document.createElement('option'); o.value=v.id; o.text = v.title; sim.appendChild(o); });
});

</script>
</body>
</html>
