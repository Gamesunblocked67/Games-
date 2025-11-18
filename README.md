<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ultimate Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    
    <style>
        :root { --primary-color: #3b82f6; }
        .bg-primary { background-color: var(--primary-color); }
        .text-primary { color: var(--primary-color); }
        .border-primary { border-color: var(--primary-color); }
        .hover-bg-primary:hover { background-color: var(--primary-color); }
        
        html, body { height: 100%; overflow: hidden; }
        body { font-family: 'Inter', sans-serif; background-color: #1f2937; color: #f3f4f6; display: flex; flex-direction: column; }
        .card { background-color: #374151; border-radius: 0.75rem; box-shadow: 0 10px 15px rgba(0, 0, 0, 0.2); }
        
        /* Main Content Area */
        #main-content-area { flex-grow: 1; overflow-y: auto; padding-bottom: 2rem; }
        
        /* Game Container */
        .game-container { width: 100%; height: calc(100vh - 220px); max-width: 1200px; margin: 0 auto; overflow: hidden; border-radius: 0.75rem; border: 3px solid var(--primary-color); }
        
        /* Inputs */
        .input-dark { width: 100%; padding: 10px; border-radius: 6px; border: 1px solid #4b5563; background: #111827; color: #f3f4f6; margin-top: 5px; }
        .input-dark:focus { outline: none; border-color: var(--primary-color); }

        /* Modal */
        .modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.8); z-index: 9999; display: flex; justify-content: center; align-items: center; }
        .modal-content { background-color: #1f2937; padding: 2rem; border-radius: 1rem; max-width: 400px; text-align: center; border: 2px solid var(--primary-color); }
        
        .hidden { display: none !important; }
        .locked { opacity: 0.5; pointer-events: none; }
    </style>

    <!-- 1. STANDARD SCRIPT for UI (Guarantees Buttons Work) -->
    <script>
        // -- Global State --
        window.currentTheme = '#3b82f6';
        window.GLOBAL_KEY = ''; // Stores the API Key loaded from DB
        window.ownerUnlocked = false;

        // -- UI Functions (Global) --
        function openTab(tabName, button) {
            // Hide all tabs
            ['bookmarklets', 'games', 'study', 'settings'].forEach(t => {
                const el = document.getElementById(t);
                if(el) el.classList.add('hidden');
            });
            
            // Show selected
            const target = document.getElementById(tabName);
            if(target) target.classList.remove('hidden');
            
            // Reset buttons
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('bg-gray-700', 'text-white'));
            // Active button
            if(button) button.classList.add('bg-gray-700', 'text-white');
            
            // Close settings panel if open
            if (tabName !== 'settings') {
                 document.getElementById('settings-panel').classList.add('hidden');
            }
        }

        function toggleSettings() {
            document.getElementById('settings-panel').classList.toggle('hidden');
        }

        function handleColorChange(e) {
            const color = e.target.value;
            document.documentElement.style.setProperty('--primary-color', color);
            window.currentTheme = color;
            window.dispatchEvent(new CustomEvent('themeChanged', { detail: color }));
        }

        function changeGame(url) {
            document.getElementById('game-embed').src = url;
        }

        // -- Owner Settings Logic --
        function unlockOwner() {
            const pass = document.getElementById('owner-pass').value;
            if (pass === 'owner123') {
                window.ownerUnlocked = true;
                document.getElementById('owner-controls').classList.remove('hidden');
                document.getElementById('owner-lock-msg').classList.add('hidden');
                alert('Owner mode unlocked!');
            } else {
                alert('Incorrect password.');
            }
        }

        function saveApiKey() {
            const key = document.getElementById('api-key-input').value.trim();
            if (key) {
                // Save to global variable temporarily
                window.GLOBAL_KEY = key;
                // Send to Firebase module to save persistently for ALL users
                if (window.saveGlobalKeyToDB) {
                    window.saveGlobalKeyToDB(key);
                    alert('API Key saved Globally! All users will now use this key.');
                } else {
                    alert('Database not ready. Key saved for this session only.');
                }
            }
        }

        // -- AI Logic (Gemini) --
        async function callAI(prompt, systemInst) {
            const outputDiv = document.getElementById('ai-output');
            // This function is a helper, actual calls are below in runAiTool
        }

        function runAiTool(toolType) {
            const input = document.getElementById(toolType + '-input').value;
            if(!input) return alert("Please enter text.");

            let system = "You are a helpful assistant.";
            let prompt = input;

            if(toolType === 'teacher') {
                system = "You are a helpful teacher. Explain things clearly and simply.";
            } else if(toolType === 'quiz') {
                system = "You are a quiz generator. Create a 3-question multiple choice quiz based on the topic.";
                prompt = `Generate a quiz about: ${input}`;
            } else if(toolType === 'flashcard') {
                system = "You are a study aid. Create 5 term:definition flashcards.";
                prompt = `Create flashcards for: ${input}`;
            }
            
            const targetId = toolType + '-result';
            const resultBox = document.getElementById(targetId);
            
            if(resultBox) {
                 resultBox.innerHTML = '<span class="text-yellow-400">Thinking...</span>';
                 
                 const apiKey = window.GLOBAL_KEY;
                 
                 if(!apiKey) { 
                     resultBox.innerHTML = '<span class="text-red-400">Error: Global API Key not loaded yet. If you are the owner, set it in Settings.</span>'; 
                     return; 
                 }

                 fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        contents: [{ parts: [{ text: prompt }] }],
                        systemInstruction: { parts: [{ text: system }] }
                    })
                 }).then(r => r.json()).then(data => {
                     if(data.error) throw new Error(data.error.message);
                     resultBox.innerHTML = data.candidates[0].content.parts[0].text.replace(/\n/g, '<br>');
                 }).catch(e => {
                     resultBox.innerHTML = `<span class="text-red-400">AI Error: ${e.message}</span>`;
                 });
            }
        }
        
        function runMath() {
            const v = document.getElementById('math-input').value;
            try {
                if (v.includes('window') || v.includes('doc')) throw new Error("Unsafe");
                const res = eval(v);
                document.getElementById('math-result').innerText = "Result: " + res;
            } catch(e) {
                document.getElementById('math-result').innerText = "Error: Invalid Expression";
            }
        }


        // -- Modal Logic --
        function hideSubscriptionModal() {
            document.getElementById('subscription-modal').classList.add('hidden');
            sessionStorage.setItem('hasSeenModal', 'true');
        }

        function handleSubscribe() {
            window.open('https://www.youtube.com/@cursedgamer2', '_blank');
            hideSubscriptionModal();
        }

        function checkModal() {
            if (!sessionStorage.getItem('hasSeenModal')) {
                document.getElementById('subscription-modal').classList.remove('hidden');
            }
        }

        // -- Initialize UI on Load --
        window.addEventListener('DOMContentLoaded', () => {
            // Populate Bookmarklets
            const bookmarklets = [
                { title: "Zoom Text", code: "javascript:(function(){var size=parseFloat(document.body.style.fontSize)||16;size*=1.2;document.body.style.fontSize=size+'px';})();" },
                { title: "Edit Page", code: "javascript:document.body.contentEditable='true';document.designMode='on';void 0" },
                { title: "Dark Mode", code: "javascript:(function(){var s=document.createElement('style');s.innerHTML='body{filter:invert(100%)!important;background:#222!important;}img,video{filter:invert(100%)!important;}';document.head.appendChild(s);})();" },
                { title: "History Back", code: "javascript:history.back()" },
                { title: "Show Passwords", code: "javascript:(function(){var s,F,j,f,i;s='';F=document.forms;for(j=0;j<F.length;++j){f=F[j];for(i=0;i<f.length;++i){if(f[i].type.toLowerCase()=='password')s+=f[i].value+'\\n';}}if(s)alert('Passwords in forms:\\n\\n'+s);else alert('No passwords found in forms.');})();" },
                { title: "Piano", code: "javascript:(function(){var s=document.createElement('script');s.setAttribute('src','https://www.funhtml5games.com/bookmarklets/piano/piano.js');document.body.appendChild(s);})();" }
            ];
            
            const container = document.getElementById('bm-grid');
            if(container) {
                container.innerHTML = bookmarklets.map(b => `
                    <div class="card p-4 hover:ring-2 hover-ring-primary transition">
                        <h3 class="font-bold text-lg mb-2">${b.title}</h3>
                        <a href="${b.code}" class="inline-block px-4 py-2 bg-primary text-white rounded shadow hover:opacity-90">Drag Me</a>
                    </div>
                `).join('');
            }

            checkModal();
        });
    </script>
