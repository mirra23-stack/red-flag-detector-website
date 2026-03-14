<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Red Flag Detector 🚩</title>
    <style>
        :root {
            --pink: #ff85a2;
            --dark-pink: #f6416c;
            --bg: #fff0f3;
            --black: #2d3436;
            --green: #2ecc71;
            --yellow: #f1c40f;
            --red: #e74c3c;
        }

        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: var(--bg);
            background-image: radial-gradient(#ff85a2 0.5px, transparent 0.5px);
            background-size: 20px 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        .container {
            width: 90%;
            max-width: 450px;
            background: white;
            padding: 30px;
            border-radius: 30px;
            box-shadow: 0 15px 35px rgba(246, 65, 108, 0.15);
            text-align: center;
        }

        .screen { display: none; animation: fadeIn 0.4s ease; }
        .active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; } }

        h1 { color: var(--dark-pink); font-size: 1.8rem; margin-bottom: 5px; }
        .subtitle { font-size: 0.9rem; color: #666; margin-bottom: 20px; }

        textarea, input[type="text"], select {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            border: 2px solid #ffeef2;
            border-radius: 12px;
            box-sizing: border-box;
            outline: none;
            font-family: inherit;
        }

        .btn-primary {
            width: 100%;
            background: var(--dark-pink);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 15px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
            transition: 0.3s;
        }

        .btn-outline {
            background: transparent;
            border: 2px solid var(--dark-pink);
            color: var(--dark-pink);
        }

        .flag-list { text-align: left; max-height: 250px; overflow-y: auto; margin: 20px 0; }
        .flag-item { display: flex; align-items: center; background: #fdf2f4; margin-bottom: 8px; padding: 10px; border-radius: 10px; }

        /* Scanning Animation */
        #loading-bar { width: 0%; height: 5px; background: var(--dark-pink); border-radius: 5px; transition: width 2s; }
        .scanning-container { display: none; margin: 20px 0; }

        #verdict-box { margin-top: 20px; padding: 20px; border-radius: 15px; display: none; }
    </style>
</head>
<body>

<div class="container">
    
    <div id="screen-signin" class="screen active">
        <h1>Welcome Back 💖</h1>
        <p class="subtitle">Log in to check the vibes.</p>
        <input type="text" placeholder="Username">
        <input type="password" style="width:100%; padding:12px; border:2px solid #ffeef2; border-radius:12px;" placeholder="Password">
        <button class="btn-primary" onclick="showScreen('screen-dashboard')">Login</button>
    </div>

    <div id="screen-dashboard" class="screen">
        <h1>Choose Your Tool 🛠️</h1>
        <p class="subtitle">What are we investigating today?</p>
        <button class="btn-primary" onclick="showScreen('screen-test')">🚩 The Flag Quiz</button>
        <button class="btn-primary" onclick="showScreen('screen-upload')">📸 Screenshot Scanner</button>
        <button class="btn-primary" onclick="showScreen('screen-behavior')">✍️ Behavior Analysis</button>
    </div>

    <div id="screen-upload" class="screen">
        <h1>Screenshot Scan 📸</h1>
        <p class="subtitle">Upload a chat to detect manipulation.</p>
        <input type="file" id="fileInput" accept="image/*" style="margin: 20px 0;">
        <button class="btn-primary" onclick="simulateScan('screen-upload')">Analyze Chat</button>
        <button class="btn-primary btn-outline" onclick="showScreen('screen-dashboard')">Back</button>
    </div>

    <div id="screen-behavior" class="screen">
        <h1>Behavior Input ✍️</h1>
        <p class="subtitle">Describe a situation or something they said.</p>
        <textarea id="behaviorText" rows="4" placeholder="Example: He got mad because I didn't answer within 5 minutes..."></textarea>
        <button class="btn-primary" onclick="simulateScan('screen-behavior')">Check Behavior</button>
        <button class="btn-primary btn-outline" onclick="showScreen('screen-dashboard')">Back</button>
    </div>

    <div id="screen-test" class="screen">
        <h1>The Flag Test 🚩</h1>
        <div class="flag-list">
            <label class="flag-item"><input type="checkbox" value="30"> Hides his phone when you walk by</label>
            <label class="flag-item"><input type="checkbox" value="25"> Gets mad when you go out with friends</label>
            <label class="flag-item"><input type="checkbox" value="20"> Says "You're overreacting"</label>
            <label class="flag-item"><input type="checkbox" value="40"> Still has dating apps</label>
            <label class="flag-item"><input type="checkbox" value="50"> Controls what you wear</label>
        </div>
        <button class="btn-primary" onclick="calculateRisk()">GET VERDICT</button>
        <button class="btn-primary btn-outline" onclick="showScreen('screen-dashboard')">Back</button>
    </div>

    <div class="scanning-container" id="scanner">
        <p>🔍 Scanning for toxic patterns...</p>
        <div style="background: #eee; border-radius: 5px;"><div id="loading-bar"></div></div>
    </div>

    <div id="verdict-box">
        <h2 id="verdict-title"></h2>
        <p id="verdict-desc" style="font-size: 0.9rem;"></p>
        <button class="btn-primary" style="background:#2d3436" onclick="resetApp()">Try Another Tool</button>
    </div>

</div>

<script>
    function showScreen(screenId) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById('verdict-box').style.display = 'none';
        document.getElementById(screenId).classList.add('active');
    }

    function simulateScan(currentScreen) {
        // Hide the current inputs
        document.getElementById(currentScreen).classList.remove('active');
        const scanner = document.getElementById('scanner');
        const bar = document.getElementById('loading-bar');
        
        scanner.style.display = 'block';
        setTimeout(() => bar.style.width = '100%', 100);

        // After 2.5 seconds, show result
        setTimeout(() => {
            scanner.style.display = 'none';
            bar.style.width = '0%';
            displayRandomResult();
        }, 2500);
    }

    function displayRandomResult() {
        const box = document.getElementById('verdict-box');
        const title = document.getElementById('verdict-title');
        const desc = document.getElementById('verdict-desc');
        
        box.style.display = "block";
        // Logic: For a real app, this is where you'd call an AI API. 
        // For the prototype, we show a "Red Flag" result.
        box.style.background = "#fff5f5";
        title.innerText = "🚨 RED FLAG DETECTED";
        title.style.color = "#c53030";
        desc.innerText = "Pattern Analysis: We detected signs of Controlling Behavior and Gaslighting. Recommendation: Set firm boundaries.";
    }

    function calculateRisk() {
        const checks = document.querySelectorAll('#screen-test input[type="checkbox"]');
        let score = 0;
        checks.forEach(c => { if(c.checked) score += parseInt(c.value); });
        
        document.getElementById('screen-test').classList.remove('active');
        displayResultByScore(score);
    }

    function displayResultByScore(score) {
        const box = document.getElementById('verdict-box');
        const title = document.getElementById('verdict-title');
        const desc = document.getElementById('verdict-desc');
        box.style.display = "block";

        if (score === 0) {
            box.style.background = "#e6fffa";
            title.innerText = "💚 GREEN FLAG";
            desc.innerText = "Everything looks healthy! Stay happy.";
        } else if (score < 50) {
            box.style.background = "#fffaf0";
            title.innerText = "⚠️ YELLOW FLAG";
            desc.innerText = "Some warning signs detected. Keep an eye out.";
        } else {
            box.style.background = "#fff5f5";
            title.innerText = "🚩 RED FLAG";
            desc.innerText = "High toxicity detected. Protect your peace!";
        }
    }

    function resetApp() {
        location.reload();
    }
</script>

</body>
</html>
