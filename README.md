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
            --glass: rgba(255, 255, 255, 0.9);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            background-image: radial-gradient(#ff85a2 0.5px, transparent 0.5px);
            background-size: 20px 20px;
            color: var(--black);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        .container {
            width: 90%;
            max-width: 400px;
            background: white;
            padding: 30px;
            border-radius: 30px;
            box-shadow: 0 15px 35px rgba(246, 65, 108, 0.15);
            text-align: center;
            transition: all 0.5s ease;
        }

        .screen { display: none; animation: fadeIn 0.5s ease; }
        .active { display: block; }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

        h1 { color: var(--dark-pink); margin-bottom: 5px; font-size: 1.8rem; }
        .subtitle { font-size: 0.9rem; color: #666; margin-bottom: 25px; }

        input[type="text"], input[type="password"], input[type="email"], input[type="number"], input[type="date"], select {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            border: 2px solid #ffeef2;
            border-radius: 12px;
            box-sizing: border-box;
            outline: none;
            transition: 0.3s;
        }

        input:focus { border-color: var(--pink); }

        .btn-primary {
            width: 100%;
            background: var(--dark-pink);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 15px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            margin-top: 15px;
            transition: 0.3s;
        }

        .btn-primary:hover { transform: scale(1.02); filter: brightness(1.1); }

        .link-text {
            display: block;
            margin-top: 15px;
            color: var(--dark-pink);
            text-decoration: none;
            font-size: 0.85rem;
            cursor: pointer;
        }

        /* Questionnaire Styles */
        .flag-list { text-align: left; max-height: 300px; overflow-y: auto; padding-right: 5px; margin: 20px 0; }
        .flag-item {
            display: flex; align-items: center;
            background: #fdf2f4; margin-bottom: 10px;
            padding: 12px; border-radius: 12px; cursor: pointer;
        }
        .flag-item input { margin-right: 12px; accent-color: var(--dark-pink); }

        #verdict-box {
            margin-top: 25px; padding: 20px; border-radius: 15px;
            display: none; animation: pop 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        @keyframes pop { 0% { transform: scale(0.5); } 100% { transform: scale(1); } }
    </style>
</head>
<body>

<div class="container">
    
    <div id="screen-signin" class="screen active">
        <h1>Welcome Back 💖</h1>
        <p class="subtitle">Log in to check your man.</p>
        <input type="text" placeholder="Username" id="user">
        <input type="password" placeholder="Password" id="pass">
        <button class="btn-primary" onclick="showScreen('screen-info')">Login</button>
        <span class="link-text" onclick="showScreen('screen-forgot')">Forgot Password?</span>
        <span class="link-text" style="color:#666">Don't have an account? <b>Sign Up</b></span>
    </div>

    <div id="screen-forgot" class="screen">
        <h1>Recover Access</h1>
        <p class="subtitle">Enter your Gmail to receive a 6-digit code.</p>
        <input type="email" placeholder="Enter your Gmail" id="reset-email">
        <button class="btn-primary" onclick="handleForgot()">Send Code</button>
        
        <div id="otp-section" style="display:none; margin-top:20px;">
            <p style="font-size: 0.8rem;">Enter the 6-digit code sent to your mail:</p>
            <input type="number" placeholder="X X X X X X" style="letter-spacing: 5px; text-align: center;">
            <button class="btn-primary" onclick="showScreen('screen-signin')">Verify & Reset</button>
        </div>
        <span class="link-text" onclick="showScreen('screen-signin')">Back to Login</span>
    </div>

    <div id="screen-info" class="screen">
        <h1>The Basics 📝</h1>
        <p class="subtitle">Tell us about you two.</p> 
        <input type="text" placeholder="Your Name">
        <div style="display: flex; gap: 10px;">
            <input type="number" placeholder="Your Age">
            <select><option>Your Zodiac</option><option>Aries</option><option>Leo</option><option>Scorpio</option><option>Other</option></select>
        </div>
        <hr style="border: 1px solid #eee; margin: 15px 0;">
        <input type="text" placeholder="Boyfriend's Name">
        <div style="display: flex; gap: 10px;">
            <input type="number" placeholder="His Age">
            <select><option>His Zodiac</option><option>Aries</option><option>Leo</option><option>Scorpio</option><option>Other</option></select>
        </div>
        <p style="text-align:left; font-size: 0.8rem; margin-bottom:0;">His Date of Birth:</p>
        <input type="date">
        <button class="btn-primary" onclick="showScreen('screen-test')">Next: The Red Flag Test</button>
    </div>

    <div id="screen-test" class="screen">
        <h1>The Verdict 🚩</h1>
        <p class="subtitle">Be honest, girl. Check all that apply:</p>
        
        <div class="flag-list">
            <label class="flag-item"><input type="checkbox" value="10"> He "forgets" to text back for 8 hours</label>
            <label class="flag-item"><input type="checkbox" value="20"> He follows his "crazy" ex on IG</label>
            <label class="flag-item"><input type="checkbox" value="30"> He hides his phone when you walk by</label>
            <label class="flag-item"><input type="checkbox" value="15"> He’s "besties" with a girl he used to like</label>
            <label class="flag-item"><input type="checkbox" value="25"> He gets mad when you go out with friends</label>
            <label class="flag-item"><input type="checkbox" value="20"> He says "You're overreacting" during fights</label>
            <label class="flag-item"><input type="checkbox" value="40"> He still has Tinder "for the memes"</label>
            <label class="flag-item"><input type="checkbox" value="10"> He’s 25+ and still has a "gaming chair" but no job</label>
            <label class="flag-item"><input type="checkbox" value="50"> He tells you what you can and can't wear</label>
            <label class="flag-item"><input type="checkbox" value="20"> He likes every photo of his "cousin" (who isn't his cousin)</label>
        </div>

        <button class="btn-primary" onclick="calculateRisk()">GET THE VERDICT</button>

        <div id="verdict-box">
            <h2 id="verdict-title"></h2>
            <p id="verdict-desc"></p>
            <button class="btn-primary" style="background:#2d3436" onclick="location.reload()">Start Over</button>
        </div>
    </div>

</div>

<script>
    function showScreen(screenId) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(screenId).classList.add('active');
    }

    function handleForgot() {
        const email = document.getElementById('reset-email').value;
        if(email.includes('@')) {
            document.getElementById('otp-section').style.display = 'block';
            alert("Verification code sent to " + email);
        } else {
            alert("Please enter a valid Gmail!");
        }
    }

    function calculateRisk() {
        const checks = document.querySelectorAll('#screen-test input[type="checkbox"]');
        let score = 0;
        checks.forEach(c => { if(c.checked) score += parseInt(c.value); });

        const box = document.getElementById('verdict-box');
        const title = document.getElementById('verdict-title');
        const desc = document.getElementById('verdict-desc');
        
        box.style.display = "block";
        window.scrollTo(0, document.body.scrollHeight);

        if (score === 0) {
            box.style.background = "#e6fffa";
            title.innerText = "🏆 PURE GREEN FLAG";
            title.style.color = "#2c7a7b";
            desc.innerText = "He is literally a fictional character. Marry him.";
        } else if (score < 40) {
            box.style.background = "#fffaf0";
            title.innerText = "⚠️ YELLOW ZONE";
            title.style.color = "#975a16";
            desc.innerText = "He's okay, but keep your eyes open. Communication is key!";
        } else if (score < 80) {
            box.style.background = "#fff5f5";
            title.innerText = "🚨 RED FLAG DETECTED";
            title.style.color = "#c53030";
            desc.innerText = "Girl, he’s toxic. You deserve better than this stress!";
        } else {
            box.style.background = "#742a2a";
            title.innerText = "☢️ NUCLEAR BIOHAZARD";
            title.style.color = "#fff";
            desc.style.color = "#fff";
            desc.innerText = "BLOCK HIM. DELETE HIS NUMBER. CHANGE YOUR IDENTITY. RUN!";
        }
    }
</script>

</body>
</html>
