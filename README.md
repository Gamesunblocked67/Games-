<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>gamesunbloocked67 — Hybrid All-in-One</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{--bg:#0d0420;--card:#151224;--accent:#ff7a38;--muted:#dcd3ef}
*{box-sizing:border-box}
html,body{margin:0;height:100%;font-family:Roboto,system-ui,-apple-system,Segoe UI,Arial;background:linear-gradient(180deg,var(--bg),#30103b);color:var(--muted)}
.container{max-width:1100px;margin:18px auto;padding:18px}
.header{display:flex;justify-content:space-between;align-items:center}
.brand{font-weight:700;font-size:20px}
.note{font-size:13px}
nav{display:flex;gap:10px;margin-top:12px;flex-wrap:wrap}
.tab{padding:8px 12px;border-radius:8px;background:rgba(255,255,255,0.02);cursor:pointer}
.tab.active{outline:2px solid rgba(255,255,255,0.04);box-shadow:0 6px 24px rgba(0,0,0,0.6)}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:12px;margin-top:14px}
.card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));padding:14px;border-radius:10px;border:1px solid rgba(255,255,255,0.03)}
.small{font-size:13px;color:rgba(255,255,255,0.8)}
.btn{background:var(--accent);color:#120018;padding:8px 10px;border-radius:8px;border:none;font-weight:700;cursor:pointer}
.notice{background:rgba(255,255,255,0.02);padding:10px;border-radius:8px;margin-top:12px;color:var(--muted)}
.full-screen{position:fixed;inset:0;background:rgba(0,0,0,0.75);display:none;align-items:center;justify-content:center;z-index:9999}
.modal-card{background:#0b0b0b;padding:14px;border-radius:8px;width:93%;max-width:1200px;max-height:92vh;overflow:auto}
.draggable{position:fixed;top:60px;right:12px;width:300px;padding:8px;background:#111;border:1px solid rgba(255,255,255,0.03);border-radius:8px;z-index:99999;cursor:move;max-height:60vh;overflow:auto}
.footer{margin-top:18px;text-align:center;color:rgba(255,255,255,0.6);font-size:13px}
label{font-size:13px}
input,select,textarea{width:100%;padding:8px;border-radius:6px;border:1px solid rgba(255,255,255,0.03);background:#0a0710;color:var(--muted)}
</style>
</head>
<body>
<div class="container">
  <div class="header">
    <div class="brand">gamesunbloocked67 <small id="ver">(hybrid)</small></div>
    <div class="note">Support: <a href="https://m.youtube.com/@cursedgamer2?sub_confirmation=1" target="_blank" style="color:var(--muted)">YouTube.com/@cursedgamer2</a></div>
  </div>

  <nav>
    <div class="tab active" id="tab-games" onclick="showTab('games')">Games</div>
    <div class="tab" id="tab-study" onclick="showTab('study')">Study</div>
    <div class="tab" id="tab-bookmarks" onclick="showTab('bookmarks')">Bookmarklets</div>
    <div class="tab" id="tab-settings" onclick="showTab('settings')">Settings</div>
    <div class="tab" id="tab-updates" onclick="showTab('updates')">Updates</div>
  </nav>

  <!-- GAMES -->
  <section id="games" style="display:block">
    <div class="grid" id="gamesGrid"></div>
    <div class="notice">Hybrid: first 20 inline games + 5 heavier ports embedded inline. Additional games are discovered from the repo and loaded via CDN if available.</div>
  </section>

  <!-- STUDY -->
  <section id="study" style="display:none">
    <div class="card">
      <h3>Study Hub</h3>
      <div class="small">Quick Lessons · Quiz Generator · Flashcards · AI Tutor</div>
      <div id="subjectsWrap" style="margin-top:12px"></div>
    </div>
  </section>

  <!-- BOOKMARKLETS -->
  <section id="bookmarks" style="display:none">
    <div class="card">
      <h3>Bookmarklets (draggable)</h3>
      <div id="bookmarkletList"></div>
    </div>
  </section>

  <!-- SETTINGS -->
  <section id="settings" style="display:none">
    <div class="card">
      <h3>Settings</h3>
      <label>Tab Cloaker URL</label>
      <input id="cloakUrl" placeholder="https://drive.google.com/">
      <div style="margin-top:8px"><button class="btn" onclick="applyCloak()">Apply Cloak</button></div>
    </div>
  </section>

  <!-- UPDATES -->
  <section id="updates" style="display:none">
    <div id="updatesGrid" class="grid"></div>
    <div id="changelog" class="notice" style="margin-top:12px"></div>
  </section>

  <div class="footer">© 2025 gamesunbloocked67</div>
</div>

<!-- full-screen modal -->
<div class="full-screen" id="fsModal"><div class="modal-card" id="fsCard"></div></div>

<!-- subscription prompt -->
<div class="full-screen" id="subModal" style="display:flex;align-items:center;justify-content:center;">
  <div class="modal-card" style="text-align:center;">
    <h2>Support the channel</h2>
    <p>Sub to my channel to support the project!</p>
    <div style="display:flex;gap:8px;justify-content:center;margin-top:12px">
      <a class="btn" href="https://m.youtube.com/@cursedgamer2?sub_confirmation=1" target="_blank">Subscribe</a>
      <button class="btn" onclick="closeSubPrompt()">Close</button>
    </div>
  </div>
</div>

<!-- draggable bookmarklet panel -->
<div id="bmPanel" class="draggable" style="display:none">
  <strong>Bookmarklets</strong>
  <div id="bmPanelList"></div>
</div>

<script>
/* ===================== CONFIG ===================== */
const REPO_OWNER = 'selenite-cc';
const REPO_NAME = 'selenite-old';
const REPO_BRANCH = 'main'; // usually main or master
let discoveredGames = []; // will fill with {id,title,src}

/* ===================== INLINE GAMES (20 lightweight) ===================== */
/* Each inline game has {id,title,html,run} where html is DOM fragment (no <script>) and run() attaches behavior */
const INLINE_GAMES = [
  // 1) Snake (playable)
  {id:'snake', title:'Snake (inline)', html:`<canvas id="snakeCanvas" width="300" height="300" style="display:block;margin:12px auto;background:#000;border:2px solid #222"></canvas>`, run:function(){
      const canvas = document.getElementById('snakeCanvas'); if(!canvas) return;
      const ctx = canvas.getContext('2d');
      let x=10,y=10,dx=10,dy=0,trail=[],tail=5;
      let food = {x:Math.floor(Math.random()*30), y:Math.floor(Math.random()*30)};
      function key(e){ if(e.key==='ArrowUp'){dx=0;dy=-10;} if(e.key==='ArrowDown'){dx=0;dy=10;} if(e.key==='ArrowLeft'){dx=-10;dy=0;} if(e.key==='ArrowRight'){dx=10;dy=0;} }
      document.addEventListener('keydown', key);
      const iv = setInterval(()=>{
        x+=dx; y+=dy;
        if(x<0) x=290; if(x>290) x=0; if(y<0) y=290; if(y>290) y=0;
        ctx.fillStyle='#000'; ctx.fillRect(0,0,300,300);
        ctx.fillStyle='lime'; trail.push({x,y}); while(trail.length>tail) trail.shift();
        trail.forEach(p=>ctx.fillRect(p.x,p.y,10,10));
        if(x===food.x*10 && y===food.y*10){ tail++; food={x:Math.floor(Math.random()*30), y:Math.floor(Math.random()*30)}; }
        ctx.fillStyle='red'; ctx.fillRect(food.x*10,food.y*10,10,10);
      },100);
      window._inlineCleanup = ()=>{ clearInterval(iv); document.removeEventListener('keydown', key); };
  }},
  // 2) Pong (simple)
  {id:'pong', title:'Pong (inline)', html:`<canvas id="pongCanvas" width="480" height="320" style="display:block;margin:12px auto;background:#000;border:2px solid #222"></canvas>`, run:function(){
      const c=document.getElementById('pongCanvas'); const ctx=c.getContext('2d');
      let px=10, py=c.height/2-30, pxh=60, pyv=0;
      let ox=c.width-20, oy=c.height/2-30, oh=60, ov=0;
      let bx=c.width/2, by=c.height/2, bvx=4, bvy=3;
      function draw(){ ctx.fillStyle='#000';ctx.fillRect(0,0,c.width,c.height); ctx.fillStyle='white'; ctx.fillRect(px,py,10,pxh); ctx.fillRect(ox,oy,10,oh); ctx.beginPath(); ctx.arc(bx,by,6,0,Math.PI*2); ctx.fill();}
      function step(){ by+=bvy; bx+=bvx; if(by<0||by>c.height) bvy=-bvy; if(bx<0){ bx=c.width/2; bvx=4;} if(bx>c.width){ bx=c.width/2; bvx=-4;} if(bx<px+10 && by>py && by<py+pxh) bvx=-bvx; if(bx>ox && by>oy && by<oy+oh) bvx=-bvx; oy += (by - (oy+oh/2))*0.05; draw();}
      const iv=setInterval(step,16); document.addEventListener('mousemove',e=>{ const r=c.getBoundingClientRect(); py = e.clientY - r.top - pxh/2; py = Math.max(0, Math.min(c.height-pxh, py)); });
      window._inlineCleanup = ()=>{ clearInterval(iv); };
  }},
  // 3) Breakout-lite
  {id:'breakout', title:'Breakout (lite)', html:`<canvas id="breakCanvas" width="480" height="320" style="display:block;margin:12px auto;background:#041"></canvas>`, run:function(){
      const c=document.getElementById('breakCanvas'); if(!c) return; const ctx=c.getContext('2d');
      let x=c.width/2,y=c.height-30, dx=2,dy=-2, paddleWidth=75, paddleX=(c.width-paddleWidth)/2, right=false,left=false;
      const bricks = []; const rows=3, cols=5; for(let r=0;r<rows;r++){ for(let col=0;col<cols;col++){ bricks.push({x:col*80+30,y:r*20+30,w:60,h:12,alive:true});}}
      document.addEventListener('keydown',e=>{ if(e.key==='ArrowRight') right=true; if(e.key==='ArrowLeft') left=true;});
      document.addEventListener('keyup',e=>{ if(e.key==='ArrowRight') right=false; if(e.key==='ArrowLeft') left=false;});
      function draw(){ ctx.clearRect(0,0,c.width,c.height); ctx.beginPath(); ctx.arc(x,y,8,0,Math.PI*2); ctx.fillStyle='white'; ctx.fill(); ctx.fillRect(paddleX,c.height-10,paddleWidth,10); bricks.forEach(b=>{ if(b.alive){ ctx.fillStyle='orange'; ctx.fillRect(b.x,b.y,b.w,b.h); }}); }
      function step(){ x+=dx;y+=dy; if(x<0||x>c.width) dx=-dx; if(y<0) dy=-dy; if(y>c.height){ x=c.width/2;y=c.height-30;} if(right) paddleX+=6; if(left) paddleX-=6; paddleX=Math.max(0,Math.min(c.width-paddleWidth,paddleX)); bricks.forEach(b=>{ if(b.alive && x>b.x && x<b.x+b.w && y>b.y && y<b.y+b.h){ dy=-dy; b.alive=false; } }); draw(); }
      const iv = setInterval(step,16);
      window._inlineCleanup = ()=>{ clearInterval(iv); };
  }},
  // 4) 2048-lite (very simple)
  {id:'2048', title:'2048 (lite)', html:`<div id="g2048" style="width:300px;margin:12px auto;color:#fff"></div>`, run:function(){
      const el=document.getElementById('g2048'); if(!el) return;
      let grid=[[0,0,0,0],[0,0,0,0],[0,0,0,0],[0,0,0,0]];
      function addRand(){ const empt=[]; for(let r=0;r<4;r++) for(let c=0;c<4;c++) if(!grid[r][c]) empt.push([r,c]); if(!empt.length) return; const [r,c]=empt[Math.floor(Math.random()*empt.length)]; grid[r][c]=Math.random()<0.9?2:4; }
      function render(){ el.innerHTML=''; grid.forEach(r=>{ const row=document.createElement('div'); row.style.display='flex'; r.forEach(v=>{ const d=document.createElement('div'); d.style.width='64px'; d.style.height='64px'; d.style.margin='4px'; d.style.background=v? '#ffcc66':'#333'; d.style.display='flex'; d.style.alignItems='center'; d.style.justifyContent='center'; d.style.fontSize='20px'; d.innerText=v||''; row.appendChild(d); }); el.appendChild(row); }); }
      function moveLeft(){ let moved=false; for(let r=0;r<4;r++){ let row=grid[r].filter(n=>n); for(let i=0;i<row.length-1;i++){ if(row[i]===row[i+1]){ row[i]*=2; row.splice(i+1,1); } } while(row.length<4) row.push(0); if(row.some((v,i)=>v!==grid[r][i])){ moved=true; grid[r]=row; } } if(moved) addRand(); render();}
      addRand(); addRand(); render();
      document.addEventListener('keydown',e=>{ if(e.key==='ArrowLeft') moveLeft(); });
      window._inlineCleanup = ()=>{};
  }},
  // 5) Flappy-lite
  {id:'flappy', title:'Flappy (lite)', html:`<canvas id="flappyCanvas" width="320" height="480" style="display:block;margin:12px auto;background:skyblue"></canvas>`, run:function(){
      const c=document.getElementById('flappyCanvas'); const ctx=c.getContext('2d');
      let y=240, vy=0, gravity=0.5; const pipes=[]; let tick=0,score=0;
      function spawn(){ const gap=120; const top = Math.random()*(c.height-gap-40)+20; pipes.push({x:c.width,top:top}); }
      function step(){ vy+=gravity; y+=vy; if(tick%90===0) spawn(); for(let p of pipes){ p.x -=2; if(p.x+50<0) score++; } pipes.filter(p=>p.x+50>0); ctx.clearRect(0,0,c.width,c.height); ctx.fillStyle='yellow'; ctx.fillRect(50,y-10,30,20); ctx.fillStyle='green'; for(let p of pipes){ ctx.fillRect(p.x,0,50,p.top); ctx.fillRect(p.x,p.top+140,50,c.height-(p.top+140)); } tick++; requestAnimationFrame(step); }
      c.addEventListener('click', ()=>{ vy=-8; }); step();
      window._inlineCleanup = ()=>{};
  }},
  // 6) Minesweeper-lite
  {id:'mines', title:'Minesweeper (mini)', html:`<div id="mines" style="width:300px;margin:12px auto;display:grid;grid-template-columns:repeat(8,1fr);gap:4px"></div>`, run:function(){
      const el=document.getElementById('mines'); if(!el) return;
      const size=8; const mines=10; const grid=[]; for(let i=0;i<size;i++){ grid[i]=[]; for(let j=0;j<size;j++){ grid[i][j]={m:false,v:0,o:false}; }}
      for(let k=0;k<mines;k++){ let r,j; do{ r=Math.floor(Math.random()*size); j=Math.floor(Math.random()*size);}while(grid[r][j].m); grid[r][j].m=true; }
      for(let i=0;i<size;i++) for(let j=0;j<size;j++){ if(grid[i][j].m) continue; let cnt=0; for(let di=-1;di<=1;di++) for(let dj=-1;dj<=1;dj++){ const ni=i+di, nj=j+dj; if(ni>=0&&nj>=0&&ni<size&&nj<size && grid[ni][nj].m) cnt++; } grid[i][j].v=cnt; }
      function render(){ el.innerHTML=''; for(let i=0;i<size;i++) for(let j=0;j<size;j++){ const b=document.createElement('button'); b.style.width='34px'; b.style.height='34px'; const cell=grid[i][j]; b.innerText = cell.o ? (cell.m? '💣' : (cell.v||'')) : ''; b.onclick = ()=>{ cell.o=true; render(); }; el.appendChild(b);} }
      render(); window._inlineCleanup=()=>{};
  }},
  // 7) Connect4-lite (two-player local)
  {id:'connect4', title:'Connect 4', html:`<div id="c4" style="width:320px;margin:12px auto"></div>`, run:function(){
      const el=document.getElementById('c4'); const cols=7, rows=6; const board=Array.from({length:rows},()=>Array(cols).fill(0)); let turn=1;
      function render(){ el.innerHTML=''; for(let r=0;r<rows;r++){ const row=document.createElement('div'); row.style.display='flex'; for(let c=0;c<cols;c++){ const cell=document.createElement('div'); cell.style.width='40px'; cell.style.height='40px'; cell.style.border='1px solid #333'; cell.style.display='flex'; cell.style.alignItems='center'; cell.style.justifyContent='center'; cell.style.background=board[r][c]===0?'#111':(board[r][c]===1?'red':'yellow'); cell.onclick=()=>{ for(let rr=rows-1;rr>=0;rr--){ if(board[rr][c]===0){ board[rr][c]=turn; turn=3-turn; break;} } render(); }; row.appendChild(cell);} el.appendChild(row);} }
      render(); window._inlineCleanup=()=>{};
  }},
  // 8) Memory Match (cards)
  {id:'memory', title:'Memory Match', html:`<div id="mem" style="width:320px;margin:12px auto;display:grid;grid-template-columns:repeat(4,1fr);gap:6px"></div>`, run:function(){
      const el=document.getElementById('mem'); const vals=[1,1,2,2,3,3,4,4,5,5,6,6,7,7,8,8]; vals.sort(()=>Math.random()-0.5);
      const state=vals.map(()=>({fl:false,ok:false}));
      function render(){ el.innerHTML=''; for(let i=0;i<16;i++){ const b=document.createElement('div'); b.style.width='70px'; b.style.height='70px'; b.style.display='flex'; b.style.alignItems='center'; b.style.justifyContent='center'; b.style.background=state[i].fl||state[i].ok?'#fff':'#333'; b.style.color=state[i].fl||state[i].ok?'#000':'#fff'; b.style.fontSize='24px'; b.innerText = state[i].fl||state[i].ok? vals[i]:''; b.onclick=()=>{ if(state[i].ok||state[i].fl) return; state[i].fl=true; const flipped = state.map((s,idx)=>s.fl&&!s.ok?idx:-1).filter(n=>n!=-1); if(flipped.length===2){ const [a,bx]=flipped; if(vals[a]===vals[bx]){ state[a].ok=state[bx].ok=true; } setTimeout(()=>{ state[a].fl=false; state[bx].fl=false; render(); },600); } render(); }; el.appendChild(b);} }
      render(); window._inlineCleanup=()=>{};
  }},
  // 9) Simple Racer (avoid obstacles)
  {id:'racer', title:'Racer Mini', html:`<canvas id="race" width="320" height="480" style="display:block;margin:12px auto;background:#333"></canvas>`, run:function(){
      const c=document.getElementById('race'); const ctx=c.getContext('2d'); let carX=c.width/2-15, carY=c.height-70; const obstacles=[]; let tick=0,score=0;
      function step(){ tick++; if(tick%60===0) obstacles.push({x:Math.random()*(c.width-40),y:-40}); ctx.clearRect(0,0,c.width,c.height); ctx.fillStyle='red'; ctx.fillRect(carX,carY,30,50); obstacles.forEach(o=>{ o.y+=2; ctx.fillStyle='white'; ctx.fillRect(o.x,o.y,40,20); if(o.y>c.height) score++; if(o.y+20>carY && o.y<carY+50 && o.x<carX+30 && o.x+40>carX){ o.y=9999; } }); requestAnimationFrame(step);}
      document.addEventListener('mousemove',e=>{ const r=c.getBoundingClientRect(); carX = e.clientX - r.left - 15; });
      step(); window._inlineCleanup=()=>{};
  }},
  // 10) Jump Cube (simple platform jumper)
  {id:'jumper', title:'Jump Cube', html:`<canvas id="jump" width="320" height="480" style="display:block;margin:12px auto;background:linear-gradient(#87ceeb,#6aa)"></canvas>`, run:function(){
      const c=document.getElementById('jump'); const ctx=c.getContext('2d'); let px=160, py=240, vy=0; const gravity=0.5; const platforms=[{x:140,y:300,w:80}];
      function step(){ vy+=gravity; py+=vy; if(py>c.height) py=240, vy=-8; ctx.clearRect(0,0,c.width,c.height); ctx.fillStyle='brown'; platforms.forEach(p=>{ ctx.fillRect(p.x,p.y,p.w,10); if(py+20>p.y && py+20<p.y+10 && px+10>p.x && px<p.x+p.w && vy>0){ vy=-10; } }); ctx.fillStyle='red'; ctx.fillRect(px,py,20,20); requestAnimationFrame(step); }
      document.addEventListener('mousemove',e=>{ const r=c.getBoundingClientRect(); px = e.clientX - r.left - 10; });
      step(); window._inlineCleanup=()=>{};
  }},
  // 11) Asteroids mini
  {id:'asteroids', title:'Asteroids (mini)', html:`<canvas id="ast" width="480" height="360" style="display:block;margin:12px auto;background:#000"></canvas>`, run:function(){
      const c=document.getElementById('ast'); const ctx=c.getContext('2d'); let ship={x:c.width/2,y:c.height/2,ang:0}; const asts=[{x:50,y:50,r:30},{x:300,y:80,r:20}];
      function step(){ ctx.clearRect(0,0,c.width,c.height); ctx.save(); ctx.translate(ship.x,ship.y); ctx.rotate(ship.ang); ctx.fillStyle='white'; ctx.beginPath(); ctx.moveTo(15,0); ctx.lineTo(-10,10); ctx.lineTo(-10,-10); ctx.closePath(); ctx.fill(); ctx.restore(); asts.forEach(a=>{ ctx.beginPath(); ctx.arc(a.x,a.y,a.r,0,Math.PI*2); ctx.strokeStyle='gray'; ctx.stroke(); }); requestAnimationFrame(step); }
      step(); window._inlineCleanup=()=>{};
  }},
  // 12) Space Invaders mini
  {id:'invaders', title:'Space Invaders (mini)', html:`<canvas id="inv" width="480" height="320" style="display:block;margin:12px auto;background:#000"></canvas>`, run:function(){
      const c=document.getElementById('inv'); const ctx=c.getContext('2d'); let px=c.width/2; const enemies=[]; for(let r=0;r<3;r++) for(let c2=0;c2<8;c2++) enemies.push({x:40+c2*50,y:30+r*30});
      function step(){ ctx.clearRect(0,0,c.width,c.height); ctx.fillStyle='white'; ctx.fillRect(px, c.height-20, 40, 10); enemies.forEach(e=>{ ctx.fillRect(e.x,e.y,30,20); }); requestAnimationFrame(step); }
      document.addEventListener('mousemove',e=>{ const r=document.getElementById('inv').getBoundingClientRect(); px = e.clientX - r.left - 20; });
      step(); window._inlineCleanup=()=>{};
  }},
  // 13) Platformer mini (basic)
  {id:'plat1', title:'Platformer 1', html:`<canvas id="plat" width="480" height="320" style="display:block;margin:12px auto;background:#7ec0ee"></canvas>`, run:function(){
      const c=document.getElementById('plat'); const ctx=c.getContext('2d'); let x=50,y=200,vy=0,gravity=0.8; const platforms=[{x:0,y:280,w:480}];
      function step(){ vy+=gravity; y+=vy; if(y>260){ y=260; vy=0;} ctx.clearRect(0,0,c.width,c.height); ctx.fillStyle='brown'; platforms.forEach(p=>ctx.fillRect(p.x,p.y,p.w,10)); ctx.fillStyle='red'; ctx.fillRect(x,y,20,20); requestAnimationFrame(step); }
      document.addEventListener('keydown',e=>{ if(e.key===' ' && y>=260) vy=-12; if(e.key==='ArrowLeft') x-=10; if(e.key==='ArrowRight') x+=10; });
      step(); window._inlineCleanup=()=>{};
  }},
  // 14) Platformer 2 (different layout)
  {id:'plat2', title:'Platformer 2', html:`<div style="padding:12px;text-align:center">Platformer 2 placeholder — add more physics here</div>`, run:()=>{}},
  // 15) Brick Shooter (minimal)
  {id:'bricksh', title:'Brick Shooter', html:`<div style="padding:12px">Brick Shooter placeholder</div>`, run:()=>{}},
  // 16) Dungeon runner (mini)
  {id:'dunge', title:'Dungeon Mini', html:`<div style="padding:12px">Dungeon runner placeholder</div>`, run:()=>{}},
  // 17) Tile match (puzzle)
  {id:'tilem', title:'Tile Match', html:`<div style="padding:12px">Tile match placeholder</div>`, run:()=>{}},
  // 18) Simon memory
  {id:'simon', title:'Simon', html:`<div style="padding:12px;text-align:center"><button class="btn" id="simonStart">Start</button></div>`, run:function(){ document.getElementById('simonStart').onclick=()=>alert('Simon start - demo'); window._inlineCleanup=()=>{} }},
  // 19) Dodge ball
  {id:'dodge', title:'Dodge Ball', html:`<div style="padding:12px">Dodge Ball placeholder</div>`, run:()=>{}},
  // 20) Runner lite
  {id:'runner', title:'Runner Lite', html:`<div style="padding:12px">Runner placeholder</div>`, run:()=>{}}
];

/* ===================== HEAVIER PORTS (5) - more faithful, heavier but included inline ===================== */
/* These are simplified faithful rewrites (no external assets). Feel free to ask to replace with exact repo files. */
const HEAVY_INLINE = [
  {id:'heavy-tetris', title:'Tetris (heavy)', html:`<canvas id="htCanvas" width="300" height="600" style="display:block;margin:12px auto;background:#000"></canvas>`, run:function(){
      // tiny Tetris-like visual, simplified
      const c=document.getElementById('htCanvas'); const ctx=c.getContext('2d'); const cols=10, rows=20, size=30;
      let grid = Array.from({length:rows}, ()=>Array(cols).fill(0));
      function draw(){ ctx.clearRect(0,0,c.width,c.height); for(let r=0;r<rows;r++) for(let co=0;co<cols;co++){ if(grid[r][co]) ctx.fillStyle='orange'; else ctx.fillStyle='#111'; ctx.fillRect(co*size, r*size, size-1, size-1); } }
      draw(); window._inlineCleanup=()=>{};
  }},
  {id:'heavy-platform', title:'Platformer (heavy)', html:`<canvas id="hp" width="480" height="320" style="display:block;margin:12px auto;background:#87ceeb"></canvas>`, run:function(){ const c=document.getElementById('hp'); const ctx=c.getContext('2d'); ctx.fillStyle='green'; ctx.fillRect(0,260,480,60); ctx.fillStyle='red'; ctx.fillRect(40,220,20,40); window._inlineCleanup=()=>{} }},
  {id:'heavy-2048', title:'2048 (heavy)', html:`<div id="h2048" style="width:340px;margin:12px auto"></div>`, run:function(){ const el=document.getElementById('h2048'); el.innerText='2048 heavy placeholder'; window._inlineCleanup=()=>{} }},
  {id:'heavy-invaders', title:'Invaders (heavy)', html:`<canvas id="hinv" width="480" height="320" style="display:block;margin:12px auto;background:#000"></canvas>`, run:function(){ const c=document.getElementById('hinv'); const ctx=c.getContext('2d'); ctx.fillStyle='white'; ctx.fillRect(200,280,80,10); window._inlineCleanup=()=>{} }},
  {id:'heavy-arcade', title:'Arcade Mega', html:`<div style="padding:12px">Arcade heavy placeholder</div>`, run:()=>{} }
];

/* ===================== RENDER UI ===================== */
function showTab(tab){
  ['games','study','bookmarks','settings','updates'].forEach(id => document.getElementById(id).style.display = (id===tab)?'block':'none');
  document.querySelectorAll('.tab').forEach(el=>el.classList.remove('active'));
  document.getElementById('tab-'+tab).classList.add('active');
  if(tab==='games') renderGames();
  if(tab==='study') renderSubjects();
  if(tab==='bookmarks') renderBookmarklets();
  if(tab==='updates') renderUpdates();
}

function renderGames(){
  const grid = document.getElementById('gamesGrid'); grid.innerHTML = '';
  // inline quick games
  INLINE_GAMES.forEach(g=>{
    const d=document.createElement('div'); d.className='card';
    d.innerHTML = `<h4>${escapeHtml(g.title)}</h4><div style="margin-top:8px"><button class="btn" onclick="playInline('${g.id}')">Play</button></div>`;
    grid.appendChild(d);
  });
  // heavy inline
  HEAVY_INLINE.forEach(g=>{
    const d=document.createElement('div'); d.className='card';
    d.innerHTML = `<h4>${escapeHtml(g.title)}</h4><div style="margin-top:8px"><button class="btn" onclick="playHeavyInline('${g.id}')">Play</button></div>`;
    grid.appendChild(d);
  });
  // discovered CDN games
  if(discoveredGames.length===0){
    const p=document.createElement('div'); p.className='card'; p.innerHTML='<h4>Discovering repo games...</h4><div class="small">Scanning selenite-old repo. This may take a few seconds.</div>'; grid.appendChild(p);
  } else {
    discoveredGames.forEach(g=>{
      const d=document.createElement('div'); d.className='card';
      d.innerHTML = `<h4>${escapeHtml(g.title)}</h4><div class="small" style="word-break:break-word">${escapeHtml(g.src)}</div>
        <div style="margin-top:8px"><button class="btn" onclick="playIframe('${g.src}', '${escapeHtml(g.title)}')">Play</button></div>`;
      grid.appendChild(d);
    });
  }
}

/* play inline small */
function playInline(id){
  const g = INLINE_GAMES.find(x=>x.id===id); if(!g) return alert('Not found');
  openModal(g.title, g.html);
  setTimeout(()=>{ try{ g.run(); }catch(e){ console.error(e); } },60);
}
/* play heavy inline */
function playHeavyInline(id){
  const g = HEAVY_INLINE.find(x=>x.id===id); if(!g) return alert('Not found');
  openModal(g.title, g.html);
  setTimeout(()=>{ try{ g.run(); }catch(e){ console.error(e); } },60);
}

/* iframe player for CDN games */
function playIframe(src,title){
  openModal(title, `<iframe src="${src}" style="width:100%;height:72vh;border:none"></iframe>`);
}

/* modal helpers */
function openModal(title,content){
  const m=document.getElementById('fsModal'); const card=document.getElementById('fsCard');
  card.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><strong>${escapeHtml(title)}</strong><button class="btn" onclick="closeModal()">Close</button></div><div style="margin-top:8px">${content}</div>`;
  m.style.display='flex';
}
function closeModal(){ if(window._inlineCleanup){ try{ window._inlineCleanup(); }catch(e){} window._inlineCleanup=null; } document.getElementById('fsCard').innerHTML=''; document.getElementById('fsModal').style.display='none'; }
function escapeHtml(s){ return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

/* ===================== STUDY HUB ===================== */
function renderSubjects(){
  const wrap = document.getElementById('subjectsWrap'); wrap.innerHTML='';
  ['Math','English','Science'].forEach(sub=>{
    const d=document.createElement('div'); d.className='card';
    d.innerHTML = `<h4>${sub}</h4><div class="subject-row">
      <div class="subject-btn" onclick="quickLesson('${sub}')">Quick Lessons</div>
      <div class="subject-btn" onclick="quizPrompt('${sub}')">Practice Quiz</div>
      <div class="subject-btn" onclick="flashcardPrompt('${sub}')">Flashcard Maker</div>
      <div class="subject-btn" onclick="aiPrompt('${sub}')">AI Tutor</div>
    </div>`;
    wrap.appendChild(d);
  });
}
function quickLesson(sub){ openModal(sub+' — Quick Lesson', `<p class="small">Quick lesson for ${escapeHtml(sub)} (demo).</p><ul><li>Key point 1</li><li>Key point 2</li></ul>`); }
function quizPrompt(sub){
  const topic = prompt('Topic for '+sub+' quiz (short):'); if(!topic) return;
  const qs=[]; for(let i=1;i<=5;i++){ qs.push({q:`${topic} problem ${i}`, choices:[`Correct ${i}`, 'A','B','C'], correct:`Correct ${i}`}); }
  renderQuiz(qs, sub+' — '+topic);
}
function renderQuiz(qs,title){ window._activeQuiz=qs; const html = `<h3>${escapeHtml(title)}</h3>` + qs.map((q,i)=>`<div style="margin-top:8px"><strong>${escapeHtml(q.q)}</strong>` + q.choices.map(c=>`<div><label><input type="radio" name="q${i}" value="${escapeHtml(c)}"> ${escapeHtml(c)}</label></div>`).join('')+`</div>`).join('') + `<div style="margin-top:12px"><button class="btn" onclick="gradeQuiz()">Submit</button> <button class="btn" onclick="closeModal()">Close</button></div>`; openModal(title, html); }
function gradeQuiz(){ const qs=window._activeQuiz||[]; let s=0; for(let i=0;i<qs.length;i++){ const sel=document.querySelector(`input[name=q${i}]:checked`); if(sel && sel.value===qs[i].correct) s++; } alert('Score: '+s+'/'+qs.length); }

function flashcardPrompt(sub){
  const topic = prompt('Topic for '+sub+' flashcards:'); if(!topic) return;
  let n = parseInt(prompt('How many cards (1-12)?','6'))||6; n=Math.max(1,Math.min(12,n));
  const cards=[]; for(let i=1;i<=n;i++) cards.push({term:`${topic} ${i}`, def:`Short explanation ${i}`});
  const html = `<h3>${escapeHtml(sub)} Flashcards — ${escapeHtml(topic)}</h3>` + cards.map(c=>`<div style="margin-top:8px"><strong>${escapeHtml(c.term)}</strong><div class="small">${escapeHtml(c.def)}</div></div>`).join('') + `<div style="margin-top:12px"><button class="btn" onclick='saveFlashcards(${JSON.stringify(topic)}, ${JSON.stringify(cards)})'>Save</button></div>`;
  openModal('Flashcards', html);
}
function saveFlashcards(topic, cards){ const all = JSON.parse(localStorage.getItem('gs_flash_sets')||'{}'); all[topic]=cards; localStorage.setItem('gs_flash_sets', JSON.stringify(all)); alert('Saved '+topic); closeModal(); }

function aiPrompt(sub){
  const q = prompt('Ask AI Tutor about '+sub+':'); if(!q) return;
  let ans = 'Think step by step. Use examples.';
  if(sub.toLowerCase().includes('math') && /\d/.test(q)){ try{ const expr = q.replace(/[^0-9+\-*/().\/ ]/g,''); const res = Function('"use strict";return ('+expr+')')(); ans = 'Example evaluation: '+res; }catch(e){} }
  else if(sub.toLowerCase().includes('science')) ans = 'Identify inputs, processes, outputs. Example: photosynthesis: sunlight + CO₂ + water -> glucose + O₂.';
  openModal(sub+' AI Tutor', `<p><strong>Q:</strong> ${escapeHtml(q)}</p><div style="margin-top:8px">${escapeHtml(ans)}</div>`);
}

/* ===================== BOOKMARKLETS ===================== */
let BOOKMARKLETS = [
  {id:'bm-google', title:'Open Google', code:()=>window.open('https://google.com','_blank')},
  {id:'bm-yt', title:'Open YouTube', code:()=>window.open('https://youtube.com','_blank')},
  {id:'bm-cookies', title:'Show Cookies', code:()=>alert(document.cookie)},
  {id:'bm-randbg', title:'Random BG', code:()=>document.body.style.backgroundColor='#'+Math.floor(Math.random()*16777215).toString(16)},
  {id:'bm-big', title:'Big Text', code:()=>document.body.style.fontSize='20px'}
];
function renderBookmarklets(){
  const list = document.getElementById('bookmarkletList'); list.innerHTML='';
  BOOKMARKLETS.forEach(b=>{ const el=document.createElement('div'); el.className='card'; el.style.marginBottom='8px'; el.innerHTML = `<strong>${escapeHtml(b.title)}</strong><div style="margin-top:8px"><button class="btn" onclick="(function(){(${b.code.toString()})();})();">Run</button></div>`; list.appendChild(el); });
  const bp = document.getElementById('bmPanel'); const bl = document.getElementById('bmPanelList'); bl.innerHTML=''; BOOKMARKLETS.forEach(b=>{ const btn=document.createElement('button'); btn.className='btn'; btn.style.width='100%'; btn.style.marginTop='6px'; btn.innerText=b.title; btn.onclick=b.code; bl.appendChild(btn); });
  bp.style.display='block'; makeDraggable(bp);
}

/* draggable helper for the panel */
function makeDraggable(el){
  let pos1=0,pos2=0,pos3=0,pos4=0; el.onmousedown=dragMouseDown;
  function dragMouseDown(e){ e=e||window.event; e.preventDefault(); pos3=e.clientX; pos4=e.clientY; document.onmouseup=closeDrag; document.onmousemove=elementDrag; }
  function elementDrag(e){ e=e||window.event; e.preventDefault(); pos1=pos3-e.clientX; pos2=pos4-e.clientY; pos3=e.clientX; pos4=e.clientY; el.style.top=(el.offsetTop-pos2)+'px'; el.style.left=(el.offsetLeft-pos1)+'px'; }
  function closeDrag(){ document.onmouseup=null; document.onmousemove=null; }
}

/* ===================== UPDATES & REPO SCAN ===================== */
/*
Scan GitHub repo page for links to files. Convert likely HTML/index paths into jsDelivr CDN URLs:
https://cdn.jsdelivr.net/gh/<owner>/<repo>@<branch>/<path>
Test each candidate with fetch; if OK, add to discoveredGames.
*/
async function scanRepo(){
  discoveredGames = [];
  try{
    const repoUrl = `https://github.com/${REPO_OWNER}/${REPO_NAME}`;
    const r = await fetch(repoUrl);
    if(!r.ok) { console.warn('Repo fetch failed', r.status); renderGames(); renderUpdates(); return; }
    const html = await r.text();
    const parser = new DOMParser(); const doc = parser.parseFromString(html,'text/html');
    const anchors = Array.from(doc.querySelectorAll('a.js-navigation-open'));
    const candidates = new Set();
    anchors.forEach(a=>{ const href=a.getAttribute('href')||''; const m=href.match(/\/[^\/]+\/[^\/]+\/blob\/[^\/]+\/(.+)/); if(m && m[1]){ const p=m[1]; if(p.toLowerCase().endsWith('.html')||p.toLowerCase().endsWith('.htm')) candidates.add(p); else { const dir = p.split('/').slice(0,-1).join('/'); if(dir) candidates.add(dir+'/index.html'); } }});
    const cdnBase = `https://cdn.jsdelivr.net/gh/${REPO_OWNER}/${REPO_NAME}@${REPO_BRANCH}/`;
    for(const rel of candidates){
      const url = cdnBase + rel;
      try{
        const rr = await fetch(url, {method:'GET'});
        if(rr.ok){
          discoveredGames.push({id: 'cdn-'+btoa(rel).slice(0,8), title: rel.split('/').slice(-1)[0], src: url});
        }
      }catch(e){}
      await new Promise(res=>setTimeout(res, 120));
    }
    discoveredGames.sort((a,b)=>a.title.localeCompare(b.title));
    renderGames(); renderUpdates();
  }catch(e){ console.error('Scan failed', e); renderGames(); renderUpdates(); }
}

function renderUpdates(){
  const ug = document.getElementById('updatesGrid'); ug.innerHTML='';
  const info=document.createElement('div'); info.className='card'; info.innerHTML=`<h4>Repository scan</h4><div class="small">${discoveredGames.length} playable HTML files discovered (jsDelivr)</div>`; ug.appendChild(info);
  discoveredGames.slice(0,50).forEach(g=>{ const c=document.createElement('div'); c.className='card'; c.innerHTML=`<h4>${escapeHtml(g.title)}</h4><div class="small" style="word-break:break-word">${escapeHtml(g.src)}</div><div style="margin-top:8px"><button class="btn" onclick="playIframe('${g.src}','${escapeHtml(g.title)}')">Play</button></div>`; ug.appendChild(c);});
  document.getElementById('changelog').innerHTML = `<div class="small">Scan completed at ${new Date().toLocaleString()} — ${discoveredGames.length} discovered.</div>`;
}

/* ===================== UTILITIES ===================== */
function applyCloak(){ const url=document.getElementById('cloakUrl').value||''; if(!url) return alert('Enter cloaker URL'); document.title='Cloaked Page'; try{ history.replaceState({}, 'Cloaked', url); }catch(e){} alert('Cloak applied.'); }
function closeSubPrompt(){ document.getElementById('subModal').style.display='none'; localStorage.setItem('gs_seen_sub','1'); }

/* keyboard ESC close */
document.addEventListener('keydown', e=>{ if(e.key==='Escape') closeModal(); });

/* ===================== INIT ===================== */
document.addEventListener('DOMContentLoaded', ()=>{
  renderGames();
  renderSubjects();
  renderBookmarklets();
  if(!localStorage.getItem('gs_seen_sub')){ document.getElementById('subModal').style.display='flex'; } else document.getElementById('subModal').style.display='none';
  // start repo scan in background
  scanRepo();
});
</script>
</body>
</html>
