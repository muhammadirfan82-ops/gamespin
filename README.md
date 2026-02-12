<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spin & Win Deluxe Edition - Score Fixed</title>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Poppins:wght@600&display=swap" rel="stylesheet">
    <style>
        :root {
            --pink: #ff78ae; --yellow: #ffde59; --blue: #5ce1e6;
            --green: #7ed957; --orange: #ff914d; --purple: #8c52ff;
            --dark: #0f0c29;
        }

        body { 
            font-family: 'Poppins', sans-serif; 
            background: radial-gradient(circle, #302b63, #0f0c29);
            color: white; margin: 0; padding: 10px; min-height: 100vh;
            display: flex; flex-direction: column; align-items: center;
        }

        h1, h2, h3, .btn-spin, .btn-start, #score-board { font-family: 'Fredoka One', cursive; }

        /* --- SCORE BOARD --- */
        #score-container { position: fixed; top: 20px; right: 20px; z-index: 100; display: none; }
        #score-board {
            background: linear-gradient(135deg, var(--orange), #ff6b00);
            color: white; padding: 12px 30px; border-radius: 50px;
            border: 4px solid white; font-size: 26px; box-shadow: 0 8px 15px rgba(0,0,0,0.3);
        }

        /* --- SETUP AREA --- */
        #setup-area {
            background: white; color: #333; width: 95%; max-width: 550px;
            padding: 25px; border-radius: 30px; border: 8px solid var(--blue);
            box-shadow: 0 15px 30px rgba(0,0,0,0.5); margin-top: 20px;
        }
        .input-group { background: #f8f9fa; padding: 15px; margin-bottom: 15px; border-radius: 20px; border: 2px solid #eee; }
        input[type="text"] { width: 90%; padding: 10px; margin: 5px 0; border-radius: 10px; border: 2px solid #ddd; }

        /* --- GAME PLAY AREA --- */
        #game-play-area { display: none; width: 100%; text-align: center; padding-top: 20px; }
        #status-msg { height: 50px; font-size: 22px; color: var(--yellow); margin-bottom: 10px; text-shadow: 2px 2px 4px rgba(0,0,0,0.5); }

        /* --- THE WHEEL DELUXE --- */
        #wheel-wrapper { 
            position: relative; width: 380px; height: 380px; margin: 0 auto; 
            background: #222; border-radius: 50%; padding: 15px;
            box-shadow: 0 0 50px rgba(0,0,0,0.5), inset 0 0 20px rgba(255,255,255,0.2);
            border: 10px solid #444;
        }
        
        /* PERBAIKAN: CSS Pointer agar bisa bergerak (animasi) */
        #pointer { 
            position: absolute; top: -20px; left: 50%; transform: translateX(-50%); 
            width: 60px; height: 60px; z-index: 100; filter: drop-shadow(0 5px 5px rgba(0,0,0,0.5));
            transition: transform 0.1s ease; /* Transisi halus */
            transform-origin: center top;    /* Putar dari pangkal atas */
        }
        
        canvas { 
            border-radius: 50%; background: white; 
            box-shadow: 0 0 20px rgba(0,0,0,0.8);
        }

        .dot { position: absolute; width: 8px; height: 8px; background: white; border-radius: 50%; z-index: 5; box-shadow: 0 0 10px white; }

        .btn-spin { 
            background: linear-gradient(to bottom, var(--yellow), #f39c12);
            color: #2c3e50; font-size: 40px; border-radius: 80px; 
            padding: 15px 90px; border: none; border-bottom: 8px solid #d35400; 
            margin-top: 40px; cursor: pointer; transition: 0.2s;
        }
        .btn-spin:active { transform: translateY(5px); border-bottom-width: 2px; }

        .btn-result { display: none; background: var(--purple); color: white; padding: 20px 40px; border-radius: 25px; font-size: 24px; border: none; box-shadow: 0 8px 0 #6a39cc; margin-top: 30px; cursor: pointer; }

        #final-screen {
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(15, 12, 41, 0.98); z-index: 2000; flex-direction: column;
            align-items: center; justify-content: center; text-align: center;
        }

        #popup {
            display: none; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%);
            background: white; color: #333; padding: 30px; border-radius: 40px;
            width: 85%; max-width: 450px; z-index: 1000; border: 12px solid var(--blue); text-align: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.5);
        }
        .preview-img { width: 100%; max-height: 200px; object-fit: contain; border-radius: 20px; margin: 15px 0; background: #f9f9f9; }

        .btn-start { width: 100%; padding: 18px; background: var(--green); color: white; border: none; border-radius: 20px; font-size: 20px; cursor: pointer; box-shadow: 0 6px 0 #4cae27; margin-top: 10px; }
    </style>
</head>
<body>

<div id="score-container">
    <div id="score-board">SKOR: 0</div>
</div>

<div id="setup-area">
    <h2 style="text-align:center; color: var(--purple); margin-bottom: 20px;">🎡 Mesin Soal Pintar</h2>
    <div id="inputs-list"></div>
    <button class="btn-start" onclick="initGame()">SIMPAN & MAIN! 🚀</button>
</div>

<div id="game-play-area">
    <div id="status-msg"></div> 
    <div id="wheel-wrapper">
        <svg id="pointer" viewBox="0 0 100 100">
            <path d="M50 90 L20 20 L80 20 Z" fill="#e74c3c" stroke="white" stroke-width="5"/>
            <circle cx="50" cy="30" r="10" fill="white"/>
        </svg>
        <div id="dots-container"></div>
        <canvas id="wheel" width="380" height="380"></canvas>
    </div>
    <button id="spin-btn" class="btn-spin" onclick="startSpin()">SPIN!</button>
    <button id="result-btn" class="btn-result" onclick="showFinalResult()">HASIL AKHIR 🏆</button>
</div>

<div id="final-screen">
    <h1 style="color: var(--yellow); font-size: 60px;">LUAR BIASA! 🎉</h1>
    <div style="background: white; color: #333; padding: 40px; border-radius: 40px; border: 10px solid var(--pink);">
        <h2 style="margin:0;">TOTAL SKOR:</h2>
        <div id="final-score-val" style="font-size: 100px; color: var(--purple);">0</div>
    </div>
    <div style="display: flex; gap: 20px; margin-top: 40px;">
        <button class="btn-start" style="background: var(--orange); box-shadow: 0 6px 0 #cc743d; padding: 15px 30px;" onclick="mainLagiSama()">MAIN LAGI 🔄</button>
        <button class="btn-start" style="background: var(--purple); box-shadow: 0 6px 0 #6a39cc; padding: 15px 30px;" onclick="gantiSoal()">GANTI SOAL 📝</button>
    </div>
</div>

<div id="popup">
    <h3 id="pop-title" style="color: var(--pink); font-size: 30px; margin-top:0;"></h3>
    <img id="pop-q-img" class="preview-img">
    <p id="pop-q-text" style="font-size: 22px; font-weight: bold;"></p>
    <div id="input-zone">
        <input type="text" id="user-ans" placeholder="Jawabanmu?" style="width:85%; padding:15px; border-radius:15px; font-size:20px; text-align:center; border:3px solid var(--blue);">
        <button class="btn-start" style="background: var(--blue); box-shadow: 0 6px 0 #3ebec3;" onclick="submitAnswer()">CEK JAWABAN ✅</button>
    </div>
    <div id="feedback-zone" style="display:none;">
        <h2 id="fb-status" style="font-size:35px;"></h2>
        <p id="fb-correct-ans" style="font-size: 20px; color:#555;"></p>
        <img id="pop-a-img" class="preview-img">
        <button class="btn-start" style="background: var(--purple); box-shadow: 0 6px 0 #6a39cc;" onclick="closePopup()">LANJUTKAN ➡</button>
    </div>
</div>

<script>
    // --- SOUND ENGINE ---
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    function playNote(freq, type, duration, vol) {
        if (audioCtx.state === 'suspended') audioCtx.resume();
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = type;
        osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
        gain.gain.setValueAtTime(vol, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);
        osc.connect(gain); gain.connect(audioCtx.destination);
        osc.start(); osc.stop(audioCtx.currentTime + duration);
    }

    const sfx = {
        tick: () => playNote(600, 'sine', 0.08, 0.05),
        click: () => playNote(400, 'sine', 0.1, 0.1),
        win: () => {
            playNote(523, 'sine', 0.5, 0.1); 
            setTimeout(() => playNote(659, 'sine', 0.5, 0.1), 100);
            setTimeout(() => playNote(784, 'sine', 0.7, 0.1), 200);
        },
        lose: () => {
            playNote(200, 'sawtooth', 0.3, 0.1);
            setTimeout(() => playNote(150, 'sawtooth', 0.5, 0.1), 200);
        }
    };

    const canvas = document.getElementById('wheel');
    const ctx = canvas.getContext('2d');
    let db = []; let solved = 0; let currentRotation = 0; let isSpinning = false;
    let totalScore = 0;
    const colors = [['#FF595E', '#FF924C'], ['#FFCA3A', '#FFD86E'], ['#8AC926', '#A7DB4F'], ['#1982C4', '#52A6DE'], ['#6A4C93', '#8E72B5']];

    const dotsContainer = document.getElementById('dots-container');
    for(let i=0; i<12; i++) {
        const angle = (i * 30) * Math.PI / 180;
        const x = 190 + 175 * Math.cos(angle) - 4;
        const y = 190 + 175 * Math.sin(angle) - 4;
        dotsContainer.innerHTML += `<div class="dot" style="left:${x}px; top:${y}px"></div>`;
    }

    function makeForm() {
        const list = document.getElementById('inputs-list');
        list.innerHTML = "";
        for(let i=1; i<=5; i++) {
            list.innerHTML += `
                <div class="input-group">
                    <strong>SOAL ${i}</strong><br>
                    <input type="text" placeholder="Pertanyaan" class="in-q">
                    <input type="text" placeholder="Jawaban" class="in-a">
                    <div style="display:flex; gap:5px; margin-top:5px;">
                        <input type="file" class="in-qi" accept="image/*" style="font-size:10px; width:50%">
                        <input type="file" class="in-ai" accept="image/*" style="font-size:10px; width:50%">
                    </div>
                </div>`;
        }
    }
    makeForm();

    async function initGame() {
        sfx.click();
        const groups = document.querySelectorAll('.input-group');
        db = [];
        for (let g of groups) {
            const q = g.querySelector('.in-q').value;
            const a = g.querySelector('.in-a').value;
            if(q && a) {
                const qi = g.querySelector('.in-qi').files[0] ? await new Promise(r => {const fr=new FileReader(); fr.onload=e=>r(e.target.result); fr.readAsDataURL(g.querySelector('.in-qi').files[0])}) : null;
                const ai = g.querySelector('.in-ai').files[0] ? await new Promise(r => {const fr=new FileReader(); fr.onload=e=>r(e.target.result); fr.readAsDataURL(g.querySelector('.in-ai').files[0])}) : null;
                db.push({ id: db.length+1, q, a: a.toLowerCase().trim(), qi, ai, done: false, colors: colors[db.length % colors.length] });
            }
        }
        if(db.length < 1) return alert("Ups! Isi minimal 1 soal ya.");
        startGameSession();
    }

    function startGameSession() {
        document.getElementById('setup-area').style.display = 'none';
        document.getElementById('game-play-area').style.display = 'block';
        document.getElementById('score-container').style.display = 'block';
        document.getElementById('spin-btn').style.display = 'inline-block';
        document.getElementById('result-btn').style.display = 'none';
        drawWheel();
    }

    function drawWheel() {
        const slices = db.length; const arc = (2 * Math.PI) / slices;
        ctx.clearRect(0, 0, 380, 380);
        db.forEach((item, i) => {
            const angle = i * arc;
            const grad = ctx.createRadialGradient(190, 190, 0, 190, 190, 190);
            grad.addColorStop(0, item.done ? '#ccc' : item.colors[1]);
            grad.addColorStop(1, item.done ? '#999' : item.colors[0]);
            ctx.beginPath(); ctx.fillStyle = grad;
            ctx.moveTo(190, 190); ctx.arc(190, 190, 185, angle, angle + arc); ctx.fill();
            ctx.strokeStyle = "rgba(255,255,255,0.5)"; ctx.lineWidth = 4; ctx.stroke();
            ctx.save(); ctx.translate(190, 190); ctx.rotate(angle + arc/2);
            ctx.fillStyle = "white"; ctx.shadowColor = "black"; ctx.shadowBlur = 5;
            ctx.font = "bold 40px 'Fredoka One'"; ctx.textAlign = "right";
            ctx.fillText(item.done ? "✓" : item.id, 160, 15); ctx.restore();
        });
        ctx.beginPath(); ctx.fillStyle = "#444"; ctx.arc(190, 190, 15, 0, Math.PI*2); ctx.fill();
        ctx.strokeStyle = "white"; ctx.lineWidth = 3; ctx.stroke();
    }

    function startSpin() {
        if(isSpinning || solved >= db.length) return;
        
        // PERBAIKAN: Hilangkan pesan saat mulai diputar
        document.getElementById('status-msg').innerText = "";
        
        isSpinning = true;
        sfx.click();
        
        const extraDegree = Math.floor(Math.random() * 360) + 3600; 
        const startRotation = currentRotation;
        const startTimestamp = performance.now();
        const duration = 5000; 
        let lastTickIndex = -1;

        function animate(now) {
            const elapsed = now - startTimestamp;
            const t = Math.min(elapsed / duration, 1);
            const progress = 1 - Math.pow(1 - t, 4);
            currentRotation = startRotation + (extraDegree * progress);
            canvas.style.transform = `rotate(${currentRotation}deg)`;

            const sliceAngle = 360 / db.length;
            const currentTickIndex = Math.floor((currentRotation % 360) / sliceAngle);
            
            if (currentTickIndex !== lastTickIndex) {
                sfx.tick();
                lastTickIndex = currentTickIndex;
                
                // PERBAIKAN: Efek panah (pointer) gerak/goyang
                const pointer = document.getElementById('pointer');
                pointer.style.transform = 'translateX(-50%) rotate(-15deg)';
                setTimeout(() => {
                    pointer.style.transform = 'translateX(-50%) rotate(0deg)';
                }, 50);
            }

            if (t < 1) {
                requestAnimationFrame(animate);
            } else {
                isSpinning = false;
                const actualDeg = currentRotation % 360;
                const winningIndex = Math.floor((360 - actualDeg + 270) % 360 / (360/db.length));
                
                if (db[winningIndex].done) {
                    document.getElementById('status-msg').innerText = "Sudah dijawab! Putar lagi ya...";
                } else {
                    currentIdx = winningIndex;
                    setTimeout(openQuestion, 600);
                }
            }
        }
        requestAnimationFrame(animate);
    }

    function openQuestion() {
        const item = db[currentIdx];
        document.getElementById('pop-title').innerText = "SOAL NOMOR " + item.id;
        document.getElementById('pop-q-text').innerText = item.q;
        document.getElementById('pop-q-img').src = item.qi || '';
        document.getElementById('pop-q-img').style.display = item.qi ? 'block' : 'none';
        document.getElementById('input-zone').style.display = 'block';
        document.getElementById('feedback-zone').style.display = 'none';
        document.getElementById('popup').style.display = 'block';
        document.getElementById('user-ans').value = "";
    }

    function submitAnswer() {
        const uAns = document.getElementById('user-ans').value.toLowerCase().trim();
        const item = db[currentIdx];
        document.getElementById('input-zone').style.display = 'none';
        document.getElementById('feedback-zone').style.display = 'block';
        document.getElementById('pop-a-img').src = item.ai || '';
        document.getElementById('pop-a-img').style.display = item.ai ? 'block' : 'none';

        if(uAns === item.a) {
            sfx.win();
            document.getElementById('fb-status').innerText = "BENAR! 🤩";
            document.getElementById('fb-status').style.color = "#27ae60"; 
            totalScore += 20; 
            document.getElementById('score-board').innerText = "SKOR: " + totalScore;
        } else {
            sfx.lose();
            document.getElementById('fb-status').innerText = "MAAF, SALAH 😅";
            document.getElementById('fb-status').style.color = "#e74c3c";
        }
        document.getElementById('fb-correct-ans').innerText = "Jawaban: " + item.a;
        item.done = true; solved++; drawWheel();
    }

    function closePopup() {
        sfx.click();
        document.getElementById('popup').style.display = 'none';
        if(solved === db.length) {
            document.getElementById('spin-btn').style.display = 'none';
            document.getElementById('result-btn').style.display = 'inline-block';
        }
    }

    function showFinalResult() {
        sfx.win();
        document.getElementById('final-score-val').innerText = totalScore;
        document.getElementById('final-screen').style.display = 'flex';
    }

    function mainLagiSama() {
        sfx.click();
        totalScore = 0; solved = 0;
        db.forEach(item => item.done = false);
        document.getElementById('score-board').innerText = "SKOR: 0";
        document.getElementById('final-screen').style.display = 'none';
        startGameSession();
    }

    function gantiSoal() {
        sfx.click();
        location.reload();
    }
</script>
</body>
</html>
