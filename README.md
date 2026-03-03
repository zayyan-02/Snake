<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ultimate Snake Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            color: white;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            overflow: hidden;
        }

        .game-title {
            font-size: 3rem;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 8px;
            margin-bottom: 10px;
            background: linear-gradient(90deg, #00ff87, #60efff, #00ff87);
            background-size: 200% auto;
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: gradient 3s linear infinite;
            text-shadow: 0 0 30px rgba(0, 255, 135, 0.5);
        }

        @keyframes gradient {
            0%, 100% { background-position: 0% center; }
            50% { background-position: 200% center; }
        }

        .score-container {
            display: flex;
            gap: 40px;
            margin-bottom: 20px;
            font-size: 1.2rem;
        }

        .score-box {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px 30px;
            border-radius: 10px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .score-label {
            font-size: 0.8rem;
            color: #aaa;
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .score-value {
            font-size: 2rem;
            font-weight: bold;
            color: #00ff87;
        }

        #game-container {
            position: relative;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 0 50px rgba(0, 255, 135, 0.3),
                        0 0 100px rgba(0, 255, 135, 0.1),
                        inset 0 0 50px rgba(0, 0, 0, 0.5);
            border: 3px solid rgba(0, 255, 135, 0.5);
        }

        canvas {
            background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
            display: block;
        }

        #ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.85);
            backdrop-filter: blur(5px);
            z-index: 10;
        }

        .ui-title {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 20px;
            text-transform: uppercase;
            letter-spacing: 5px;
        }

        .ui-title.game-over {
            color: #ff4757;
            text-shadow: 0 0 30px rgba(255, 71, 87, 0.8);
        }

        .ui-title.ready {
            color: #00ff87;
            text-shadow: 0 0 30px rgba(0, 255, 135, 0.8);
        }

        .ui-score {
            font-size: 1.5rem;
            margin-bottom: 30px;
            color: #ddd;
        }

        .btn-group {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
            justify-content: center;
        }

        button {
            background: linear-gradient(135deg, #00ff87 0%, #60efff 100%);
            border: none;
            color: #1a1a2e;
            padding: 15px 40px;
            font-size: 1.1rem;
            font-weight: bold;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            border-radius: 50px;
            transition: all 0.3s ease;
            box-shadow: 0 5px 20px rgba(0, 255, 135, 0.4);
        }

        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 30px rgba(0, 255, 135, 0.6);
        }

        button:active {
            transform: translateY(0);
        }

        button.secondary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
        }

        button.secondary:hover {
            box-shadow: 0 8px 30px rgba(102, 126, 234, 0.6);
        }

        .controls-info {
            margin-top: 30px;
            text-align: center;
            color: #888;
            font-size: 0.9rem;
            line-height: 1.8;
        }

        .key {
            display: inline-block;
            background: rgba(255, 255, 255, 0.1);
            padding: 5px 12px;
            border-radius: 5px;
            margin: 0 3px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .pause-indicator {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(255, 255, 255, 0.1);
            padding: 10px 20px;
            border-radius: 20px;
            font-size: 0.9rem;
            display: none;
        }

        .pause-indicator.active {
            display: block;
            background: rgba(255, 193, 7, 0.3);
            border: 1px solid rgba(255, 193, 7, 0.5);
        }

        /* Mobile Controls */
        .mobile-controls {
            display: none;
            margin-top: 20px;
            gap: 10px;
        }

        .mobile-btn {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            font-size: 1.5rem;
            padding: 0;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .mobile-row {
            display: flex;
            justify-content: center;
            gap: 10px;
        }

        @media (max-width: 600px) {
            .game-title {
                font-size: 2rem;
                letter-spacing: 4px;
            }

            .score-container {
                gap: 20px;
            }

            .score-box {
                padding: 10px 20px;
            }

            .score-value {
                font-size: 1.5rem;
            }

            .mobile-controls {
                display: flex;
                flex-direction: column;
            }

            .controls-info {
                display: none;
            }
        }

        /* Particle effects */
        .particle {
            position: absolute;
            border-radius: 50%;
            pointer-events: none;
            animation: particleFade 1s ease-out forwards;
        }

        @keyframes particleFade {
            0% {
                opacity: 1;
                transform: scale(1);
            }
            100% {
                opacity: 0;
                transform: scale(0);
            }
        }

        /* Combo indicator */
        .combo-text {
            position: absolute;
            font-size: 1.5rem;
            font-weight: bold;
            color: #ffd700;
            text-shadow: 0 0 20px rgba(255, 215, 0, 0.8);
            animation: comboFloat 1s ease-out forwards;
            pointer-events: none;
        }

        @keyframes comboFloat {
            0% {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
            100% {
                opacity: 0;
                transform: translateY(-50px) scale(1.5);
            }
        }
    </style>
</head>
<body>
    <h1 class="game-title">🐍 SNAKE 🐍</h1>

    <div class="score-container">
        <div class="score-box">
            <div class="score-label">Score</div>
            <div class="score-value" id="score">0</div>
        </div>
        <div class="score-box">
            <div class="score-label">High Score</div>
            <div class="score-value" id="high-score">0</div>
        </div>
    </div>

    <div id="game-container">
        <canvas id="gameCanvas" width="500" height="500"></canvas>
        
        <div class="pause-indicator" id="pause-indicator">⏸ PAUSED</div>

        <div id="ui-layer">
            <h2 class="ui-title ready" id="game-message">READY?</h2>
            <p class="ui-score" id="ui-score">Press Start to Play</p>
            <div class="btn-group">
                <button onclick="startGame()" id="start-btn">START GAME</button>
                <button class="secondary" onclick="toggleSound()" id="sound-btn">🔊 Sound ON</button>
            </div>
            <div class="controls-info">
                <p><span class="key">↑</span> <span class="key">↓</span> <span class="key">←</span> <span class="key">→</span> to Move</p>
                <p><span class="key">SPACE</span> to Pause | <span class="key">R</span> to Restart</p>
            </div>
        </div>
    </div>

    <!-- Mobile Controls -->
    <div class="mobile-controls">
        <div class="mobile-row">
            <button class="mobile-btn" onclick="mobileControl('up')">⬆</button>
        </div>
        <div class="mobile-row">
            <button class="mobile-btn" onclick="mobileControl('left')">⬅</button>
            <button class="mobile-btn" onclick="mobileControl('down')">⬇</button>
            <button class="mobile-btn" onclick="mobileControl('right')">➡</button>
        </div>
    </div>

    <script>
        // ============================================
        // GAME CONFIGURATION
        // ============================================
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const highScoreElement = document.getElementById('high-score');
        const uiLayer = document.getElementById('ui-layer');
        const gameMessage = document.getElementById('game-message');
        const uiScore = document.getElementById('ui-score');
        const startBtn = document.getElementById('start-btn');
        const soundBtn = document.getElementById('sound-btn');
        const pauseIndicator = document.getElementById('pause-indicator');

        // Game Settings
        const gridSize = 25;
        const tileCount = canvas.width / gridSize;
        const baseSpeed = 100;

        // Game State
        let score = 0;
        let highScore = localStorage.getItem('snakeHighScore') || 0;
        let snake = [];
        let food = {};
        let velocity = { x: 0, y: 0 };
        let nextVelocity = { x: 0, y: 0 };
        let gameInterval = null;
        let isGameRunning = false;
        let isPaused = false;
        let soundEnabled = true;
        let combo = 0;
        let lastFoodTime = 0;

        // Audio Context for sound effects
        let audioCtx = null;

        // Initialize High Score Display
        highScoreElement.innerText = highScore;

        // ============================================
        // SOUND EFFECTS
        // ============================================
        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
        }

        function playSound(type) {
            if (!soundEnabled || !audioCtx) return;

            const oscillator = audioCtx.createOscillator();
            const gainNode = audioCtx.createGain();

            oscillator.connect(gainNode);
            gainNode.connect(audioCtx.destination);

            switch(type) {
                case 'eat':
                    oscillator.frequency.value = 600 + (combo * 50);
                    oscillator.type = 'sine';
                    gainNode.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gainNode.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                    oscillator.start(audioCtx.currentTime);
                    oscillator.stop(audioCtx.currentTime + 0.1);
                    break;
                case 'gameover':
                    oscillator.frequency.value = 200;
                    oscillator.type = 'sawtooth';
                    gainNode.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gainNode.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.5);
                    oscillator.start(audioCtx.currentTime);
                    oscillator.stop(audioCtx.currentTime + 0.5);
                    break;
                case 'start':
                    oscillator.frequency.value = 400;
                    oscillator.type = 'square';
                    gainNode.gain.setValueAtTime(0.2, audioCtx.currentTime);
                    gainNode.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.3);
                    oscillator.start(audioCtx.currentTime);
                    oscillator.stop(audioCtx.currentTime + 0.3);
                    break;
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            soundBtn.innerText = soundEnabled ? '🔊 Sound ON' : '🔇 Sound OFF';
        }

        // ============================================
        // GAME FUNCTIONS
        // ============================================
        function startGame() {
            initAudio();
            
            // Reset game state
            snake = [{ x: 10, y: 10 }];
            velocity = { x: 0, y: 0 };
            nextVelocity = { x: 0, y: 0 };
            score = 0;
            combo = 0;
            isGameRunning = true;
            isPaused = false;
            scoreElement.innerText = score;
            pauseIndicator.classList.remove('active');

            // Hide UI
            uiLayer.style.display = 'none';

            // Place food
            placeFood();

            // Start game loop
            if (gameInterval) clearInterval(gameInterval);
            gameInterval = setInterval(gameLoop, baseSpeed);

            playSound('start');
        }

        function gameLoop() {
            if (!isPaused) {
                update();
                draw();
            }
        }

        function update() {
            // Apply queued velocity
            velocity = { ...nextVelocity };

            // Don't move if no direction
            if (velocity.x === 0 && velocity.y === 0) return;

            // Calculate new head position
            const head = { 
                x: snake[0].x + velocity.x, 
                y: snake[0].y + velocity.y 
            };

            // Wall Collision
            if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
                gameOver();
                return;
            }

            // Self Collision
            for (let i = 0; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    gameOver();
                    return;
                }
            }

            // Move snake
            snake.unshift(head);

            // Food Collision
            if (head.x === food.x && head.y === food.y) {
                const now = Date.now();
                if (now - lastFoodTime < 3000) {
                    combo++;
                    showCombo();
                } else {
                    combo = 0;
                }
                lastFoodTime = now;

                score += 10 + (combo * 5);
                scoreElement.innerText = score;
                
                // Update High Score
                if (score > highScore) {
                    highScore = score;
                    highScoreElement.innerText = highScore;
                    localStorage.setItem('snakeHighScore', highScore);
                }

                createParticles(food.x * gridSize, food.y * gridSize);
                placeFood();
                playSound('eat');
            } else {
                snake.pop();
            }
        }

        function draw() {
            // Clear canvas with gradient
            const gradient = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
            gradient.addColorStop(0, '#0a0a0a');
            gradient.addColorStop(1, '#1a1a1a');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw grid (subtle)
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.03)';
            ctx.lineWidth = 1;
            for (let i = 0; i <= tileCount; i++) {
                ctx.beginPath();
                ctx.moveTo(i * gridSize, 0);
                ctx.lineTo(i * gridSize, canvas.height);
                ctx.stroke();
                ctx.beginPath();
                ctx.moveTo(0, i * gridSize);
                ctx.lineTo(canvas.width, i * gridSize);
                ctx.stroke();
            }

            // Draw food with glow
            ctx.shadowColor = '#ff4757';
            ctx.shadowBlur = 20;
            ctx.fillStyle = '#ff4757';
            ctx.beginPath();
            ctx.arc(
                food.x * gridSize + gridSize/2,
                food.y * gridSize + gridSize/2,
                gridSize/2 - 3,
                0,
                Math.PI * 2
            );
            ctx.fill();
            ctx.shadowBlur = 0;

            // Draw snake with gradient
            for (let i = 0; i < snake.length; i++) {
                const segment = snake[i];
                const isHead = i === 0;

                // Gradient color from head to tail
                const hue = 140 + (i * 2);
                const lightness = 50 - (i * 0.5);
                ctx.fillStyle = `hsl(${hue}, 100%, ${Math.max(lightness, 30)}%)`;

                if (isHead) {
                    ctx.shadowColor = '#00ff87';
                    ctx.shadowBlur = 15;
                }

                // Rounded rectangle for snake segments
                const x = segment.x * gridSize + 1;
                const y = segment.y * gridSize + 1;
                const size = gridSize - 2;
                const radius = isHead ? 8 : 5;

                ctx.beginPath();
                ctx.roundRect(x, y, size, size, radius);
                ctx.fill();

                ctx.shadowBlur = 0;

                // Draw eyes on head
                if (isHead) {
                    ctx.fillStyle = 'white';
                    const eyeSize = 4;
                    const eyeOffset = 6;

                    if (velocity.x === 1) { // Right
                        ctx.beginPath();
                        ctx.arc(x + size - eyeOffset, y + eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.arc(x + size - eyeOffset, y + size - eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (velocity.x === -1) { // Left
                        ctx.beginPath();
                        ctx.arc(x + eyeOffset, y + eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.arc(x + eyeOffset, y + size - eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (velocity.y === -1) { // Up
                        ctx.beginPath();
                        ctx.arc(x + eyeOffset, y + eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.arc(x + size - eyeOffset, y + eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                    } else { // Down or stationary
                        ctx.beginPath();
                        ctx.arc(x + eyeOffset, y + size - eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.arc(x + size - eyeOffset, y + size - eyeOffset, eyeSize, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }
            }
        }

        function placeFood() {
            let valid = false;
            while (!valid) {
                food = {
                    x: Math.floor(Math.random() * tileCount),
                    y: Math.floor(Math.random() * tileCount)
                };

                valid = true;
                for (let part of snake) {
                    if (part.x === food.x && part.y === food.y) {
                        valid = false;
                        break;
                    }
                }
            }
        }

        function gameOver() {
            isGameRunning = false;
            clearInterval(gameInterval);
            playSound('gameover');

            gameMessage.innerText = 'GAME OVER';
            gameMessage.className = 'ui-title game-over';
            uiScore.innerText = `Score: ${score} | High Score: ${highScore}`;
            startBtn.innerText = 'PLAY AGAIN';
            uiLayer.style.display = 'flex';
        }

        function togglePause() {
            if (!isGameRunning || uiLayer.style.display !== 'none') return;
            
            isPaused = !isPaused;
            pauseIndicator.classList.toggle('active');
            
            if (isPaused) {
                gameMessage.innerText = 'PAUSED';
                gameMessage.className = 'ui-title';
                uiScore.innerText = 'Press SPACE to Resume';
                startBtn.innerText = 'RESUME';
                uiLayer.style.display = 'flex';
            } else {
                uiLayer.style.display = 'none';
            }
        }

        // ============================================
        // VISUAL EFFECTS
        // ============================================
        function createParticles(x, y) {
            const container = document.getElementById('game-container');
            const colors = ['#00ff87', '#60efff', '#ffd700', '#ff4757'];

            for (let i = 0; i < 10; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                particle.style.left = (x + Math.random() * gridSize) + 'px';
                particle.style.top = (y + Math.random() * gridSize) + 'px';
                particle.style.width = (Math.random() * 10 + 5) + 'px';
                particle.style.height = particle.style.width;
                particle.style.background = colors[Math.floor(Math.random() * colors.length)];
                container.appendChild(particle);

                setTimeout(() => particle.remove(), 1000);
            }
        }

        function showCombo() {
            if (combo < 2) return;

            const container = document.getElementById('game-container');
            const comboText = document.createElement('div');
            comboText.className = 'combo-text';
            comboText.innerText = `${combo}x COMBO!`;
            comboText.style.left = '50%';
            comboText.style.top = '40%';
            comboText.style.transform = 'translate(-50%, -50%)';
            container.appendChild(comboText);

            setTimeout(() => comboText.remove(), 1000);
        }

        // ============================================
        // INPUT HANDLING
        // ============================================
        document.addEventListener('keydown', (e) => {
            // Prevent default for game keys
            if ([32, 37, 38, 39, 40, 82].includes(e.keyCode)) {
                e.preventDefault();
            }

            // Start game on first key press if waiting
            if (!isGameRunning && uiLayer.style.display !== 'none' && e.keyCode !== 82) {
                // Don't start on pause key
                if (e.keyCode !== 32) {
                    startGame();
                    // Continue to process the direction key
                }
            }

            switch (e.keyCode) {
                case 37: // Left
                    if (velocity.x !== 1) nextVelocity = { x: -1, y: 0 };
                    break;
                case 38: // Up
                    if (velocity.y !== 1) nextVelocity = { x: 0, y: -1 };
                    break;
                case 39: // Right
                    if (velocity.x !== -1) nextVelocity = { x: 1, y: 0 };
                    break;
                case 40: // Down
                    if (velocity.y !== -1) nextVelocity = { x: 0, y: 1 };
                    break;
                case 32: // Space - Pause
                    togglePause();
                    break;
                case 82: // R - Restart
                    if (!isGameRunning || isPaused) {
                        startGame();
                    }
                    break;
            }
        });

        // Mobile controls
        function mobileControl(direction) {
            initAudio();
            
            if (!isGameRunning) {
                startGame();
            }

            switch(direction) {
                case 'up':
                    if (velocity.y !== 1) nextVelocity = { x: 0, y: -1 };
                    break;
                case 'down':
                    if (velocity.y !== -1) nextVelocity = { x: 0, y: 1 };
                    break;
                case 'left':
                    if (velocity.x !== 1) nextVelocity = { x: -1, y: 0 };
                    break;
                case 'right':
                    if (velocity.x !== -1) nextVelocity = { x: 1, y: 0 };
                    break;
            }
        }

        // Touch swipe support
        let touchStartX = 0;
        let touchStartY = 0;

        canvas.addEventListener('touchstart', (e) => {
            touchStartX = e.touches[0].clientX;
            touchStartY = e.touches[0].clientY;
            e.preventDefault();
        }, { passive: false });

        canvas.addEventListener('touchend', (e) => {
            if (!isGameRunning) {
                startGame();
                return;
            }

            const touchEndX = e.changedTouches[0].clientX;
            const touchEndY = e.changedTouches[0].clientY;

            const dx = touchEndX - touchStartX;
            const dy = touchEndY - touchStartY;

            if (Math.abs(dx) > Math.abs(dy)) {
                // Horizontal swipe
                if (dx > 0 && velocity.x !== -1) {
                    nextVelocity = { x: 1, y: 0 };
                } else if (dx < 0 && velocity.x !== 1) {
                    nextVelocity = { x: -1, y: 0 };
                }
            } else {
                // Vertical swipe
                if (dy > 0 && velocity.y !== -1) {
                    nextVelocity = { x: 0, y: 1 };
                } else if (dy < 0 && velocity.y !== 1) {
                    nextVelocity = { x: 0, y: -1 };
                }
            }

            e.preventDefault();
        }, { passive: false });

        // Initial draw
        draw();
    </script>
</body>
</html>

