<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>7th Grade Ultimate Hub — Final</title>
<style>
:root{
  --bg:#4c1d95;
  --card:#1e1b4b;
  --muted:#cfc4ff;
  --blue:#3b82f6;
  --blue-dark:#1e40af;
  --accent:#312e81;
}
*{box-sizing:border-box}
body{
  margin:0;
  font-family:Inter,system-ui,Segoe UI,Roboto,"Helvetica Neue",Arial;
  background:var(--bg);
  color:#fff;
  -webkit-font-smoothing:antialiased;
}
.app{max-width:1100px;margin:18px auto;padding:18px;}
.header{display:flex;justify-content:space-between;align-items:center;gap:12px;margin-bottom:12px;}
.title h1{margin:0;font-size:20px} .title p{margin:0;color:var(--muted);font-size:13px}
.menu{display:flex;gap:8px;flex-wrap:wrap}
.btn{background:var(--blue);color:white;border:0;padding:8px 12px;border-radius:8px;cursor:pointer;font-weight:600}
.btn.ghost{background:transparent;border:1px solid rgba(255,255,255,0.08)}
.container{display:grid;grid-template-columns:1fr 360px;gap:16px;align-items:start}
.card{background:var(--card);padding:14px;border-radius:12px;box-shadow:0 6px 18px rgba(0,0,0,0.25)}
.input, textarea, select{width:100%;padding:8px;border-radius:8px;border:0;background:var(--accent);color:white;margin-top:8px;font-size:14px}
.small{font-size:13px;color:var(--muted)}
.flash{display:flex;flex-direction:column;gap:8px;align-items:center;text-align:center}
.card-face{min-height:84px;width:100%;display:flex;align-items:center;justify-content:center;background:linear-gradient(180deg,#24163f,#312e81);padding:12px;border-radius:10px;font-size:18px}
.saved-list{max-height:48vh;overflow:auto;padding:6px;border-radius:8px;background:linear-gradient(180deg,#21173b,#24163f)}
.kv{display:flex;justify-content:space-between;gap:8px;align-items:center}
@media(max-width:980px){.container{grid-template-columns:1fr}.sidebar{order:2}}
.notice{background:rgba(0,0,0,0.25);padding:8px;border-radius:8px;color:var(--muted)}
.link{color:var(--blue);cursor:pointer;text-decoration:underline}
.code{background:#0f172a;padding:8px;border-radius:6px;overflow:auto}
</style>
</head>
<body>
<div class="app">
  <div class="header">
    <div class="title">
      <h1>7th Grade Ultimate Hub</h1>
      <p>Study • Focus • Achieve — flashcards, quizzes, AI tutor</p>
    </div>

    <div class="menu">
      <button class="btn" onclick="showPanel('flash')">Flashcards</button>
      <button class="btn" onclick="showPanel('quiz')">Quiz Maker</button>
      <button class="btn" onclick="showPanel('study')">Quiz Study</button>
      <button class="btn" onclick="showPanel('tutor')">AI Tutor</button>
      <button class="btn" onclick="showPanel('settings')">Settings</button>
    </div>
  </div>

  <div class="container">
    <main>
      <div id="dec1" class="card notice" style="display:none;margin-bottom:12px">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div><strong>Notice:</strong> Effective Dec 1 — subscription required for some features. Click "I Subscribed" once subscribed.</div>
          <div style="display:flex;gap:8px">
            <button class="btn" onclick="localStorage.setItem('sg2_subscribed','1'); updateDecNotice()">I Subscribed</button>
            <button class="btn ghost" onclick="localStorage.setItem('sg2_notice_dismiss','1'); updateDecNotice()">Dismiss</button>
          </div>
        </div>
      </div>

      <section id="panel-flash" class="card panel">
        <h2 style="margin:0 0 8px 0">Flashcards — interactive</h2>
        <div class="kv small">
          <div>AI generate or paste your own (one per line: <code>front - back</code>)</div>
          <div>
            <button class="btn" onclick="aiGenerateFlash()">AI Generate</button>
            <button class="btn" onclick="parseFlashInput()">Create</button>
          </div>
        </div>

        <textarea id="flash-input" class="input" rows="4" placeholder="Term - Definition"></textarea>

        <div style="display:flex;gap:8px;margin-top:8px">
          <input id="flash-topic" class="input" placeholder="If AI generate, enter topic (e.g. Photosynthesis)" />
          <select id="flash-count" class="input" style="width:120px">
            <option value="5">5 cards</option>
            <option value="8">8 cards</option>
            <option value="10">10 cards</option>
          </select>
        </div>

        <div style="display:flex;gap:8px;margin-top:10px">
          <button class="btn" onclick="startPractice()">Practice</button>
          <button class="btn" onclick="saveCurrent('flash')">Save Set</button>
          <button class="btn ghost" onclick="clearFlashInput()">Clear Input</button>
        </div>

        <div id="flash-list" style="margin-top:12px"></div>

        <div id="practice-area" style="display:none;margin-top:14px">
          <div class="flash">
            <div id="card-index" class="small">Card 1 / 1</div>
            <div id="card-face" class="card-face">...</div>
            <div style="display:flex;gap:8px;width:100%;justify-content:center;margin-top:8px">
              <button class="btn" onclick="flipCard()">Flip</button>
              <button class="btn" onclick="prevCard()">Prev</button>
              <button class="btn" onclick="nextCard()">Next</button>
              <button class="btn ghost" onclick="markKnown()">Mark Known</button>
            </div>
          </div>
        </div>
      </section>

      <section id="panel-quiz" class="card panel" style="display:none">
        <h2 style="margin:0 0 8px 0">Quiz Maker — create & play</h2>
        <div class="kv small">
          <div>AI generate multiple-choice quizzes or create manual questions</div>
          <div>
            <button class="btn" onclick="aiGenerateQuiz()">AI Generate</button>
            <button class="btn" onclick="createManualQuiz()">Create Manual</button>
          </div>
        </div>

        <textarea id="quiz-input" class="input" rows="3" placeholder="Topic or manual quiz format"></textarea>

        <div style="display:flex;gap:8px;margin-top:8px">
          <select id="quiz-count" class="input" style="width:120px">
            <option value="3">3 Qs</option>
            <option value="5" selected>5 Qs</option>
            <option value="8">8 Qs</option>
          </select>
          <button class="btn" onclick="saveCurrent('quiz')">Save Quiz</button>
          <button class="btn ghost" onclick="clearQuizInput()">Clear</button>
        </div>

        <div id="quiz-area" style="margin-top:12px"></div>
      </section>

      <section id="panel-study" class="card panel" style="display:none">
        <h2 style="margin:0 0 8px 0">Quiz Study Tool — practice & repeat weak items</h2>
        <div class="kv small">
          <div>Choose saved quiz or flash set to practice</div>
          <div>
            <select id="study-source" class="input" style="width:220px"></select>
            <button class="btn" onclick="startStudy()">Start</button>
          </div>
        </div>
        <div id="study-area" style="margin-top:12px"></div>
      </section>

      <section id="panel-tutor" class="card panel" style="display:none">
        <h2 style="margin:0 0 8px 0">AI Tutor — Ask a question</h2>
        <div style="display:flex;gap:8px;align-items:center">
          <input id="tutor-q" class="input" placeholder="Ask: Explain mitosis in simple terms" />
          <button class="btn" onclick="askTutor()">Ask</button>
        </div>
        <div id="tutor-response" style="margin-top:12px"></div>
        <div class="small" style="margin-top:8px">Tutor uses the selected provider and the Global key (then personal). If no key available, a local demo answer is returned.</div>
      </section>
    </main>

    <aside class="sidebar">
      <div class="card">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <div>
            <div class="small">AI Provider</div>
            <select id="provider-select" class="input">
              <option value="OpenAI">OpenAI</option>
              <option value="Gemini">Gemini (Google Generative)</option>
            </select>
          </div>
        </div>
        <div style="margin-top:8px">
          <div class="small">Global API Key (stored locally)</div>
          <input id="global-key" class="input" placeholder="Paste global API key here (optional)" />
          <div style="display:flex;gap:8px;margin-top:8px">
            <button class="btn" onclick="saveGlobalKey()">Save Global Key</button>
            <button class="btn ghost" onclick="clearGlobalKey()">Clear</button>
          </div>
          <div class="small" style="margin-top:8px;color:var(--muted)">Global key is saved to localStorage. For a truly site-wide key for all visitors host a server-side proxy (example included).</div>
        </div>
      </div>

      <div class="card">
        <div class="small">Personal API Key (fallback)</div>
        <input id="personal-key" class="input" placeholder="Personal API key (optional)" />
        <div style="display:flex;gap:8px;margin-top:8px">
          <button class="btn" onclick="savePersonalKey()">Save</button>
          <button class="btn ghost" onclick="clearPersonalKey()">Clear</button>
        </div>
      </div>

      <div class="card">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <strong>Saved Sets</strong>
          <div><button class="btn ghost" onclick="exportSaved()">Export</button></div>
        </div>
        <div id="saved-list" class="saved-list" style="margin-top:8px"></div>
        <div style="display:flex;gap:8px;margin-top:8px">
          <button class="btn" onclick="importSaved()">Import</button>
          <button class="btn ghost" onclick="clearSaved()">Clear All</button>
        </div>
      </div>

      <div class="card">
        <div class="small">Mini Browser</div>
        <input id="mini-url" class="input" placeholder="https://example.com" />
        <div style="display:flex;gap:8px;margin-top:8px">
          <button class="btn" onclick="openMini()">Open</button>
          <button class="btn ghost" onclick="document.getElementById('mini-url').value=''">Clear</button>
        </div>
        <iframe id="mini-frame" style="width:100%;height:220px;margin-top:8px;border-radius:8px;border:0;background:white"></iframe>
      </div>
    </aside>
  </div>
</div>

<script>
const STORE_PREFIX = 'sg2_';
const STORE_SAVED = STORE_PREFIX + 'saved';
const STORE_GLOBAL = STORE_PREFIX + 'global_key';
const STORE_PERSONAL = STORE_PREFIX + 'personal_key';
const STORE_PROVIDER = STORE_PREFIX + 'provider';
function setLS(k,v){ localStorage.setItem(k, JSON.stringify(v)); }
function getLS(k, fallback=null){ const v = localStorage.getItem(k); if(!v) return fallback; try{ return JSON.parse(v);}catch(e){return fallback;} }
function escapeHtml(s){ if(!s) return ''; return s.replace(/[&<>"']/g,(m)=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m]));}

let saved = getLS(STORE_SAVED, []);
let currentSet = { id:null, title:'Untitled', cards:[] };
let currentQuiz = null;
let practiceIndex = 0;
let showingBack = false;
let studyQueue = [], studyResults = [];

document.addEventListener('DOMContentLoaded', ()=>{
  const provider = localStorage.getItem(STORE_PROVIDER) || 'OpenAI';
  document.getElementById('provider-select').value = provider;
  document.getElementById('global-key').value = localStorage.getItem(STORE_GLOBAL) || '';
  document.getElementById('personal-key').value = localStorage.getItem(STORE_PERSONAL) || '';
  renderSavedList();
  updateDecNotice();
  populateStudySources();
  showPanel('flash');
});

function updateDecNotice(){
  const dismissed = localStorage.getItem('sg2_notice_dismiss');
  const subscribed = localStorage.getItem('sg2_subscribed');
  const now = new Date();
  const dec1 = new Date(now.getFullYear(), 11, 1);
  const el = document.getElementById('dec1');
  if(now >= dec1 && !dismissed && !subscribed){ el.style.display = 'block'; } else { el.style.display = 'none'; }
}
function showPanel(name){ document.querySelectorAll('.panel').forEach(p=>p.style.display='none'); const el = document.getElementById('panel-' + name); if(el) el.style.display = 'block'; }

function saveGlobalKey(){ const v=(document.getElementById('global-key').value||'').trim(); if(!v){ alert('Enter a key'); return; } localStorage.setItem(STORE_GLOBAL, v); alert('Global key saved (localStorage).'); }
function clearGlobalKey(){ localStorage.removeItem(STORE_GLOBAL); document.getElementById('global-key').value=''; alert('Cleared global key'); }
function savePersonalKey(){ const v=(document.getElementById('personal-key').value||'').trim(); if(!v){ alert('Enter a key'); return; } localStorage.setItem(STORE_PERSONAL, v); alert('Saved personal key'); }
function clearPersonalKey(){ localStorage.removeItem(STORE_PERSONAL); document.getElementById('personal-key').value=''; alert('Cleared personal key'); }
document.getElementById('provider-select').addEventListener('change', (e)=>{ localStorage.setItem(STORE_PROVIDER, e.target.value); });

function getEffectiveKey(){ return localStorage.getItem(STORE_GLOBAL) || localStorage.getItem(STORE_PERSONAL) || ''; }
function getProvider(){ return localStorage.getItem(STORE_PROVIDER) || document.getElementById('provider-select').value || 'OpenAI'; }

async function callAI(prompt, opts={model:'gpt-4o-mini', temp:0.6, max_tokens:500}){
  const key = getEffectiveKey();
  const provider = getProvider();
  if(!key) return null;
  try {
    if(provider === 'OpenAI'){
      const endpoint = 'https://api.openai.com/v1/chat/completions';
      const body = { model: opts.model || 'gpt-4o-mini', messages:[{role:'user', content:prompt}], temperature: opts.temp ?? 0.6, max_tokens: opts.max_tokens ?? 500 };
      const r = await fetch(endpoint, { method:'POST', headers:{ 'Content-Type':'application/json', 'Authorization':'Bearer ' + key }, body: JSON.stringify(body) });
      const data = await r.json();
      if(data.error) throw new Error(data.error.message || JSON.stringify(data.error));
      const txt = data.choices?.[0]?.message?.content ?? null;
      return txt;
    } else if(provider === 'Gemini'){
      const endpoint = 'https://generativelanguage.googleapis.com/v1beta2/models/text-bison-001:generate';
      const body = { prompt:{ text: prompt }, temperature: opts.temp ?? 0.6, maxOutputTokens: opts.max_tokens ?? 500 };
      const url = endpoint + '?key=' + encodeURIComponent(key);
      const r = await fetch(url, { method:'POST', headers:{ 'Content-Type':'application/json' }, body: JSON.stringify(body) });
      const data = await r.json();
      if(data.error) throw new Error(data.error.message || JSON.stringify(data.error));
      const txt = data.candidates?.[0]?.content ?? data.output?.[0]?.content ?? null;
      return txt;
    }
    return null;
  } catch(err){
    console.error('AI request failed', err);
    return null;
  }
}

/* Flashcards */
function parseFlashInput(){ const raw=document.getElementById('flash-input').value.trim(); if(!raw){ alert('Enter flashcards or use AI generate'); return; } const lines=raw.split(/\r?\n/).map(s=>s.trim()).filter(Boolean); const cards=lines.map(l=>{ const parts=l.split('-'); const q=parts.shift().trim(); const a=parts.join('-').trim()||''; return { q,a }; }); currentSet={ id:Date.now(), title:'Manual set', cards }; renderCurrentSet(); }
async function aiGenerateFlash(){ const topic=document.getElementById('flash-topic').value.trim(); const count=Number(document.getElementById('flash-count').value||5); if(!topic){ alert('Enter a topic for AI generation'); return; } const prompt=`Create ${count} concise study flashcards for the topic \"${topic}\". Output each on its own line as: Question - Answer.`; const ai=await callAI(prompt,{model:'gpt-4o-mini',temp:0.3,max_tokens:400}); if(ai===null){ const cards=Array.from({length:count},(_,i)=>({ q:`${topic} - point ${i+1}`, a:`Brief explanation ${i+1}` })); currentSet={ id:Date.now(), title:topic+' (demo)', cards }; renderCurrentSet(); return; } const lines=ai.split(/\r?\n/).map(s=>s.trim()).filter(Boolean); const cards=lines.map(line=>{ const parts=line.split('-'); const q=parts.shift().trim(); const a=parts.join('-').trim()||''; return { q,a }; }); currentSet={ id:Date.now(), title:topic, cards }; renderCurrentSet(); }
function renderCurrentSet(){ const list=document.getElementById('flash-list'); if(!currentSet||!currentSet.cards||currentSet.cards.length===0){ list.innerHTML='<div class=\"small\">No cards yet.</div>'; return; } list.innerHTML=`<div style=\"font-weight:700;margin-bottom:8px\">${escapeHtml(currentSet.title)} · ${currentSet.cards.length} cards</div>` + currentSet.cards.map((c,i)=>`<div style=\"padding:6px 0;border-bottom:1px solid rgba(255,255,255,0.03)\"><strong>${i+1}.</strong> ${escapeHtml(c.q)} — <span class=\"small\">${escapeHtml(c.a)}</span></div>`).join(''); }
function startPractice(){ if(!currentSet||!currentSet.cards||currentSet.cards.length===0){ alert('Generate or create a flashcard set first'); return; } practiceIndex=0; showingBack=false; document.getElementById('practice-area').style.display='block'; renderPracticeCard(); }
function renderPracticeCard(){ document.getElementById('card-index').innerText=`Card ${practiceIndex+1} / ${currentSet.cards.length}`; document.getElementById('card-face').innerText= showingBack? (currentSet.cards[practiceIndex].a||'(no answer)') : (currentSet.cards[practiceIndex].q||'(no front)'); }
function flipCard(){ showingBack=!showingBack; renderPracticeCard(); }
function nextCard(){ practiceIndex=(practiceIndex+1)%currentSet.cards.length; showingBack=false; renderPracticeCard(); }
function prevCard(){ practiceIndex=(practiceIndex-1+currentSet.cards.length)%currentSet.cards.length; showingBack=false; renderPracticeCard(); }
function markKnown(){ currentSet.cards.splice(practiceIndex,1); if(currentSet.cards.length===0){ alert('All known!'); document.getElementById('practice-area').style.display='none'; return; } practiceIndex=practiceIndex%currentSet.cards.length; showingBack=false; renderPracticeCard(); }
function saveCurrent(type){ if(type==='flash'){ if(!currentSet||!currentSet.cards||currentSet.cards.length===0){ alert('No flashcards to save'); return; } const item={id:Date.now(), type:'flash', title:currentSet.title||'Flash set', data:currentSet.cards}; saved.unshift(item); persistSaved(); renderSavedList(); alert('Flashcard set saved.'); } else if(type==='quiz'){ alert('Use Save Quiz in the Quiz Maker to save quizzes.'); } }
function clearFlashInput(){ document.getElementById('flash-input').value=''; document.getElementById('flash-topic').value=''; }

/* Quiz maker */
function clearQuizInput(){ document.getElementById('quiz-input').value=''; document.getElementById('quiz-area').innerHTML=''; currentQuiz=null; }
async function aiGenerateQuiz(){ const topic=document.getElementById('quiz-input').value.trim(); const n=Number(document.getElementById('quiz-count').value||5); if(!topic){ alert('Enter topic for AI quiz generation'); return; } const prompt=`Create ${n} multiple-choice questions (4 choices each) about \"${topic}\". For each question, include the question text followed by choices A-D on separate lines and indicate the correct choice letter in parentheses at the end of the question line.`; const ai=await callAI(prompt,{model:'gpt-4o-mini',temp:0.4,max_tokens:700}); if(ai===null){ const quiz=Array.from({length:n},(_,i)=>({ q:`What is ${topic} (example) #${i+1}?`, choices:['Option A','Option B','Option C','Option D'], a:0 })); loadQuizToUI(quiz); return; } const lines=ai.split(/\\r?\\n/).map(s=>s.trim()).filter(Boolean); const quiz=[]; for(let i=0;i<lines.length;i++){ const match=lines[i].match(/^\\d+[\\).\\s]+(.+?)(\\([A-D]\\))?$/i); if(match){ let qtext=match[1].trim(); let correct=null; const ansMatch=lines[i].match(/\\(([A-D])\\)$/i); if(ansMatch) correct="ABCD".indexOf(ansMatch[1].toUpperCase()); const choices=[]; for(let j=1;j<=4 && i+j<lines.length;j++){ const c=lines[i+j].replace(/^[A-D][\\).\\s-]*/i,'').trim(); choices.push(c); } if(choices.length===4){ quiz.push({ q:qtext, choices, a: correct ?? 0 }); } } } if(quiz.length===0){ for(let i=1;i<=n;i++) quiz.push({ q:`${topic} question #${i}`, choices:['A','B','C','D'], a:0 }); } loadQuizToUI(quiz); }
function loadQuizToUI(quiz){ currentQuiz=quiz; document.getElementById('quiz-area').innerHTML = quiz.map((item, idx)=>`<div style="margin-bottom:10px"><div style="font-weight:700">${idx+1}. ${escapeHtml(item.q)}</div><div>${item.choices.map((c,ci)=>`<label style="display:block;margin-top:6px"><input type="radio" name="q${idx}" value="${ci}"> ${escapeHtml(c)}</label>`).join('')}</div></div>`).join('') + '<div style="display:flex;gap:8px"><button class="btn" onclick="gradeCurrentQuiz()">Submit</button><button class="btn" onclick="saveCurrentQuiz()">Save Quiz</button></div>'; }
function gradeCurrentQuiz(){ if(!currentQuiz) return alert('No quiz loaded'); let score=0; currentQuiz.forEach((q,i)=>{ const sel=document.querySelector(`input[name="q${i}"]:checked`); if(sel && Number(sel.value)===(q.a||0)) score++; }); alert(`Score: ${score} / ${currentQuiz.length}`); }
function saveCurrentQuiz(){ if(!currentQuiz) return alert('No quiz to save'); const item={ id:Date.now(), type:'quiz', title:'Quiz ' + new Date().toLocaleString(), data: currentQuiz }; saved.unshift(item); persistSaved(); renderSavedList(); alert('Quiz saved.'); }
function createManualQuiz(){ const raw=document.getElementById('quiz-input').value.trim(); if(!raw){ alert('Enter manual quiz lines'); return; } const lines=raw.split(/\\r?\\n/).map(l=>l.trim()).filter(Boolean); const quiz=lines.map(line=>{ const ansMatch=line.match(/\\((\\d+)\\)\\s*$/); let ans=0; let core=line; if(ansMatch){ ans=Number(ansMatch[1]); core=line.replace(/\\(\\d+\\)\\s*$/,'').trim(); } const parts=core.split('|').map(p=>p.trim()); const q=parts.shift(); const choices=parts; return { q, choices: choices.length?choices:['A','B','C','D'], a: ans||0 }; }); loadQuizToUI(quiz); }

/* Quiz Study */
function populateStudySources(){ const sel=document.getElementById('study-source'); sel.innerHTML = saved.map(s=>`<option value="${s.id}">${escapeHtml(s.title|| (s.type+' set'))} (${s.type})</option>`).join(''); }
function startStudy(){ const id=Number(document.getElementById('study-source').value); const item=saved.find(s=>s.id===id); if(!item){ alert('Choose a saved set'); return; } studyQueue=[]; if(item.type==='flash'){ studyQueue = item.data.map(c=>({ type:'flash', q:c.q, a:c.a })); } else if(item.type==='quiz'){ studyQueue = item.data.map(q=>({ type:'quiz', q:q.q, choices:q.choices, a:q.a })); } else { alert('Unsupported type'); return; } studyResults=[]; presentStudyItem(0); }
function presentStudyItem(idx){ if(idx>=studyQueue.length){ const wrong=studyResults.filter(r=>!r.correct); let html=`<div style="font-weight:700">Study complete</div><div class="small" style="margin-top:8px">Total: ${studyResults.length}, Correct: ${studyResults.filter(r=>r.correct).length}, Wrong: ${wrong.length}</div>`; if(wrong.length) html += `<div style="margin-top:8px"><button class="btn" onclick="repeatWrong()">Repeat wrong</button></div>`; document.getElementById('study-area').innerHTML = html; return; } const it=studyQueue[idx]; if(it.type==='flash'){ document.getElementById('study-area').innerHTML = `<div style="font-weight:700">${escapeHtml(it.q)}</div><div style="margin-top:8px"><button class="btn" onclick="studyMark(true,${idx})">I knew it</button> <button class="btn" onclick="studyMark(false,${idx})">I didn't</button></div>`; } else { document.getElementById('study-area').innerHTML = `<div style="font-weight:700">${escapeHtml(it.q)}</div><div style="margin-top:8px">${it.choices.map((c,i)=>`<label style="display:block;margin:6px 0"><input type="radio" name="studyq" value="${i}"> ${escapeHtml(c)}</label>`).join('')}<div style="margin-top:8px"><button class="btn" onclick="studyCheckAnswer(${idx})">Submit</button></div></div>`; } }
function studyMark(knew, idx){ studyResults.push({ idx, correct:knew }); presentStudyItem(idx+1); }
function studyCheckAnswer(idx){ const sel=document.querySelector('input[name="studyq"]:checked'); if(!sel){ alert('Select answer'); return; } const it=studyQueue[idx]; const correct = Number(sel.value) === it.a; studyResults.push({ idx, correct }); presentStudyItem(idx+1); }
function repeatWrong(){ studyQueue = studyResults.filter(r=>!r.correct).map(r=>studyQueue[r.idx]); studyResults=[]; presentStudyItem(0); }

/* Saved items */
function persistSaved(){ setLS(STORE_SAVED, saved); populateStudySources(); }
function renderSavedList(){ persistSaved(); const el=document.getElementById('saved-list'); if(!saved||saved.length===0){ el.innerHTML='<div class="small">No saved items</div>'; return; } el.innerHTML = saved.map(s=>`<div style="padding:8px;border-radius:8px;margin-bottom:8px;background:linear-gradient(180deg,#24163f,#21133a)"><div style="display:flex;justify-content:space-between;align-items:center"><div style="font-weight:700">${escapeHtml(s.title|| (s.type+' set'))}</div><div style="display:flex;gap:6px"><button class="btn ghost" onclick="loadSaved(${s.id})">Load</button><button class="btn ghost" onclick="deleteSaved(${s.id})">Delete</button></div></div><div class="small" style="margin-top:6px">Type: ${s.type} — ${s.data? s.data.length : ''} items</div></div>`).join(''); populateStudySources(); }
function loadSaved(id){ const item=saved.find(s=>s.id===id); if(!item) return alert('Not found'); if(item.type==='flash'){ currentSet={ id:item.id, title:item.title, cards:item.data }; renderCurrentSet(); showPanel('flash'); } else if(item.type==='quiz'){ currentQuiz=item.data; loadQuizToUI(currentQuiz); showPanel('quiz'); } }
function deleteSaved(id){ if(!confirm('Delete saved item?')) return; saved = saved.filter(s=>s.id!==id); persistSaved(); renderSavedList(); }
function exportSaved(){ const blob=new Blob([JSON.stringify(saved,null,2)],{type:'application/json'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download='studyhub-saved.json'; a.click(); URL.revokeObjectURL(url); }
function importSaved(){ const inp=document.createElement('input'); inp.type='file'; inp.accept='application/json'; inp.onchange=e=>{ const f=e.target.files[0]; if(!f) return; const r=new FileReader(); r.onload=ev=>{ try{ const arr=JSON.parse(ev.target.result); if(!Array.isArray(arr)) throw new Error('Invalid'); arr.forEach(a=>a.id=Date.now()+Math.floor(Math.random()*100000)); saved=arr.concat(saved); persistSaved(); renderSavedList(); alert('Imported'); }catch(err){ alert('Invalid file'); } }; r.readAsText(f); }; inp.click(); }
function clearSaved(){ if(!confirm('Clear all saved items?')) return; saved=[]; persistSaved(); renderSavedList(); }

/* AI Tutor */
async function askTutor(){ const q=document.getElementById('tutor-q').value.trim(); if(!q) return alert('Type your question'); const area=document.getElementById('tutor-response'); area.innerHTML='<div class="small">Thinking... (uses selected provider and key)</div>'; const prompt=`Explain this clearly for a 7th grade student, with short bullets and one short example:\n\n${q}`; const ai=await callAI(prompt,{model:'gpt-4o-mini',temp:0.25,max_tokens:450}); if(ai===null){ area.innerHTML=`<div style="font-weight:700">Demo answer for: ${escapeHtml(q)}</div><div class="small" style="margin-top:8px">No API key set — set a Global or Personal API key to use the real AI.</div><div style="margin-top:8px">Short demo explanation: ${escapeHtml(q)} is important because... (demo)</div>`; return; } area.innerHTML=`<div style="font-weight:700">Tutor answer</div><div style="margin-top:8px">${ai.replace(/\n/g,'<br>')}</div>`; }

/* Mini browser */
function openMini(){ let url=(document.getElementById('mini-url').value||'').trim(); if(!url) return; if(!/^https?:\/\//i.test(url)) url='https://'+url; document.getElementById('mini-frame').src=url; }

/* Init UI */
renderSavedList();
populateStudySources();
</script>
</body>
</html>