</head>
<body class="p-4 sm:p-8">

    <!-- Header -->
    <header class="text-center mb-6">
        <h1 class="text-4xl font-extrabold mb-2">The Ultimate Portal</h1>
        <p class="text-gray-400">Your personalized launchpad.</p>
    </header>

    <div id="status-message" class="text-center text-yellow-400 text-xs mb-4 h-4"></div>

    <!-- Nav (Standard OnClick) -->
    <nav class="flex justify-center card p-1 mb-6 space-x-1 max-w-4xl mx-auto">
        <button onclick="openTab('bookmarklets', this)" class="flex-1 py-2 px-4 rounded-lg font-medium bg-gray-700 text-white transition-all">Bookmarklets</button>
        <button onclick="openTab('games', this)" class="flex-1 py-2 px-4 rounded-lg font-medium text-gray-300 hover:bg-gray-600 transition-all">Games</button>
        <button onclick="openTab('study', this)" class="flex-1 py-2 px-4 rounded-lg font-medium text-gray-300 hover:bg-gray-600 transition-all">Study Hub</button>
        <button onclick="toggleSettings()" class="py-2 px-4 rounded-lg font-medium text-gray-300 hover:bg-gray-600 transition-all">Settings</button>
    </nav>
    
    <div id="main-content-area" class="max-w-4xl mx-auto w-full">

        <!-- Settings Panel -->
        <div id="settings-panel" class="card p-6 mb-6 hidden">
            <h2 class="text-2xl font-semibold mb-4">Settings</h2>
            
            <!-- Theme -->
            <div class="mb-6">
                <label class="font-medium block mb-2">Theme Color:</label>
                <div class="flex items-center justify-between p-3 card bg-gray-600">
                    <span>Pick Color</span>
                    <input type="color" value="#3b82f6" oninput="handleColorChange(event)" class="h-8 w-16 cursor-pointer rounded">
                </div>
            </div>

            <!-- Owner Zone -->
            <div class="card p-4 border border-gray-600">
                <h3 class="font-bold text-lg mb-2 text-red-400">Owner Zone</h3>
                <p class="text-xs text-gray-400 mb-2">Unlock to set the Global API Key for all users.</p>
                
                <!-- Locked State -->
                <div id="owner-lock-msg">
                    <input type="password" id="owner-pass" placeholder="Enter Password" class="input-dark mb-2">
                    <button onclick="unlockOwner()" class="w-full py-2 bg-gray-600 hover:bg-gray-500 rounded font-bold mt-1">Unlock</button>
                </div>

                <!-- Unlocked State (Hidden Initially) -->
                <div id="owner-controls" class="hidden">
                    <label class="block text-sm mb-1 text-gray-400">Global AI API Key (Gemini):</label>
                    <input type="text" id="api-key-input" placeholder="sk-..." class="input-dark mb-2">
                    <button onclick="saveApiKey()" class="w-full py-2 bg-green-600 hover:bg-green-500 rounded font-bold">Save Globally</button>
                </div>
            </div>

            <div id="user-id-display" class="text-xs text-right text-gray-500 mt-4"></div>
        </div>

        <!-- Bookmarklets -->
        <div id="bookmarklets" class="tab-content">
            <h2 class="text-3xl font-bold mb-4">Bookmarklets</h2>
            <div id="bm-grid" class="grid grid-cols-1 md:grid-cols-2 gap-4"></div>
        </div>

        <!-- Games (iframe is here) -->
        <div id="games" class="tab-content hidden">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-3xl font-bold">Games</h2>
                <div class="space-x-2">
                    <button onclick="changeGame('https://geometry-lessons.github.io')" class="text-xs bg-gray-600 px-2 py-1 rounded hover:bg-primary">Geometry</button>
                    <button onclick="changeGame('https://tbg95.github.io/2048/')" class="text-xs bg-gray-600 px-2 py-1 rounded hover:bg-primary">2048</button>
                </div>
            </div>
            
            <div class="game-container card bg-gray-800">
                <iframe id="game-embed" class="w-full h-full" src="https://geometry-lessons.github.io" frameborder="0" allowfullscreen></iframe>
            </div>
        </div>

        <!-- Study Hub (AI Tools) -->
        <div id="study" class="tab-content hidden">
            <h2 class="text-3xl font-bold mb-4">AI Study Hub</h2>
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <!-- AI Teacher -->
                <div class="card p-4">
                    <h3 class="font-bold text-xl text-blue-400 mb-2">AI Teacher</h3>
                    <textarea id="teacher-input" rows="3" class="input-dark" placeholder="Ask a question..."></textarea>
                    <button onclick="runAiTool('teacher')" class="mt-2 w-full bg-primary py-2 rounded font-bold hover:opacity-90">Ask</button>
                    <div id="teacher-result" class="mt-3 text-sm p-2 bg-gray-800 rounded min-h-[60px]"></div>
                </div>

                <!-- AI Quiz -->
                <div class="card p-4">
                    <h3 class="font-bold text-xl text-purple-400 mb-2">Quiz Maker</h3>
                    <input id="quiz-input" type="text" class="input-dark" placeholder="Enter topic (e.g., Biology)">
                    <button onclick="runAiTool('quiz')" class="mt-2 w-full bg-purple-600 py-2 rounded font-bold hover:opacity-90">Generate Quiz</button>
                    <div id="quiz-result" class="mt-3 text-sm p-2 bg-gray-800 rounded min-h-[60px]"></div>
                </div>

                <!-- Flashcards -->
                <div class="card p-4">
                    <h3 class="font-bold text-xl text-green-400 mb-2">Flashcards</h3>
                    <input id="flashcard-input" type="text" class="input-dark" placeholder="Enter topic">
                    <button onclick="runAiTool('flashcard')" class="mt-2 w-full bg-green-600 py-2 rounded font-bold hover:opacity-90">Create Cards</button>
                    <div id="flashcard-result" class="mt-3 text-sm p-2 bg-gray-800 rounded min-h-[60px]"></div>
                </div>

                <!-- Math Solver (Local) -->
                <div class="card p-4">
                    <h3 class="font-bold text-xl text-orange-400 mb-2">Math Solver</h3>
                    <input id="math-input" type="text" class="input-dark" placeholder="e.g. 5 * (10 + 2)">
                    <button onclick="runMath()" class="mt-2 w-full bg-orange-600 py-2 rounded font-bold hover:opacity-90">Solve</button>
                    <div id="math-result" class="mt-3 text-sm p-2 bg-gray-800 rounded min-h-[30px]">Result:</div>
                </div>
            </div>
        </div>

    </div>
    
    <!-- Modal -->
    <div id="subscription-modal" class="modal-overlay hidden">
        <div class="modal-content">
            <h3 class="text-2xl font-bold mb-4 text-primary">👋 Support the Project!</h3>
            <p class="mb-6 text-gray-300">Subscribe to support development!</p>
            <button onclick="handleSubscribe()" class="w-full mb-3 px-6 py-3 bg-red-600 text-white font-bold rounded hover:bg-red-700 transition">
                SUBSCRIBE NOW ▶️
            </button>
            <button onclick="hideSubscriptionModal()" class="w-full px-6 py-2 bg-gray-600 text-gray-300 font-semibold rounded hover:bg-gray-500">
                Dismiss
            </button>
        </div>
    </div>

    <!-- 2. MODULE SCRIPT for Database (Runs in background) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        
        let db, auth, userId;

        async function initFirebase() {
            if (Object.keys(firebaseConfig).length === 0) return;
            
            const app = initializeApp(firebaseConfig);
            db = getFirestore(app);
            auth = getAuth(app);
            
            await signInAnonymously(auth);
            
            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    userId = user.uid;
                    document.getElementById('user-id-display').textContent = `ID: ${userId.substring(0,8)}...`;
                    document.getElementById('status-message').textContent = "Settings Sync Active";
                    loadUserTheme(userId);
                    
                    // Load global API Key immediately on auth
                    loadGlobalKey();
                }
            });
        }

        // --- Load/Save User Theme (Private) ---
        async function loadUserTheme(uid) {
            try {
                const ref = doc(db, 'artifacts', appId, 'users', uid, 'settings', 'theme');
                const snap = await getDoc(ref);
                if (snap.exists()) {
                    const color = snap.data().primaryColor;
                    document.documentElement.style.setProperty('--primary-color', color);
                    const picker = document.querySelector('input[type="color"]');
                    if(picker) picker.value = color;
                }
            } catch(e) { console.log("Theme load error", e); }
        }

        async function saveUserTheme(color) {
            if (!userId || !db) return;
            try {
                const ref = doc(db, 'artifacts', appId, 'users', userId, 'settings', 'theme');
                await setDoc(ref, { primaryColor: color }, { merge: true });
            } catch(e) { console.log("Theme save error", e); }
        }

        // --- Load/Save Global API Key (Public Document) ---
        async function saveGlobalKey(key) {
            if (!db) return;
            try {
                // Storing in a public location so all users can read it
                // Path: /artifacts/{appId}/public/data/config/main
                const ref = doc(db, 'artifacts', appId, 'public', 'data', 'config', 'global_ai');
                await setDoc(ref, { apiKey: key }, { merge: true });
                console.log("Global Key saved.");
            } catch(e) { console.error("Global Key Save Error:", e); }
        }

        async function loadGlobalKey() {
            if (!db) return;
            try {
                const ref = doc(db, 'artifacts', appId, 'public', 'data', 'config', 'global_ai');
                const snap = await getDoc(ref);
                if (snap.exists()) {
                    const key = snap.data().apiKey;
                    if (key) {
                        window.GLOBAL_KEY = key;
                        console.log("Global API Key loaded.");
                        // Visual indicator (optional, for debug)
                        // document.getElementById('status-message').textContent += " | AI Ready";
                    }
                }
            } catch(e) { console.error("Global Key Load Error:", e); }
        }

        // --- Event Listeners to Bridge UI and Module ---
        window.addEventListener('themeChanged', (e) => saveUserTheme(e.detail));
        
        // Expose save function to global scope for the Owner UI to call
        window.saveGlobalKeyToDB = saveGlobalKey;
        
        initFirebase();
    </script>
</body>
</html>
