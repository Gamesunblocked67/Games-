<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>gamesunbloocked67 — v2.0</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{--bg:#0d0420;--card:#151224;--accent:#ff7a38;--muted:#dcd3ef}
*{box-sizing:border-box}
html,body{margin:0;height:100%;font-family:Roboto,system-ui,-apple-system,Segoe UI,Arial;background:linear-gradient(180deg,var(--bg),#30103b);color:var(--muted)}
.container{max-width:1100px;margin:18px auto;padding:18px}
.header{display:flex;justify-content:space-between;align-items:center}
.brand{font-weight:700;font-size:20px}
.note{font-size:13px}
nav{display:flex;gap:10px;margin-top:12px}
.tab{padding:8px 12px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer}
.tab.active{outline:2px solid rgba(255,255,255,0.04);box-shadow:0 6px 24px rgba(0,0,0,0.6)}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:12px;margin-top:14px}
.card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));padding:14px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
.small{font-size:13px;color:rgba(255,255,255,0.8)}
.btn{background:var(--accent);color:#120018;padding:8px 10px;border-radius:8px;border:none;font-weight:700;cursor:pointer}
.corner-update{position:fixed;right:18px;bottom:18px;width:56px;height:56px;border-radius:50%;background:var(--accent);display:none;align-items:center;justify-content:center;font-size:20px;cursor:pointer;box-shadow:0 8px 30px rgba(0,0,0,0.6)}
.full-screen{position:fixed;inset:0;background:rgba(0,0,0,0.85);display:none;align-items:center;justify-content:center;z-index:9998}
.modal-card{background:#0b0b0b;padding:12px;border-radius:8px;width:90%;max-width:1100px;max-height:92vh;overflow:auto;color:#fff}
.subject-row{display:flex;gap:8px;flex-wrap:wrap;margin-top:10px}
.subject-btn{flex:1;padding:10px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer;text-align:center}
.notice{background:rgba(255,255,255,0.02);padding:10px;border-radius:8px;margin-top:12px;color:var(--muted)}
.footer{margin-top:18px;text-align:center;color:rgba(255,255,255,0.6);font-size:13px}
input,textarea,select{width:100%;padding:8px;border-radius:6px;border:1px solid rgba(255,255,255,0.03);background:#0a0710;color:var(--muted)}
label{font-size:13px}
.draggable{position:fixed;top:80px;right:20px;background:#151224;padding:10px;border:1px solid var(--accent);border-radius:8px;z-index:99999;cursor:move;overflow:auto;max-height:70vh;}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <div class="brand">gamesunbloocked67 <small id="ver">(v2.0)</small></div>
    <div class="note">Support: <a href="https://m.youtube.com/@cursedgamer2?sub_confirmation=1" target="_blank" style="color:var(--muted)">Subscribe Here</a></div>
  </div>
  <nav>
    <div class="tab active" id="tab-games" onclick="showTab('games')">Games</div>
    <div class="tab" id="tab-study" onclick="showTab('study')">Study</div>
    <div class="tab" id="tab-bookmarklets" onclick="showTab('bookmarklets')">Bookmarklets</div>
    <div class="tab" id="tab-settings" onclick="showTab('settings')">Settings</div>
  </nav>

  <!-- Games -->
  <section id="games" style="display:block">
    <div class="grid" id="gamesGrid"></div>
    <div class="notice">Click a game to play in full-screen mode.</div>
  </section>

  <!-- Study -->
  <section id="study" style="display:none">
    <div class="card">
      <h3>Study Hub</h3>
      <div class="small">Subjects and dynamic tools:</div>
      <div style="margin-top:12px" id="subjectsWrap"></div>
    </div>
  </section>

  <!-- Bookmarklets -->
  <section id="bookmarklets" style="display:none">
    <div class="grid" id="bookmarkletsGrid"></div>
    <div class="notice">Draggable bookmarklets for browser tools.</div>
  </section>

  <!-- Settings -->
  <section id="settings" style="display:none">
    <div class="card">
      <h3>Settings</h3>
      <label>Tab Cloaker URL:</label>
      <input type="text" id="tabCloaker" value="https://drive.google.com/">
      <button class="btn" onclick="applyCloaker()">Apply Cloaker</button>
    </div>
  </section>

  <div class="footer">© 2025 gamesunbloocked67</div>
</div>

<!-- Fullscreen Modal -->
<div class="full-screen" id="fs">
  <div class="modal-card" id="fsCard"></div>
</div>

<script>
const appState = {
  version:'2.0',
  games:[
    {id:'2048',title:'2048',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/2048/index.html'},
    {id:'1v1soccer',title:'1v1 Soccer',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/1v1soccer/index.html'},
    {id:'badicecream',title:'Bad Ice Cream',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/badicecream/index.html'},
    {id:'chess',title:'Chess',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/chess/index.html'},
    {id:'tetris',title:'Tetris',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/tetris/index.html'},
    {id:'snake',title:'Snake',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/snake/index.html'},
    {id:'soccerphysics',title:'Soccer Physics',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/soccerphysics/index.html'},
    {id:'basketball',title:'Basketball',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/basketball/index.html'},
    {id:'checkers',title:'Checkers',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/checkers/index.html'},
    {id:'pingpong',title:'Ping Pong',src:'https://cdn.jsdelivr.net/gh/selenite-cc/selenite-old@main/pingpong/index.html'}
  ],
  subjects:{
    Math:['Quick Lessons','Practice Quiz','Flashcard Maker','AI Tutor'],
    English:['Quick Lessons','Practice Quiz','Flashcard Maker','AI Tutor']
  },
  bookmarklets:[
    {id:'editpage',title:'Edit Any Page',code:"javascript:(function(){document.body.contentEditable='true'; alert('Edit mode ON');})();"},
    {id:'devconsole',title:'Open Dev Console',code:"javascript:(function(){console.log('Dev Console'); alert('Check console');})();"}
  ],
  tabCloaker:'https://drive.google.com/'
};

function showTab(tab){
  document.getElementById('games').style.display=tab==='games'?'block':'none';
  document.getElementById('study').style.display=tab==='study'?'block':'none';
  document.getElementById('bookmarklets').style.display=tab==='bookmarklets'?'block':'none';
  document.getElementById('settings').style.display=tab==='settings'?'block':'none';
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.getElementById('tab-'+tab).classList.add('active');
  if(tab==='games') renderGames();
  if(tab==='study') renderSubjects();
  if(tab==='bookmarklets') renderBookmarklets();
}

function renderGames(){
  const g=document.getElementById('gamesGrid'); g.innerHTML='';
  appState.games.forEach(game=>{
    const card=document.createElement('div'); card.className='card';
    card.innerHTML=`<h4>${game.title}</h4><div style="margin-top:10px"><button class="btn" onclick="playGame('${game.src}')">Play</button></div>`;
    g.appendChild(card);
  });
}

function playGame(src){
  const fs=document.getElementById('fs'); const card=document.getElementById('fsCard');
  card.innerHTML=`<div style="display:flex;justify-content:space-between;align-items:center"><strong>Game</strong><button class="btn" onclick="closeFS()">Close</button></div>
  <div style="height:80vh;margin-top:8px"><iframe src="${src}" style="width:100%;height:100%;border:none"></iframe></div>`;
  fs.style.display='flex';
}

function closeFS(){document.getElementById('fs').style.display='none';document.getElementById('fsCard').innerHTML='';}

function renderSubjects(){
  const wrap=document.getElementById('subjectsWrap'); wrap.innerHTML='';
  Object.keys(appState.subjects).forEach(s=>{
    const card=document.createElement('div'); card.className='card';
    card.innerHTML=`<h4>${s}</h4>
    <div class="subject-row">
      <div class="subject-btn" onclick="openQuickLesson('${s}')">Quick Lessons</div>
      <div class="subject-btn" onclick="openPracticeQuizPrompt('${s}')">Practice Quiz</div>
      <div class="subject-btn" onclick="openFlashcardMakerPrompt('${s}')">Flashcard Maker</div>
      <div class="subject-btn" onclick="openAITeacher('${s}')">AI Tutor</div>
    </div>`;
    wrap.appendChild(card);
  });
}

function openQuickLesson(subject){
  openFS(`${subject} — Quick Lessons`, `<p><strong>${subject}</strong></p><p class="small">Key points overview (demo).</p>`);
}

function openPracticeQuizPrompt(subject){
  const promptText=prompt(`Enter a topic for a practice quiz in ${subject}`);
  if(!promptText) return;
  const qs=Array.from({length:5},(_,i)=>({
    q:`Question about ${promptText} #${i+1}`,
    choices:[`Correct ${i+1}`,`Option A`,`Option B`,`Option C`].sort(()=>Math.random()-0.5),
    correct:`Correct ${i+1}`
  }));
  renderQuizModal(qs,`${subject} Quiz`);
}

function openFlashcardMakerPrompt(subject){
  const topic=prompt(`Enter a topic for flashcards in ${subject}`);
  if(!topic) return;
  const cards=Array.from({length:6},(_,i)=>({term:`${topic} Term ${i+1}`,def:`${topic} Definition ${i+1}`}));
  const html=`<h3>${subject} — Flashcards: ${topic}</h3>${cards.map(c=>`<div><strong>${c.term}</strong><div class="small">${c.def}</div></div>`).join('')}`;
  openFS('Flashcards',html);
}

function openAITeacher(subject){
  const q=prompt(`Ask the ${subject} AI Tutor a question`);
  if(!q) return;
  let ans='';
  if(subject==='Math'){ans='Break the problem into steps and solve each part carefully.';}
  else if(subject==='English'){ans='Analyze the text and provide structured guidance.';}
  else{ans='Think logically and write step by step.';}
  openFS(`${subject} AI Tutor`,`<h3>${subject} AI Tutor</h3><p><strong>Question:</strong> ${q}</p><p>${ans}</p>`);
}

function openFS(title,html){const fs=document.getElementById('fs');const card=document.getElementById('fsCard');card.innerHTML=`<div style="display:flex;justify-content:space-between;align-items:center"><strong>${title}</strong><button class="btn" onclick="closeFS()">Close</button></div><div style="margin-top:10px">${html}</div>`;fs.style.display='flex';}

function renderBookmarklets(){
  const grid=document.getElementById('bookmarkletsGrid'); grid.innerHTML='';
  appState.bookmarklets.forEach(b=>{
    const div=document.createElement('div'); div.className='draggable';
    div.innerHTML=`<strong>${b.title}</strong><br><button class="btn" onclick="${b.code}">Run</button>`;
    makeDraggable(div); grid.appendChild(div);
  });
}

function makeDraggable(el){
  let offsetX=0, offsetY=0, isDown=false;
  el.onmousedown=function(e){isDown=true; offsetX=e.clientX-el.offsetLeft; offsetY=e.clientY-el.offsetTop;}
  document.onmouseup=function(){isDown=false;}
  document.onmousemove=function(e){if(!isDown) return; el.style.left=(e.clientX-offsetX)+'px'; el.style.top=(e.clientY-offsetY)+'px';}
}

function applyCloaker(){
  const url=document.getElementById('tabCloaker').value;
  appState.tabCloaker=url;
  alert('Tab Cloaker URL applied!');
}

document.addEventListener('DOMContentLoaded',()=>{
  renderGames(); renderSubjects();
});
</script>
</body>
</html>
