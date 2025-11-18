<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>gamesunbloocked67 — v2.4 (Massive Upgrade)</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{--bg:#0d0420;--card:#151224;--accent:#ff7a38;--muted:#dcd3ef}
*{box-sizing:border-box}
html,body{margin:0;height:100%;font-family:Roboto,system-ui,-apple-system,Segoe UI,Arial;background:linear-gradient(180deg,var(--bg),#30103b);color:var(--muted)}
.container{max-width:1200px;margin:14px auto;padding:14px}
.header{display:flex;justify-content:space-between;align-items:center;gap:12px;flex-wrap:wrap}
.brand{font-weight:700;font-size:20px}
.note{font-size:13px}
nav{display:flex;gap:8px;margin-top:12px;flex-wrap:wrap}
.tab{padding:8px 12px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer}
.tab.active{outline:2px solid rgba(255,255,255,0.04);box-shadow:0 6px 24px rgba(0,0,0,0.6)}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:12px;margin-top:14px}
.card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));padding:14px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
.small{font-size:13px;color:rgba(255,255,255,0.8)}
.btn{background:var(--accent);color:#120018;padding:8px 10px;border-radius:8px;border:none;font-weight:700;cursor:pointer}
.full-screen{position:fixed;inset:0;background:rgba(0,0,0,0.9);display:none;align-items:center;justify-content:center;z-index:9999}
.modal-card{background:#0b0b0b;padding:12px;border-radius:8px;width:94%;max-width:1200px;max-height:92vh;overflow:auto;color:#fff}
.input,textarea,select{width:100%;padding:8px;border-radius:6px;border:1px solid rgba(255,255,255,0.03);background:#0a0710;color:var(--muted)}
.footer{margin-top:18px;text-align:center;color:rgba(255,255,255,0.6);font-size:13px}
.draggable{position:fixed;top:80px;right:20px;background:#151224;padding:10px;border:1px solid var(--accent);border-radius:8px;z-index:99999;cursor:move;overflow:auto;max-height:70vh}
.notice{background:rgba(255,255,255,0.02);padding:10px;border-radius:8px;margin-top:12px;color:var(--muted)}
.err{color:#ff9b9b}
label{font-size:13px;display:block;margin-top:8px}
.game-tile{display:flex;flex-direction:column;gap:8px;align-items:flex-start}
.kv{display:flex;gap:8px;align-items:center}
.small-btn{background:rgba(255,255,255,0.04);color:var(--muted);padding:6px 8px;border-radius:6px;border:1px solid rgba(255,255,255,0.02);cursor:pointer}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <div class="brand">gamesunbloocked67 <small id="ver">(v2.4)</small></div>
    <div class="note">
      Support: <a href="https://m.youtube.com/@cursedgamer2?sub_confirmation=1" target="_blank" style="color:var(--muted)">Subscribe</a>
      &nbsp;•&nbsp;
      <span id="ownerTag" class="small">Owner mode: off</span>
    </div>
  </div>

  <nav>
    <div class="tab active" id="tab-games" onclick="showTab('games')">Games</div>
    <div class="tab" id="tab-study" onclick="showTab('study')">Study</div>
    <div class="tab" id="tab-updates" onclick="showTab('updates')">Updates</div>
    <div class="tab" id="tab-bookmarks" onclick="showTab('bookmarks')">Bookmarklets</div>
    <div class="tab" id="tab-settings" onclick="showTab('settings')">Settings</div>
  </nav>

  <!-- GAMES -->
  <section id="games" style="display:block">
    <div class="grid" id="gamesGrid"></div>
    <div class="notice">Click Play. Games are fetched from the selenite-old repo and loaded into a sandboxed iframe (rewritten asset paths).</div>
  </section>

  <!-- STUDY -->
  <section id="study" style="display:none">
    <div class="card">
      <h3>Study Hub — AI Study Tools</h3>
      <div class="small">AI Teacher, AI Quiz Maker, AI Flashcards, and Math Solver. Global API key is used automatically if set by owner.</div>

      <label>AI Teacher — Ask any question (step-by-step)</label>
      <textarea id="teacherQuery" rows="4" placeholder="Explain mitosis step by step or solve 2x+3=11"></textarea>
      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="askTeacher()">Ask Teacher</button>
        <button class="btn" onclick="clearTeacher()">Clear</button>
      </div>
      <div id="teacherAnswer" class="notice" style="margin-top:12px"></div>

      <hr style="margin:14px 0;border-color:#241224">

      <label>AI Quiz Maker — topic or prompt</label>
      <input id="aiQuizTopic" class="input" placeholder="e.g. photosynthesis, fractions">
      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="makeAIQuiz()">Make AI Quiz</button>
        <button class="btn" onclick="clearAIQuiz()">Clear</button>
      </div>
      <div id="aiQuizArea" class="notice" style="margin-top:12px"></div>

      <hr style="margin:14px 0;border-color:#241224">

      <label>AI Flashcard Generator — topic</label>
      <input id="aiFlashTopic" class="input" placeholder="e.g. algebra basics">
      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="makeAIFlashcards()">Generate Flashcards</button>
        <button class="btn" onclick="clearAIFlashcards()">Clear</button>
      </div>
      <div id="aiFlashArea" class="notice" style="margin-top:12px"></div>

      <hr style="margin:14px 0;border-color:#241224">

      <label>Math Solver (local)</label>
      <input id="mathInput" class="input" placeholder="e.g. solve 3x+5=20 or compute 12*3/4">
      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="runMathSolver()">Solve</button>
        <button class="btn" onclick="clearMath()">Clear</button>
      </div>
      <div id="mathArea" class="notice" style="margin-top:12px"></div>

    </div>
  </section>

  <!-- UPDATES -->
  <section id="updates" style="display:none">
    <div class="card">
      <h3>Updates</h3>
      <div class="small">Install updates that add games and study tools. v2.4 (Massive Upgrade) is available below.</div>
      <div style="margin-top:12px" id="updatesList"></div>
      <div style="margin-top:12px"><button class="btn" onclick="installUpdate('v2.4')">Install v2.4</button></div>
    </div>
  </section>

  <!-- BOOKMARKLETS -->
  <section id="bookmarks" style="display:none">
    <div class="card">
      <h3>Bookmarklets</h3>
      <div class="small">Drag the panel on the right to keep bookmarklets handy.</div>
      <div id="bmList" style="margin-top:12px"></div>
    </div>
  </section>

  <!-- SETTINGS -->
  <section id="settings" style="display:none">
    <div class="card">
      <h3>Settings & Owner Controls</h3>
      <div class="small">Set global API key (owner only) — this key will be stored in site localStorage and used for AI requests for all visitors. Keep it secret.</div>

      <label>Owner password</label>
      <input id="ownerPass" class="input" placeholder="Enter owner password to unlock owner controls">

      <label style="margin-top:8px">Global Hugging Face API Key (owner)</label>
      <input id="globalApiKey" class="input" placeholder="hf_...">

      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="setGlobalKey()">Save Global Key (owner)</button>
        <button class="btn" onclick="clearGlobalKey()">Clear Global Key</button>
      </div>

      <hr style="margin:12px 0;border-color:#241224">

      <label>Tab Cloaker Title</label>
      <input id="cloakTitle" class="input" placeholder="e.g. Google Drive">

      <label>Tab Cloaker Favicon URL (optional)</label>
      <input id="cloakFavicon" class="input" placeholder="https://.../favicon.png">

      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="applyCloak()">Apply Cloak</button>
        <button class="btn" onclick="resetCloak()">Reset Cloak</button>
      </div>
    </div>
  </section>

  <div class="footer">© 2025 gamesunbloocked67</div>
</div>

<!-- right draggable bookmarklet panel -->
<div class="draggable" id="bmPanel" title="Drag me">
  <strong style="display:block">Bookmarklets</strong>
  <div style="margin-top:8px">
    <button class="small-btn" onclick="copyBM('javascript:(function(){document.body.contentEditable=true;document.designMode=\"on\"})()')">Copy: Edit Page</button>
  </div>
  <div style="margin-top:8px">
    <button class="small-btn" onclick="copyBM('javascript:(function(){var s=document.createElement(\"script\");s.src=\"https://cdn.jsdelivr.net/npm/eruda\";document.body.appendChild(s);s.onload=function(){eruda.init()}})()')">Copy: Dev Console</button>
  </div>
  <div style="margin-top:8px">
    <button class="small-btn" onclick="copyBM('javascript:(function(){document.title=\"Google Drive\";alert(\"Cloaked\")})()')">Copy: Cloaker</button>
  </div>
</div>

<!-- fullscreen modal -->
<div class="full-screen" id="fs"><div class="modal-card" id="fsCard"></div></div>

<script>
/* =========================
   Core app state & helpers
   ========================= */
const APP = {
  version: 'v2.4',
  repoBase: 'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/',
  games: [],
  bookmarklets: [
    {id:'edit',title:'Edit Page',code:"javascript:(function(){document.body.contentEditable=true;document.designMode='on';})()"},
    {id:'eruda',title:'Dev Console',code:"javascript:(function(){var s=document.createElement('script');s.src='https://cdn.jsdelivr.net/npm/eruda';document.body.appendChild(s);s.onload=function(){eruda.init();}})()"},
    {id:'cloak',title:'Simple Cloak',code:"javascript:(function(){document.title='Google Drive';})();"}
  ],
  updates: {
    'v2.4': {
      id:'v2.4',
      title:'v2.4 — Massive Upgrade',
      desc:'Adds 20+ games and new AI study tools (AI Teacher, AI Quiz Maker, AI Flashcards, AI Math Solver).',
      applied: false,
      addGames:[
        {id:'1v1soccer', title:'1v1 Soccer', path:'1v1soccer/index.html'},
        {id:'2048', title:'2048', path:'2048/index.html'},
        {id:'badicecream', title:'Bad Ice Cream', path:'badicecream/index.html'},
        {id:'basketball', title:'Basketball', path:'basketball/index.html'},
        {id:'bombsquad', title:'Bombs', path:'bombs/index.html'},
        {id:'cars', title:'Cars', path:'cars/index.html'},
        {id:'chess', title:'Chess', path:'chess/index.html'},
        {id:'checkers', title:'Checkers', path:'checkers/index.html'},
        {id:'crooked', title:'Crooked', path:'crooked/index.html'},
        {id:'pong', title:'Pong', path:'pong/index.html'},
        {id:'tetris', title:'Tetris', path:'tetris/index.html'},
        {id:'snake', title:'Snake', path:'snake/index.html'},
        {id:'soccerphysics', title:'Soccer Physics', path:'soccerphysics/index.html'},
        {id:'run', title:'Run Game', path:'run/index.html'},
        {id:'parking', title:'Parking', path:'parking/index.html'},
        {id:'memory', title:'Memory', path:'memory/index.html'},
        {id:'minesweeper', title:'Minesweeper', path:'minesweeper/index.html'},
        {id:'shoot', title:'Shooter', path:'shooter/index.html'},
        {id:'platform', title:'Platformer', path:'platform/index.html'},
        {id:'puzzle', title:'Puzzle', path:'puzzle/index.html'}
      ]
    }
  }
};

/* simple escaping helpers */
function esc(s){ return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }
function escJs(s){ return String(s).replace(/\\/g,'\\\\').replace(/'/g,"\\'").replace(/"/g,'\\"'); }

/* -------------------------
   Page & UI management
   ------------------------- */
document.getElementById('ver').innerText = '('+APP.version+')';
function showTab(t){
  ['games','study','updates','bookmarks','settings'].forEach(id=> document.getElementById(id).style.display = (id===t)?'block':'none');
  document.querySelectorAll('.tab').forEach(el=>el.classList.remove('active'));
  document.getElementById('tab-'+t).classList.add('active');
  if(t==='games') renderGames();
  if(t==='bookmarks') renderBookmarklets();
  if(t==='updates') renderUpdates();
}
showTab('games');

/* -------------------------
   Games loader
   ------------------------- */
function renderGames(){
  const grid = document.getElementById('gamesGrid'); grid.innerHTML = '';
  if(APP.games.length === 0){
    grid.innerHTML = '<div class="card small">No games installed. Install update v2.4 from Updates tab.</div>';
    return;
  }
  APP.games.forEach(g=>{
    const card = document.createElement('div'); card.className='card game-tile';
    card.innerHTML = `<strong>${esc(g.title)}</strong>
      <div style="display:flex;gap:8px;margin-top:8px">
        <button class="btn" onclick="playGame('${escJs(g.path)}')">Play</button>
        <button class="small-btn" onclick="openRaw('${escJs(g.path)}')">Open raw</button>
      </div>
      <div class="small" id="st-${esc(g.id)}"></div>`;
    grid.appendChild(card);
  });
}

function rewriteHtmlForCDN(html, basePath){
  html = html.replace(/(<head[^>]*>)/i, `$1<base href="${basePath}">`);
  html = html.replace(/(src|href)\s*=\s*["'](?!https?:|\/\/|data:|mailto:|#)([^"']+)["']/gi, (m,p1,p2)=> `${p1}="${basePath}${p2}"`);
  html = html.replace(/url\((?!['"]?(?:https?:|data:|\/\/))['"]?([^'")]+)['"]?\)/gi, (m,p1)=> `url(${basePath}${p1})`);
  return html;
}

async function playGame(relPath){
  const fs = document.getElementById('fs'); const card = document.getElementById('fsCard');
  card.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><strong>Loading Game</strong><div><button class="btn" onclick="closeFS()">Close</button></div></div><div id="playerArea" style="height:82vh;margin-top:8px;display:flex;align-items:center;justify-content:center;color:var(--muted)">Fetching...</div>`;
  fs.style.display='flex';
  const playerArea = document.getElementById('playerArea');
  try{
    const url = APP.repoBase + relPath;
    const res = await fetch(url); const txt = await res.text();
    const rewritten = rewriteHtmlForCDN(txt, APP.repoBase + relPath.substring(0, relPath.lastIndexOf('/')+1));
    playerArea.innerHTML = `<iframe style="width:100%;height:100%;border:none" srcdoc="${escJs(rewritten)}"></iframe>`;
  }catch(e){ playerArea.innerHTML = `<div class="err">Failed to load: ${esc(e.message)}</div>`; }
}

function openRaw(relPath){ window.open(APP.repoBase + relPath,'_blank'); }
function closeFS(){ document.getElementById('fs').style.display='none'; }

/* -------------------------
   Bookmarklets panel
   ------------------------- */
function renderBookmarklets(){
  const list = document.getElementById('bmList'); list.innerHTML='';
  APP.bookmarklets.forEach(b=>{
    const btn = document.createElement('button'); btn.className='small-btn';
    btn.innerText = b.title; btn.onclick=()=>copyBM(b.code);
    list.appendChild(btn);
  });
}
function copyBM(code){ navigator.clipboard.writeText(code).then(()=>alert('Copied!')) }

/* -------------------------
   Updates
   ------------------------- */
function renderUpdates(){
  const list = document.getElementById('updatesList'); list.innerHTML='';
  Object.values(APP.updates).forEach(u=>{
    const div = document.createElement('div'); div.className='notice';
    div.innerHTML=`<strong>${esc(u.title)}</strong><br>${esc(u.desc)}<br>Status: ${u.applied?'Applied':'Not applied'}`;
    list.appendChild(div);
  });
}
function installUpdate(id){
  const u = APP.updates[id]; if(!u || u.applied) return;
  if(u.addGames) APP.games.push(...u.addGames); u.applied=true;
  alert(`${u.title} installed!`); renderGames(); renderUpdates();
}

/* -------------------------
   Owner & settings
   ------------------------- */
function setGlobalKey(){
  const pass = document.getElementById('ownerPass').value.trim();
  if(pass!=='owner123'){ alert('Wrong owner password'); return; }
  const key = document.getElementById('globalApiKey').value.trim();
  localStorage.setItem('global_api_key', key); alert('Global API key saved!');
}
function clearGlobalKey(){ localStorage.removeItem('global_api_key'); alert('Global API key cleared!'); }
function getGlobalKey(){ return localStorage.getItem('global_api_key')||''; }

function applyCloak(){ document.title=document.getElementById('cloakTitle').value||document.title;
  const url = document.getElementById('cloakFavicon').value; if(url){ let l=document.querySelector('link[rel="icon"]')||document.createElement('link'); l.rel='icon'; l.href=url; document.head.appendChild(l); } }
function resetCloak(){ document.title='gamesunbloocked67'; let l=document.querySelector('link[rel="icon"]'); if(l)l.remove(); }

/* -------------------------
   Math Solver (local)
   ------------------------- */
function runMathSolver(){
  const inp=document.getElementById('mathInput').value.trim(); const out=document.getElementById('mathArea'); if(!inp){out.innerHTML='';return;}
  try{ out.innerHTML='Computing...'; let res=eval(inp.replace(/[^-()\d/*+.]/g,'')); out.innerHTML='Result: '+res; }catch(e){ out.innerHTML='<div class="err">Error: '+esc(e.message)+'</div>'; }
}
function clearMath(){ document.getElementById('mathInput').value=''; document.getElementById('mathArea').innerHTML=''; }

/* -------------------------
   AI Tools (Hugging Face)
   ------------------------- */
function shuffle(arr){ return arr.sort(()=>Math.random()-0.5); }

/* Teacher */
async function askTeacher(){
  const q=document.getElementById('teacherQuery').value.trim();
  const out=document.getElementById('teacherAnswer'); if(!q){out.innerHTML='<div class="err">Type a question first.</div>'; return;}
  out.innerHTML='Thinking...'; const key=getGlobalKey();
  if(!key){out.innerHTML='No API key set. Enter your Hugging Face key in Settings.'; return;}
  try{
    const payload={inputs:`Explain step-by-step: ${q}`,parameters:{max_new_tokens:400,temperature:0.2}};
    const res=await fetch('https://api-inference.huggingface.co/models/gpt2',{method:'POST',headers:{'Authorization':'Bearer '+key,'Content-Type':'application/json'},body:JSON.stringify(payload)});
    const data=await res.json();
    if(data.error){out.innerHTML='<div class="err">Model error: '+esc(data.error)+'</div>';return;}
    const text=Array.isArray(data)&&data[0]?.generated_text?data[0].generated_text:(data.generated_text||JSON.stringify(data));
    out.innerHTML=text.replace(/\n/g,'<br>');
  }catch(e){out.innerHTML='<div class="err">Request failed: '+esc(e.message)+'</div>';}
}
function clearTeacher(){document.getElementById('teacherQuery').value='';document.getElementById('teacherAnswer').innerHTML='';}

/* Quiz Maker */
async function makeAIQuiz(){
  const topic=document.getElementById('aiQuizTopic').value.trim(); const area=document.getElementById('aiQuizArea');
  if(!topic){area.innerHTML='<div class="err">Enter topic</div>'; return;}
  area.innerHTML='Generating quiz...'; const key=getGlobalKey(); if(!key){area.innerHTML='No API key set. Enter your Hugging Face key in Settings.';return;}
  try{
    const prompt=`Create 5 multiple-choice questions about "${topic}" in JSON format: [{"q":"...","correct":"...","wrongs":["...","...","..."]}]`;
    const payload={inputs:prompt,parameters:{max_new_tokens:512,temperature:0.2}};
    const res=await fetch('https://api-inference.huggingface.co/models/gpt2',{method:'POST',headers:{Authorization:'Bearer '+key,'Content-Type':'application/json'},body:JSON.stringify(payload)});
    const data=await res.json();
    let text=Array.isArray(data)&&data[0]?.generated_text?data[0].generated_text:(data.generated_text||JSON.stringify(data));
    const jsonMatch=text.match(/(\[.*\])/s); const arr=jsonMatch?JSON.parse(jsonMatch[1]):JSON.parse(text);
    const qs=arr.map(item=>({q:item.q,choices:shuffle([item.correct,...(item.wrongs||[])]),correct:item.correct}));
    renderQuizInline(qs,area);
  }catch(e){area.innerHTML='<div class="err">AI quiz failed: '+esc(e.message)+'</div>';}
}
function renderQuizInline(qs,area){
  area.innerHTML=''; qs.forEach((q,i)=>{
    const div=document.createElement('div'); div.className='notice';
    div.innerHTML=`<strong>Q${i+1}: ${esc(q.q)}</strong><br>`+q.choices.map((c,j)=>`<label><input type="radio" name="q${i}" value="${esc(c)}"> ${esc(c)}</label>`).join('<br>')+`<br><small class="small">Correct: ${esc(q.correct)}</small>`;
    area.appendChild(div);
  });
}
function clearAIQuiz(){document.getElementById('aiQuizTopic').value='';document.getElementById('aiQuizArea').innerHTML='';}

/* Flashcards */
async function makeAIFlashcards(){
  const topic=document.getElementById('aiFlashTopic').value.trim(); const area=document.getElementById('aiFlashArea');
  if(!topic){area.innerHTML='<div class="err">Enter topic</div>'; return;}
  area.innerHTML='Generating flashcards...'; const key=getGlobalKey(); if(!key){area.innerHTML='No API key set. Enter your Hugging Face key in Settings.';return;}
  try{
    const prompt=`Create 6 concise flashcards about "${topic}" in JSON format: [{"front":"...","back":"..."}]`;
    const payload={inputs:prompt,parameters:{max_new_tokens:512,temperature:0.2}};
    const res=await fetch('https://api-inference.huggingface.co/models/gpt2',{method:'POST',headers:{Authorization:'Bearer '+key,'Content-Type':'application/json'},body:JSON.stringify(payload)});
    const data=await res.json();
    let text=Array.isArray(data)&&data[0]?.generated_text?data[0].generated_text:(data.generated_text||JSON.stringify(data));
    const jsonMatch=text.match(/(\[.*\])/s); const arr=jsonMatch?JSON.parse(jsonMatch[1]):JSON.parse(text);
    area.innerHTML=''; arr.forEach(f=>{ const d=document.createElement('div'); d.className='notice'; d.innerHTML=`<strong>${esc(f.front)}</strong><br>${esc(f.back)}`; area.appendChild(d); });
  }catch(e){area.innerHTML='<div class="err">AI flashcards failed: '+esc(e.message)+'</div>';}
}
function clearAIFlashcards(){document.getElementById('aiFlashTopic').value='';document.getElementById('aiFlashArea').innerHTML='';}

/* -------------------------
   Draggable
   ------------------------- */
dragElement(document.getElementById("bmPanel"));
function dragElement(elmnt){
  let pos1=0,pos2=0,pos3=0,pos4=0;
  elmnt.onmousedown=dragMouseDown;
  function dragMouseDown(e){ e=e||window.event; e.preventDefault(); pos3=e.clientX; pos4=e.clientY; document.onmouseup=closeDrag; document.onmousemove=elementDrag; }
  function elementDrag(e){ e=e||window.event; e.preventDefault(); pos1=pos3-e.clientX; pos2=pos4-e.clientY; pos3=e.clientX; pos4=e.clientY; elmnt.style.top=(elmnt.offsetTop-pos2)+'px'; elmnt.style.left=(elmnt.offsetLeft-pos1)+'px'; }
  function closeDrag(){ document.onmouseup=null; document.onmousemove=null; }
}
</script>
</body>
</html>
