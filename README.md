# New-York-Texas-Florida-California-Massachusetts-Tennessee-Virginia
git add .
git commit -m "Describe what you changed"
git push
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pong Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            font-family: 'Arial', sans-serif;
        }

        .container {
            text-align: center;
        }

        h1 {
            color: #fff;
            margin-bottom: 20px;
            text-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
        }

        #gameCanvas {
            display: block;
            background-color: #000;
            border: 3px solid #fff;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);
            margin: 0 auto 20px;
        }

        .scoreboard {
            display: flex;
            justify-content: space-around;
            align-items: center;
            margin-bottom: 20px;
            background-color: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 8px;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
        }

        .score {
            color: #fff;
            font-size: 24px;
            font-weight: bold;
        }

        .score-label {
            color: #aaa;
            font-size: 14px;
            margin-bottom: 5px;
        }

        .divider {
            color: #fff;
            font-size: 32px;
            margin: 0 30px;
        }

        .instructions {
            color: #ddd;
            font-size: 14px;
            max-width: 800px;
            line-height: 1.6;
        }

        .instructions strong {
            color: #fff;
        }

        .reset-btn {
            margin-top: 20px;
            padding: 10px 30px;
            background-color: #ff6b6b;
            color: white;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        .reset-btn:hover {
            background-color: #ff5252;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎮 Pong Game 🎮</h1>
        
        <div class="scoreboard">
            <div>
                <div class="score-label">Player</div>
                <div class="score" id="playerScore">0</div>
            </div>
            <div class="divider">VS</div>
            <div>
                <div class="score-label">Computer</div>
                <div class="score" id="computerScore">0</div>
            </div>
        </div>

        <canvas id="gameCanvas" width="800" height="400"></canvas>

        <div class="instructions">
            <strong>Controls:</strong><br>
            🖱️ Move mouse up/down or use ⬆️⬇️ arrow keys to control the left paddle<br>
            First to 10 points wins!
        </div>

        <button class="reset-btn" onclick="resetGame()">Reset Game</button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Game objects
        const paddleWidth = 10;
        const paddleHeight = 80;
        const ballSize = 8;
        const gameWidth = canvas.width;
        const gameHeight = canvas.height;

        // Player paddle
        const player = {
            x: 20,
            y: gameHeight / 2 - paddleHeight / 2,
            width: paddleWidth,
            height: paddleHeight,
            dy: 0,
            speed: 6
        };

        // Computer paddle
        const computer = {
            x: gameWidth - 20 - paddleWidth,
            y: gameHeight / 2 - paddleHeight / 2,
            width: paddleWidth,
            height: paddleHeight,
            dy: 0,
            speed: 5
        };

        // Ball
        const ball = {
            x: gameWidth / 2,
            y: gameHeight / 2,
            dx: 5,
            dy: 5,
            radius: ballSize,
            speed: 5
        };

        // Scores
        let playerScore = 0;
        let computerScore = 0;

        // Keyboard input
        const keys = {
            ArrowUp: false,
            ArrowDown: false
        };

        // Mouse input
        let mouseY = gameHeight / 2;

        // Event listeners
        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp') keys.ArrowUp = true;
            if (e.key === 'ArrowDown') keys.ArrowDown = true;
        });

        window.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowUp') keys.ArrowUp = false;
            if (e.key === 'ArrowDown') keys.ArrowDown = false;
        });

        canvas.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            mouseY = e.clientY - rect.top;
        });

        // Update player paddle with mouse or keyboard
        function updatePlayer() {
            // Use mouse position
            player.y = mouseY - player.height / 2;

            // Also allow keyboard control
            if (keys.ArrowUp && player.y > 0) {
                player.y -= player.speed;
            }
            if (keys.ArrowDown && player.y < gameHeight - player.height) {
                player.y += player.speed;
            }

            // Keep paddle within bounds
            if (player.y < 0) player.y = 0;
            if (player.y > gameHeight - player.height) player.y = gameHeight - player.height;
        }

        // Update computer paddle with AI
        function updateComputer() {
            const computerCenter = computer.y + computer.height / 2;
            const ballCenter = ball.y;

            // Simple AI: follow the ball
            if (computerCenter < ballCenter - 35) {
                computer.y += computer.speed;
            } else if (computerCenter > ballCenter + 35) {
                computer.y -= computer.speed;
            }

            // Keep paddle within bounds
            if (computer.y < 0) computer.y = 0;
            if (computer.y > gameHeight - computer.height) computer.y = gameHeight - computer.height;
        }

        // Update ball position
        function updateBall() {
            ball.x += ball.dx;
            ball.y += ball.dy;

            // Ball collision with top and bottom walls
            if (ball.y - ball.radius < 0 || ball.y + ball.radius > gameHeight) {
                ball.dy = -ball.dy;
                ball.y = Math.max(ball.radius, Math.min(gameHeight - ball.radius, ball.y));
            }

            // Ball collision with paddles
            if (
                ball.x - ball.radius < player.x + player.width &&
                ball.y > player.y &&
                ball.y < player.y + player.height
            ) {
                ball.dx = -ball.dx;
                ball.x = player.x + player.width + ball.radius;
                // Add spin based on paddle position
                ball.dy += (ball.y - (player.y + player.height / 2)) * 0.1;
            }

            if (
                ball.x + ball.radius > computer.x &&
                ball.y > computer.y &&
                ball.y < computer.y + computer.height
            ) {
                ball.dx = -ball.dx;
                ball.x = computer.x - ball.radius;
                // Add spin based on paddle position
                ball.dy += (ball.y - (computer.y + computer.height / 2)) * 0.1;
            }

            // Scoring - ball goes off screen
            if (ball.x - ball.radius < 0) {
                computerScore++;
                resetBall();
                updateScore();
            }

            if (ball.x + ball.radius > gameWidth) {
                playerScore++;
                resetBall();
                updateScore();
            }
        }

        // Reset ball to center
        function resetBall() {
            ball.x = gameWidth / 2;
            ball.y = gameHeight / 2;
            ball.dx = (Math.random() > 0.5 ? 1 : -1) * 5;
            ball.dy = (Math.random() - 0.5) * 5;
        }

        // Update scoreboard
        function updateScore() {
            document.getElementById('playerScore').textContent = playerScore;
            document.getElementById('computerScore').textContent = computerScore;

            if (playerScore >= 10) {
                alert('🎉 Player Wins! Final Score: ' + playerScore + ' - ' + computerScore);
                resetGame();
            } else if (computerScore >= 10) {
                alert('💻 Computer Wins! Final Score: ' + computerScore + ' - ' + playerScore);
                resetGame();
            }
        }

        // Draw functions
        function drawRect(x, y, width, height, color) {
            ctx.fillStyle = color;
            ctx.fillRect(x, y, width, height);
        }

        function drawCircle(x, y, radius, color) {
            ctx.fillStyle = color;
            ctx.beginPath();
            ctx.arc(x, y, radius, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawCenterLine() {
            ctx.strokeStyle = '#fff';
            ctx.setLineDash([10, 10]);
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(gameWidth / 2, 0);
            ctx.lineTo(gameWidth / 2, gameHeight);
            ctx.stroke();
            ctx.setLineDash([]);
        }

        function draw() {
            // Clear canvas
            drawRect(0, 0, gameWidth, gameHeight, '#000');

            // Draw center line
            drawCenterLine();

            // Draw paddles
            drawRect(player.x, player.y, player.width, player.height, '#00ff00');
            drawRect(computer.x, computer.y, computer.width, computer.height, '#ff00ff');

            // Draw ball
            drawCircle(ball.x, ball.y, ball.radius, '#ffff00');
        }

        // Game loop
        function gameLoop() {
            updatePlayer();
            updateComputer();
            updateBall();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Reset game
        function resetGame() {
            playerScore = 0;
            computerScore = 0;
            updateScore();
            player.y = gameHeight / 2 - player.height / 2;
            computer.y = gameHeight / 2 - computer.height / 2;
            resetBall();
        }

        // Start game
        gameLoop();
    </script>
</body>
</html>
