<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GamesUnblocked v1.5</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
<style>
/* --- Base Styling --- */
body {
    margin:0;
    padding:0;
    font-family:'Roboto',sans-serif;
    background:linear-gradient(135deg,#1c1c1c,#3a1c3a);
    color:#f0f0f0;
    display:flex;
    flex-direction:column;
    align-items:center;
    min-height:100vh;
}

/* --- Header / Welcome --- */
header {
    text-align:center;
    margin-top:50px;
}
header h1 { font-size:3em; margin-bottom:5px; }
header p { font-size:1.2em; color:#bbb; }

/* --- Circular Navigation --- */
.nav-container {
    display:flex;
    justify-content:center;
    gap:50px;
    margin-top:50px;
    flex-wrap:wrap;
}
.nav-circle {
    width:120px;
    height:120px;
    border-radius:50%;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    cursor:pointer;
    transition:transform 0.3s, box-shadow 0.3s;
    text-align:center;
    font-weight:bold;
    font-size:1em;
}
.nav-circle span.icon { font-size:2.5em; margin-bottom:8px; }
.nav-circle:hover { transform: scale(1.1); box-shadow: 0 0 20px rgba(255,255,255,0.5); }

/* --- Colors for each circle --- */
.nav-games {background:#ff6f61; color:#fff;}
.nav-bookmarklets {background:#4caf50; color:#fff;}
.nav-updates {background:#2196f3; color:#fff;}

/* --- Footer --- */
footer {
    margin-top:auto;
    margin-bottom:30px;
    font-size:0.9em;
    color:#888;
}

/* --- Update Modal --- */
.modal {
    display:none;
    position:fixed;
    top:0; left:0;
    width:100%; height:100%;
    background:rgba(0,0,0,0.9);
    z-index:100;
    justify-content:center;
    align-items:center;
}
.modal.active { display:flex; }
.modal-content {
    width:350px;
    padding:30px;
    background-color:#333;
    border-radius:10px;
    text-align:center;
    color:#f0f0f0;
    border:3px solid #ff5722;
}
.update-bar-container {
    width:100%;
    height:20px;
    background:#444;
    border-radius:10px;
    margin-top:15px;
    overflow:hidden;
}
#update-progress-bar {
    width:0%; height:100%; background:#ff5722; transition:width 0.3s linear;
}

/* --- Button Styling --- */
button {
    padding:10px 15px;
    border:none;
    border-radius:5px;
    font-weight:bold;
    cursor:pointer;
    margin-top:15px;
}
#update-btn { background:#ff5722; color:white; }
#downgrade-btn { background:#f44336; color:white; }
</style>
</head>
<body>

<header>
    <h1>GamesUnblocked</h1>
    <p id="welcome-msg">Welcome!</p>
</header>

<div class="nav-container">
    <div class="nav-circle nav-games" onclick="openPage('games')">
        <span class="icon">🎮</span>
        Games
    </div>
    <div class="nav-circle nav-bookmarklets" onclick="openPage('bookmarklets')">
        <span class="icon">🔖</span>
        Bookmarklets
    </div>
    <div class="nav-circle nav-updates" onclick="openPage('updates')">
        <span class="icon">✨</span>
        Updates
    </div>
</div>

<!-- Update / Downgrade Modal -->
<div id="updateModal" class="modal">
    <div class="modal-content">
        <h3 id="update-title">Updating...</h3>
        <div class="update-bar-container">
            <div id="update-progress-bar"></div>
        </div>
    </div>
</div>

<footer>
    &copy; 2025 GamesUnblocked. Subscribe to cursedgamer2 for beta access.
</footer>

<script>
// --- Version System ---
const CURRENT_VERSION = "1.5";
let storedVersion = localStorage.getItem('gamesunblocked_version') || "1.3";

// Random welcome messages
const messages = ["Your gateway to fun!","Ready to play?","Explore new bookmarklets!","Stay unblocked & safe!","CursedGamer2 beta vibes!"];
document.getElementById('welcome-msg').textContent = messages[Math.floor(Math.random()*messages.length)];

// --- Navigation ---
function openPage(page){
    if(page==="games") alert("Games page would open here.");
    else if(page==="bookmarklets") alert("Bookmarklets page would open here.");
    else if(page==="updates") openModal('updateModal');
}

// --- Modal ---
function openModal(id){document.getElementById(id).classList.add('active'); runUpdateSimulation();}
function closeModal(id){document.getElementById(id).classList.remove('active');}

// --- Update / Downgrade Simulation ---
function runUpdateSimulation(){
    const progressBar = document.getElementById('update-progress-bar');
    const title = document.getElementById('update-title');
    let progress = 0;
    title.textContent = `Updating to v${CURRENT_VERSION}...`;
    progressBar.style.width='0%';
    const interval = setInterval(()=>{
        progress+=2;
        progressBar.style.width = progress + '%';
        if(progress>=100){
            clearInterval(interval);
            title.textContent = `Update Complete! Reloading...`;
            localStorage.setItem('gamesunblocked_version',CURRENT_VERSION);
            setTimeout(()=>{closeModal('updateModal'); location.reload();},1000);
        }
    },150);
}

// --- Flood History (v1.4 Feature) ---
function floodHistory(count){
    for(let i=0;i<count;i++){history.pushState(null,'',`/fake-history-${i}`);}
    alert(`${count} fake history entries added!`);
}
</script>
</body>
</html>
