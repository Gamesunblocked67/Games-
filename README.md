<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>7th Grade Ultimate Portal</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/tone@14.9.15/build/Tone.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
    
    <!-- Firebase Setup -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'study-hub-v7';

        let db, auth;
        
        // Global settings state
        window.appSettings = {
            theme: '#3b82f6',
            aiProvider: 'Gemini',
            keys: {}
        };

        if (firebaseConfig) {
            const app = initializeApp(firebaseConfig);
            db = getFirestore(app);
            auth = getAuth(app);
            await signInAnonymously(auth);

            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    // 1. Load Public Global Keys (Listener for real-time updates)
                    const globalConfigRef = doc(db, 'artifacts', appId, 'public_data', 'global_config');
                    onSnapshot(globalConfigRef, (doc) => {
                        if (doc.exists()) {
                            const data = doc.data();
                            if (data.keys) {
                                window.appSettings.keys = data.keys;
                                console.log("Global API Keys Loaded");
                                // Update UI if Settings panel is open
                                const geminiInput = document.getElementById('gemini-key');
                                const openaiInput = document.getElementById('openai-key');
                                if(geminiInput && window.ownerUnlocked) geminiInput.value = data.keys.gemini || '';
                                if(openaiInput && window.ownerUnlocked) openaiInput.value = data.keys.openai || '';
                            }
                        }
                    });

                    // 2. Load User Private Settings (Theme)
                    const userConfigRef = doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'config');
                    const snap = await getDoc(userConfigRef);
                    if (snap.exists()) {
                        const data = snap.data();
                        if(data.theme) applyTheme(data.theme);
                        if(data.provider) window.appSettings.aiProvider = data.provider;
                        updateUiFromSettings();
                    }
                }
            });

            // Save function exposed globally
            window.saveSettingsToDb = async (scope) => {
                if (!auth.currentUser) return;

                // Save Private Settings (Theme, Provider preference)
                if (scope === 'local' || scope === 'both') {
                    const userDocRef = doc(db, 'artifacts', appId, 'users', auth.currentUser.uid, 'settings', 'config');
                    await setDoc(userDocRef, {
                        theme: window.appSettings.theme,
                        provider: window.appSettings.aiProvider
                    }, { merge: true });
                }

                // Save Global Keys (Only if Owner Mode is unlocked)
                if ((scope === 'global' || scope === 'both') && window.ownerUnlocked) {
                    const globalDocRef = doc(db, 'artifacts', appId, 'public_data', 'global_config');
                    await setDoc(globalDocRef, {
                        keys: window.appSettings.keys
                    }, { merge: true });
                    alert("Global API Keys Saved for ALL Users!");
                } else if (scope === 'local') {
                    alert("User Preferences Saved.");
                }
            };
        }
    </script>

    <style>
        :root { --primary-color: #3b82f6; }
        .bg-primary { background-color: var(--primary-color); }
        .text-primary { color: var(--primary-color); }
        .border-primary { border-color: var(--primary-color); }
        
        /* Layout Fix: Flex Column with fixed height */
        body { 
            font-family: 'Inter', sans-serif; 
            background-color: #111827; 
            color: #f3f4f6; 
            height: 100vh; 
            overflow: hidden; 
            display: flex; 
            flex-direction: column; 
        }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-thumb { background: #4b5563; border-radius: 4px; }
        ::-webkit-scrollbar-track { background: #1f2937; }

        /* The container for tab content takes remaining space and scrolls internally */
        #content-wrapper {
            flex-grow: 1;
            overflow: hidden; /* Prevent wrapper itself from scrolling */
            position: relative;
        }

        .tab-content { 
            height: 100%; 
            display: flex; 
            flex-direction: column; 
            overflow-y: auto; /* Scroll inside the tab */
            padding-bottom: 2rem;
        }
        
        .hidden { display: none !important; }
        
        .resource-link:hover { transform: translateX(5px); }
        .youtube-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; border-radius: 0.5rem; }
        .youtube-container iframe { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }
    </style>

    <script>
        // --- UI LOGIC ---
        window.ownerUnlocked = false;
        
        function applyTheme(color) {
            document.documentElement.style.setProperty('--primary-color', color);
            window.appSettings.theme = color;
            const picker = document.getElementById('color-picker');
            if(picker) picker.value = color;
        }

        function openTab(tabId, btn) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(tabId).classList.remove('hidden');
            
            document.querySelectorAll('nav button').forEach(b => b.classList.remove('bg-gray-700', 'text-white'));
            if(btn) btn.classList.add('bg-gray-700', 'text-white');
            
            // Special handling for subject resources
            if(tabId === 'resources') loadResources('Math');
        }

        function updateUiFromSettings() {
            document.getElementById('ai-provider-select').value = window.appSettings.aiProvider;
            // Keys are hidden unless owner mode is on, but we update the object
            document.getElementById('color-picker').value = window.appSettings.theme;
            applyTheme(window.appSettings.theme);
        }
        
        function toggleSettings() {
            const panel = document.getElementById('settings-panel');
            panel.classList.toggle('hidden');
        }

        function unlockOwner() {
            const pass = document.getElementById('owner-pass').value;
            if (pass === 'owner123') {
                window.ownerUnlocked = true;
                document.getElementById('owner-controls').classList.remove('hidden');
                document.getElementById('owner-lock-msg').classList.add('hidden');
                
                // Populate inputs with loaded keys now that it's safe
                document.getElementById('gemini-key').value = window.appSettings.keys.gemini || '';
                document.getElementById('openai-key').value = window.appSettings.keys.openai || '';
            } else {
                alert('Incorrect password.');
            }
        }

        function saveLocalSettings() {
            // Update local state
            if (window.ownerUnlocked) {
                window.appSettings.keys.gemini = document.getElementById('gemini-key').value;
                window.appSettings.keys.openai = document.getElementById('openai-key').value;
            }
            window.appSettings.aiProvider = document.getElementById('ai-provider-select-settings').value;
            
            // Determine scope
            const scope = window.ownerUnlocked ? 'both' : 'local';

            if(window.saveSettingsToDb) window.saveSettingsToDb(scope);
            else alert("Settings saved locally (Database not connected).");
            
            // Update local UI immediately
            document.getElementById('ai-provider-select').value = window.appSettings.aiProvider;
            
            document.getElementById('settings-panel').classList.add('hidden');
        }

        // --- MUSIC PLAYER LOGIC ---
        
        function playMusic() {
            const type = document.getElementById('music-type').value;
            const input = document.getElementById('music-input').value;
            const playerArea = document.getElementById('player-area');
            const audioPlayer = document.getElementById('audio-player');

            playerArea.innerHTML = ''; // Clear previous
            audioPlayer.pause();

            if (type === 'youtube') {
                let videoId = input.split('v=')[1];
                const ampersandPosition = videoId ? videoId.indexOf('&') : -1;
                if(ampersandPosition !== -1) {
                  videoId = videoId.substring(0, ampersandPosition);
                }
                if(videoId) {
                    playerArea.innerHTML = `<div class="youtube-container"><iframe src="https://www.youtube.com/embed/${videoId}?autoplay=1" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe></div>`;
                } else {
                    alert("Invalid YouTube URL");
                }
            } else if (type === 'url') {
                audioPlayer.src = input;
                audioPlayer.classList.remove('hidden');
                audioPlayer.play();
            }
        }

        function handleFileUpload(event) {
            const file = event.target.files[0];
            if (file) {
                const url = URL.createObjectURL(file);
                const audioPlayer = document.getElementById('audio-player');
                audioPlayer.src = url;
                audioPlayer.classList.remove('hidden');
                audioPlayer.play();
            }
        }

        // Tone.js Noise
        let noiseSynth;
        function toggleNoise(type) {
            if(noiseSynth) {
                noiseSynth.stop();
                noiseSynth.dispose();
                noiseSynth = null;
                document.getElementById('noise-btn').textContent = "Start Focus Noise";
                return;
            }
            
            // Start Audio Context
            Tone.start();
            noiseSynth = new Tone.Noise(type).toDestination();
            // Auto-filter to make it softer
            const filter = new Tone.AutoFilter({
                frequency: "8m",
                min: 800,
                max: 15000
            }).connect(Tone.Master);
            noiseSynth.connect(filter);
            
            noiseSynth.start();
            document.getElementById('noise-btn').textContent = "Stop Noise";
        }

        // --- TIMER LOGIC ---
        let timerInterval;
        let timeLeft;
        
        function startTimer(minutes) {
            clearInterval(timerInterval);
            timeLeft = minutes * 60;
            updateTimerDisplay();
            
            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerDisplay();
                if(timeLeft <= 0) {
                    clearInterval(timerInterval);
                    new Audio('https://actions.google.com/sounds/v1/alarms/beep_short.ogg').play();
                    alert("Time's up!");
                }
            }, 1000);
        }

        function updateTimerDisplay() {
            const m = Math.floor(timeLeft / 60);
            const s = timeLeft % 60;
            document.getElementById('timer-display').textContent = `${m}:${s < 10 ? '0' : ''}${s}`;
        }

        function resetTimer() {
            clearInterval(timerInterval);
            document.getElementById('timer-display').textContent = "00:00";
        }

        // --- RESOURCE LIBRARY DATA (7th Grade) ---
        const resources = {
            'Math': [
                { title: "Ratios & Proportions Calculator", url: "https://www.calculatorsoup.com/calculators/math/ratios.php", type: "tool" },
                { title: "Integer Operations Game", url: "https://www.mathplayground.com/ASB_IntegerWarp.html", type: "game" },
                { title: "Equation Solver", url: "https://www.mathpapa.com/algebra-calculator.html", type: "tool" },
                { title: "Area & Circumference Visualizer", url: "https://www.geogebra.org/m/j32G5j47", type: "tool" },
                { title: "Probability Spinner", url: "https://wheelofnames.com/", type: "tool" },
                { title: "Khan Academy: 7th Grade Math", url: "https://www.khanacademy.org/math/cc-seventh-grade-math", type: "link" },
                { title: "IXL Math Practice", url: "https://www.ixl.com/math/grade-7", type: "link" },
                { title: "Desmos Graphing Calculator", url: "https://www.desmos.com/calculator", type: "tool" },
                { title: "Math is Fun: Geometry", url: "https://www.mathsisfun.com/geometry/index.html", type: "link" },
                { title: "Prodigy Math Game", url: "https://www.prodigygame.com/", type: "game" }
            ],
            'Science': [
                { title: "Periodic Table Interactive", url: "https://ptable.com/", type: "tool" },
                { title: "Cell Model Visualizer", url: "https://www.cellsalive.com/cells/cell_model.htm", type: "tool" },
                { title: "NASA Eyes on the Solar System", url: "https://eyes.nasa.gov/", type: "tool" },
                { title: "PhET Simulations", url: "https://phet.colorado.edu/", type: "tool" },
                { title: "Dynamic Earth (Plate Tectonics)", url: "https://interactive.learner.org/interactives/dynamicearth/", type: "tool" },
                { title: "National Geographic Kids", url: "https://kids.nationalgeographic.com/", type: "link" },
                { title: "Switch Zoo (Animal Adaptations)", url: "https://www.switchzoo.com/", type: "game" },
                { title: "Food Chain Game", url: "https://www.sheppardsoftware.com/content/animals/kidscorner/games/foodchaingame.htm", type: "game" },
                { title: "InnerBody Anatomy", url: "https://www.innerbody.com/", type: "tool" },
                { title: "Science Bob Experiments", url: "https://sciencebob.com/", type: "link" }
            ],
            'History': [
                { title: "Ancient Civilizations Timeline", url: "https://www.historyforkids.net/ancient-history.html", type: "link" },
                { title: "Google Earth Voyager", url: "https://earth.google.com/web/voyager", type: "tool" },
                { title: "The Democracy Project", url: "https://pbskids.org/democracy", type: "link" },
                { title: "Mission US (History Game)", url: "https://www.mission-us.org/", type: "game" },
                { title: "Interactive World Map", url: "https://geology.com/world/world-map.shtml", type: "tool" },
                { title: "Smithsonian History Explorer", url: "https://historyexplorer.si.edu/", type: "link" },
                { title: "iCivics (Gov Games)", url: "https://www.icivics.org/games", type: "game" },
                { title: "DocsTeach (Primary Sources)", url: "https://www.docsteach.org/", type: "link" },
                { title: "History.com Classroom", url: "https://www.history.com/classroom", type: "link" },
                { title: "Big History Project", url: "https://www.bighistoryproject.com/", type: "link" }
            ],
            'ELA': [
                { title: "Thesaurus.com", url: "https://www.thesaurus.com/", type: "tool" },
                { title: "Hemingway Editor (Writing Tool)", url: "https://hemingwayapp.com/", type: "tool" },
                { title: "Grammarly (Free Checker)", url: "https://www.grammarly.com/", type: "tool" },
                { title: "Vocabulary.com", url: "https://www.vocabulary.com/", type: "link" },
                { title: "Project Gutenberg (Free Books)", url: "https://www.gutenberg.org/", type: "link" },
                { title: "Poetry Foundation", url: "https://www.poetryfoundation.org/", type: "link" },
                { title: "ReadWriteThink", url: "https://www.readwritethink.org/", type: "link" },
                { title: "Quill.org (Grammar Practice)", url: "https://www.quill.org/", type: "tool" },
                { title: "Storyboard That", url: "https://www.storyboardthat.com/", type: "tool" },
                { title: "Citation Machine", url: "https://www.citationmachine.net/", type: "tool" }
            ]
        };

        function loadResources(subject) {
            const list = document.getElementById('resource-list');
            list.innerHTML = '';
            
            document.querySelectorAll('.subject-btn').forEach(b => b.classList.remove('bg-primary', 'text-white'));
            document.getElementById(`btn-${subject}`).classList.add('bg-primary', 'text-white');

            resources[subject].forEach(res => {
                const item = document.createElement('div');
                item.className = 'bg-gray-800 p-4 rounded-lg border border-gray-700 hover:border-primary transition resource-link cursor-pointer';
                item.innerHTML = `
                    <div class="flex justify-between items-center">
                        <div>
                            <h4 class="font-bold text-gray-200">${res.title}</h4>
                            <span class="text-xs uppercase font-semibold text-gray-500 tracking-wider">${res.type}</span>
                        </div>
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" /></svg>
                    </div>
                `;
                item.onclick = () => window.open(res.url, '_blank');
                list.appendChild(item);
            });
        }
        
        // --- AI Logic ---
        async function callAI() {
            const prompt = document.getElementById('ai-input').value;
            const provider = document.getElementById('ai-provider-select').value;
            const output = document.getElementById('ai-output');
            
            if(!prompt) return;
            
            output.innerHTML = '<span class="text-yellow-400">Thinking...</span>';
            
            const key = window.appSettings.keys[provider.toLowerCase()];
            
            if(!key) {
                output.innerHTML = '<span class="text-red-400">Error: API Key missing for ' + provider + '. Check Settings.</span>';
                return;
            }
            
            try {
                let text = "";
                if (provider === 'Gemini') {
                    const resp = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${key}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                    });
                    
                    const data = await resp.json();
                    
                    // FIX: Check for API errors specifically
                    if (data.error) {
                        throw new Error(data.error.message);
                    }
                    
                    // FIX: Safely access candidates
                    if (data.candidates && data.candidates.length > 0) {
                        text = data.candidates[0].content.parts[0].text;
                    } else {
                        text = "Error: No response from AI. (Check Safety Filters or Quota)";
                    }

                } else if (provider === 'OpenAI') {
                    const resp = await fetch('https://api.openai.com/v1/chat/completions', {
                        method: 'POST',
                        headers: { 'Authorization': `Bearer ${key}`, 'Content-Type': 'application/json' },
                        body: JSON.stringify({ model: "gpt-3.5-turbo", messages: [{role: "user", content: prompt}] })
                    });
                    const data = await resp.json();
                    
                    if (data.error) {
                         throw new Error(data.error.message);
                    }
                    text = data.choices[0].message.content;
                }
                
                output.innerHTML = marked.parse(text);
                
            } catch(e) {
                output.innerHTML = `<span class="text-red-400">Error: ${e.message}</span>`;
            }
        }

        // --- GENERATOR LOGIC ---
        async function runGenerator(type) {
            const topic = document.getElementById(type + '-topic').value;
            if (!topic) return alert("Please enter a topic!");
            
            // Reuse the AI function but with specific prompts
            const provider = document.getElementById('ai-provider-select').value;
            const key = window.appSettings.keys[provider.toLowerCase()];
            
            // FIX: Validation check before running
            if (!key) {
                 document.getElementById(type + '-result').innerHTML = '<span class="text-red-400">Error: API Key not set!</span>';
                 return; 
            }

            const resultDiv = document.getElementById(type + '-result');
            resultDiv.innerHTML = `<span class="text-yellow-400">Generating ${type}...</span>`;
            
            let prompt = "";
            if (type === 'flashcard') prompt = `Create 5 flashcards for the topic: ${topic}. Format as Question: Answer pairs.`;
            if (type === 'quiz') prompt = `Create a 3-question multiple choice quiz about: ${topic}. Include the answer key at the end.`;

             try {
                let text = "";
                if (provider === 'Gemini') {
                    const resp = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${key}`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ contents: [{ parts: [{ text: prompt }] }] })
                    });
                    const data = await resp.json();
                    
                    if (data.error) throw new Error(data.error.message);
                    
                    if (data.candidates && data.candidates.length > 0) {
                         text = data.candidates[0].content.parts[0].text;
                    } else {
                         text = "Error: No response content.";
                    }

                } else if (provider === 'OpenAI') {
                    const resp = await fetch('https://api.openai.com/v1/chat/completions', {
                        method: 'POST',
                        headers: { 'Authorization': `Bearer ${key}`, 'Content-Type': 'application/json' },
                        body: JSON.stringify({ model: "gpt-3.5-turbo", messages: [{role: "user", content: prompt}] })
                    });
                    const data = await resp.json();
                    if (data.error) throw new Error(data.error.message);
                    text = data.choices[0].message.content;
                }
                
                resultDiv.innerHTML = marked.parse(text);
                
            } catch(e) {
                resultDiv.innerHTML = `<span class="text-red-400">Error: ${e.message}</span>`;
            }
        }

    </script>
</head>
<body class="p-4 sm:p-6">

    <!-- Fixed Header -->
    <header class="mb-4 flex justify-between items-center">
        <div>
            <h1 class="text-3xl font-bold text-white">7th Grade Ultimate Hub</h1>
            <p class="text-gray-400 text-sm">Study. Focus. Achieve.</p>
        </div>
        <div class="flex space-x-2">
            <button onclick="toggleSettings()" class="p-2 bg-gray-800 rounded-full hover:bg-gray-700 transition">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-300" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /></svg>
            </button>
        </div>
    </header>

    <!-- Settings Modal -->
    <div id="settings-panel" class="hidden fixed inset-0 bg-black bg-opacity-90 z-50 flex items-center justify-center">
        <div class="bg-gray-800 p-6 rounded-xl max-w-md w-full border border-gray-700">
            <h2 class="text-xl font-bold mb-4 text-white">Settings</h2>
            <div class="space-y-4">
                <div>
                    <label class="block text-sm text-gray-400">Theme Color</label>
                    <input type="color" id="color-picker" class="w-full h-10 rounded cursor-pointer" oninput="applyTheme(this.value)">
                </div>
                <div>
                    <label class="block text-sm text-gray-400 mb-1">Provider Preference</label>
                    <select id="ai-provider-select-settings" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white" onchange="document.getElementById('ai-provider-select').value = this.value">
                        <option value="Gemini">Gemini</option>
                        <option value="OpenAI">OpenAI</option>
                    </select>
                </div>

                <!-- Owner Zone -->
                <div class="border-t border-gray-700 pt-4">
                    <h3 class="text-lg font-bold text-red-400 mb-2">Owner Zone (API Keys)</h3>
                    <p class="text-xs text-gray-400 mb-2">Saving keys here makes them available to ALL users.</p>
                    
                    <!-- Locked State -->
                    <div id="owner-lock-msg">
                        <p class="text-sm text-gray-400 mb-2">Enter password to edit global keys.</p>
                        <div class="flex space-x-2">
                            <input type="password" id="owner-pass" class="flex-grow bg-gray-900 border border-gray-700 rounded p-2 text-white focus:outline-none focus:border-primary" placeholder="Password">
                            <button onclick="unlockOwner()" class="bg-gray-700 hover:bg-gray-600 text-white px-4 py-2 rounded font-bold">Unlock</button>
                        </div>
                    </div>

                    <!-- Unlocked State -->
                    <div id="owner-controls" class="hidden space-y-3 mt-2">
                        <div>
                            <label class="block text-sm text-gray-400 mb-1">Gemini API Key (Global)</label>
                            <input type="password" id="gemini-key" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white">
                        </div>
                        <div>
                            <label class="block text-sm text-gray-400 mb-1">OpenAI API Key (Global)</label>
                            <input type="password" id="openai-key" class="w-full bg-gray-900 border border-gray-700 rounded p-2 text-white">
                        </div>
                    </div>
                </div>

                <button onclick="saveLocalSettings()" class="w-full bg-primary text-white py-2 rounded font-bold hover:opacity-90 mt-4">Save & Close</button>
            </div>
        </div>
    </div>

    <!-- Sticky Navigation -->
    <nav class="flex space-x-2 mb-4 overflow-x-auto pb-2 border-b border-gray-700">
        <button onclick="openTab('ai-generators', this)" class="px-4 py-2 rounded-lg bg-gray-700 text-white font-medium whitespace-nowrap">⚡ Study Generators</button>
        <button onclick="openTab('resources', this)" class="px-4 py-2 rounded-lg bg-gray-800 text-gray-300 font-medium hover:bg-gray-700 whitespace-nowrap">📚 Resources</button>
        <button onclick="openTab('music', this)" class="px-4 py-2 rounded-lg bg-gray-800 text-gray-300 font-medium hover:bg-gray-700 whitespace-nowrap">🎵 Music & Focus</button>
        <button onclick="openTab('bookmarklets', this)" class="px-4 py-2 rounded-lg bg-gray-800 text-gray-300 font-medium hover:bg-gray-700 whitespace-nowrap">🔖 Bookmarklets</button>
    </nav>

    <!-- SCROLLABLE CONTENT WRAPPER -->
    <div id="content-wrapper">
    
        <!-- 1. AI GENERATORS (formerly AI Hub) -->
        <div id="ai-generators" class="tab-content p-2">
            <div class="flex flex-col h-full">
                <!-- Provider & Chat -->
                <div class="mb-4 flex items-center space-x-4">
                    <select id="ai-provider-select" class="bg-gray-800 border border-gray-700 text-white text-sm rounded-lg p-2.5">
                        <option value="Gemini">Gemini</option>
                        <option value="OpenAI">OpenAI</option>
                    </select>
                    <span class="text-xs text-gray-500">Global Key Loaded</span>
                </div>

                <!-- Grid for Generators -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
                     <!-- Flashcard Maker -->
                     <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
                        <h3 class="text-lg font-bold text-primary mb-2">Flashcard Maker</h3>
                        <input type="text" id="flashcard-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Enter topic (e.g. Cell Biology)">
                        <button onclick="runGenerator('flashcard')" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-1 rounded">Generate Cards</button>
                        <div id="flashcard-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
                     </div>

                     <!-- Quiz Maker -->
                     <div class="bg-gray-800 p-4 rounded-lg border border-gray-700">
                        <h3 class="text-lg font-bold text-primary mb-2">Quiz Maker</h3>
                        <input type="text" id="quiz-topic" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2 text-white" placeholder="Enter topic (e.g. American Revolution)">
                        <button onclick="runGenerator('quiz')" class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-1 rounded">Generate Quiz</button>
                        <div id="quiz-result" class="mt-2 text-sm text-gray-300 max-h-32 overflow-y-auto"></div>
                     </div>
                </div>

                <!-- General AI Chat -->
                <div class="flex-grow bg-gray-800 rounded-xl border border-gray-700 p-4 overflow-y-auto mb-4 min-h-[200px]" id="ai-output">
                    <div class="text-center text-gray-500 mt-10">
                        <h3 class="text-xl font-bold mb-2">AI Study Assistant</h3>
                        <p>Ask for help with Math, Essay writing, or History facts below.</p>
                    </div>
                </div>
                <div class="flex space-x-2 pb-4">
                    <input type="text" id="ai-input" class="flex-grow bg-gray-900 border border-gray-700 text-white rounded-lg p-3 focus:outline-none focus:border-primary" placeholder="Ask your AI Tutor...">
                    <button onclick="callAI()" class="bg-primary text-white px-6 py-3 rounded-lg font-bold hover:opacity-90">Send</button>
                </div>
            </div>
        </div>

        <!-- 2. RESOURCE LIBRARY -->
        <div id="resources" class="tab-content hidden p-2">
            <div class="flex space-x-2 mb-4 overflow-x-auto">
                <button id="btn-Math" class="subject-btn px-4 py-2 rounded-full bg-gray-800 text-sm hover:bg-gray-700" onclick="loadResources('Math')">Math</button>
                <button id="btn-Science" class="subject-btn px-4 py-2 rounded-full bg-gray-800 text-sm hover:bg-gray-700" onclick="loadResources('Science')">Science</button>
                <button id="btn-History" class="subject-btn px-4 py-2 rounded-full bg-gray-800 text-sm hover:bg-gray-700" onclick="loadResources('History')">History</button>
                <button id="btn-ELA" class="subject-btn px-4 py-2 rounded-full bg-gray-800 text-sm hover:bg-gray-700" onclick="loadResources('ELA')">ELA</button>
            </div>
            <div id="resource-list" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 pb-20">
                <!-- Resources injected here -->
            </div>
        </div>

        <!-- 3. MUSIC & FOCUS -->
        <div id="music" class="tab-content hidden p-2">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 h-full pb-20">
                <!-- Player -->
                <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 flex flex-col">
                    <h3 class="text-xl font-bold mb-4 text-white">Music Player</h3>
                    <div class="mb-4">
                        <label class="block text-xs text-gray-400 mb-1">Source Type</label>
                        <select id="music-type" class="w-full bg-gray-900 border border-gray-700 rounded p-2 mb-2">
                            <option value="youtube">YouTube Link</option>
                            <option value="url">MP3 Link</option>
                        </select>
                        <input type="text" id="music-input" placeholder="Paste URL here..." class="w-full bg-gray-900 border border-gray-700 rounded p-2">
                        <div class="mt-2 flex justify-between items-center">
                            <label class="text-xs text-gray-400 cursor-pointer hover:text-white">
                                <input type="file" class="hidden" onchange="handleFileUpload(event)" accept="audio/*">
                                📁 Upload Local File
                            </label>
                            <button onclick="playMusic()" class="bg-green-600 text-white px-4 py-1 rounded text-sm font-bold">Play</button>
                        </div>
                    </div>
                    <div id="player-area" class="flex-grow bg-black rounded flex items-center justify-center overflow-hidden relative min-h-[250px]">
                        <span class="text-gray-600">Player Screen</span>
                        <audio id="audio-player" controls class="hidden absolute bottom-0 w-full"></audio>
                    </div>
                </div>

                <!-- Timer & Ambience -->
                <div class="flex flex-col space-y-6">
                    <!-- Timer -->
                    <div class="bg-gray-800 p-6 rounded-xl border border-gray-700 text-center flex-grow flex flex-col justify-center">
                        <h3 class="text-gray-400 uppercase tracking-widest text-xs font-bold mb-2">Focus Timer</h3>
                        <div id="timer-display" class="text-6xl font-mono font-bold text-white mb-6">00:00</div>
                        <div class="flex justify-center space-x-2 mb-4">
                            <button onclick="startTimer(25)" class="bg-blue-600 hover:bg-blue-500 text-white px-4 py-2 rounded">25m</button>
                            <button onclick="startTimer(5)" class="bg-blue-600 hover:bg-blue-500 text-white px-4 py-2 rounded">5m</button>
                            <button onclick="startTimer(15)" class="bg-blue-600 hover:bg-blue-500 text-white px-4 py-2 rounded">15m</button>
                        </div>
                        <button onclick="resetTimer()" class="text-red-400 text-sm hover:text-red-300">Reset</button>
                    </div>

                    <!-- Noise -->
                    <div class="bg-gray-800 p-6 rounded-xl border border-gray-700">
                        <h3 class="font-bold mb-2">Focus Ambience (Tone.js)</h3>
                        <div class="flex space-x-2">
                            <button onclick="toggleNoise('pink')" id="noise-btn" class="flex-1 bg-gray-700 hover:bg-gray-600 py-3 rounded text-sm">Start Focus Noise</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 4. BOOKMARKLETS -->
        <div id="bookmarklets" class="tab-content hidden p-2">
            <div class="flex gap-4 mb-6">
                <!-- Mini Player Bookmarklet -->
                <div class="bg-blue-900/20 border border-blue-500/30 p-4 rounded-lg flex-1">
                    <h3 class="text-blue-400 font-bold mb-1">🎵 Mini Music Player Bookmarklet</h3>
                    <p class="text-sm text-gray-400 mb-2">Drag this to bookmarks. Opens a popup player.</p>
                    <a href="javascript:(function(){window.open('data:text/html;charset=utf-8,' + encodeURIComponent('<!DOCTYPE html><html><head><title>Mini Player</title><style>body{margin:0;background:#111;color:#fff;display:flex;align-items:center;justify-content:center;height:100vh;font-family:sans-serif;}</style></head><body><h2>Paste YouTube/MP3 URL here to play</h2></body></html>'), 'MiniPlayer', 'width=400,height=400');})();" class="inline-block bg-blue-600 text-white px-4 py-2 rounded font-bold shadow-lg cursor-grab active:cursor-grabbing">Mini Player</a>
                </div>

                 <!-- Mini Browser Bookmarklet -->
                <div class="bg-green-900/20 border border-green-500/30 p-4 rounded-lg flex-1">
                    <h3 class="text-green-400 font-bold mb-1">🌐 Mini Browser Bookmarklet</h3>
                    <p class="text-sm text-gray-400 mb-2">Drag this to bookmarks. Opens a small browser popup.</p>
                    <a href="javascript:(function(){var w=window.open('about:blank','_blank','width=600,height=400,menubar=no,toolbar=no,location=no,status=no');var d=w.document;d.write('<!DOCTYPE html><html><head><title>Mini Browser</title><style>body{margin:0;display:flex;flex-direction:column;height:100vh;}input{padding:10px;border:none;border-bottom:1px solid #ccc;}iframe{flex-grow:1;border:none;}</style></head><body><input type=\'text\' id=\'url\' placeholder=\'Enter URL (https://...)\' style=\'width:100%\'><iframe id=\'frame\' src=\'about:blank\'></iframe><script>document.getElementById(\'url\').addEventListener(\'keydown\',function(e){if(e.key===\'Enter\'){document.getElementById(\'frame\').src=this.value;}});</script></body></html>');d.close();})();" class="inline-block bg-green-600 text-white px-4 py-2 rounded font-bold shadow-lg cursor-grab active:cursor-grabbing">Mini Browser</a>
                </div>
            </div>

            <h2 class="text-xl font-bold mb-4">Classic Tools</h2>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 pb-20">
                 <div class="bg-gray-800 p-4 rounded border border-gray-700">
                    <a href="javascript:document.body.contentEditable='true';document.designMode='on';void 0" class="text-primary font-bold block mb-1">Edit Page</a>
                    <p class="text-xs text-gray-500">Make any website text editable.</p>
                 </div>
                 <div class="bg-gray-800 p-4 rounded border border-gray-700">
                    <a href="javascript:(function(){var s=document.createElement('style');s.innerHTML='body{filter:invert(100%)!important;background:#222!important;}img,video{filter:invert(100%)!important;}';document.head.appendChild(s);})();" class="text-primary font-bold block mb-1">Dark Mode</a>
                    <p class="text-xs text-gray-500">Force dark mode on any site.</p>
                 </div>
                 <div class="bg-gray-800 p-4 rounded border border-gray-700">
                    <a href="javascript:(function(){window.scrollTo(0,0);})();" class="text-primary font-bold block mb-1">Scroll Top</a>
                    <p class="text-xs text-gray-500">Jump to top of page.</p>
                 </div>
            </div>
        </div>
    </div>
    
    <script>
        // Initialize with Math resources
        loadResources('Math');
    </script>

</body>
</html>
