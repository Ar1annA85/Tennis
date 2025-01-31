# Tennis
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tennis Game</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { background-color: #222; text-align: center; }
        canvas { background: linear-gradient(to bottom, #68a, #244); display: block; margin: 20px auto; border: 4px solid white; }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="400"></canvas>
    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const paddleWidth = 10, paddleHeight = 80;
        const ballSize = 10;
        let playerY = (canvas.height - paddleHeight) / 2;
        let aiY = (canvas.height - paddleHeight) / 2;
        let ballX = canvas.width / 2, ballY = canvas.height / 2;
        let ballSpeedX = 5, ballSpeedY = 3;
        let playerScore = 0, aiScore = 0;
        const maxScore = 5;

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

        function drawNet() {
            for (let i = 0; i < canvas.height; i += 20) {
                drawRect(canvas.width / 2 - 1, i, 2, 10, "white");
            }
        }

        function drawText(text, x, y, color) {
            ctx.fillStyle = color;
            ctx.font = "30px Arial";
            ctx.fillText(text, x, y);
        }

        function update() {
            ballX += ballSpeedX;
            ballY += ballSpeedY;

            // Collision con il muro superiore e inferiore
            if (ballY < 0 || ballY > canvas.height - ballSize) ballSpeedY = -ballSpeedY;

            // Collisione con le racchette
            if (ballX < paddleWidth && ballY > playerY && ballY < playerY + paddleHeight) {
                ballSpeedX = -ballSpeedX;
            }
            if (ballX > canvas.width - paddleWidth - ballSize && ballY > aiY && ballY < aiY + paddleHeight) {
                ballSpeedX = -ballSpeedX;
            }

            // Punti
            if (ballX < 0) {
                aiScore++;
                resetBall();
            } else if (ballX > canvas.width) {
                playerScore++;
                resetBall();
            }

            // AI semplice
            if (aiY + paddleHeight / 2 < ballY) aiY += 3;
            else if (aiY + paddleHeight / 2 > ballY) aiY -= 3;
        }

        function resetBall() {
            if (playerScore >= maxScore || aiScore >= maxScore) {
                playerScore = 0;
                aiScore = 0;
            }
            ballX = canvas.width / 2;
            ballY = canvas.height / 2;
            ballSpeedX = -ballSpeedX;
        }

        function draw() {
            drawRect(0, 0, canvas.width, canvas.height, "black");
            drawNet();
            drawRect(0, playerY, paddleWidth, paddleHeight, "white");
            drawRect(canvas.width - paddleWidth, aiY, paddleWidth, paddleHeight, "white");
            drawCircle(ballX, ballY, ballSize, "white");
            drawText(playerScore, canvas.width / 4, 50, "white");
            drawText(aiScore, (canvas.width * 3) / 4, 50, "white");
        }

        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        document.addEventListener("mousemove", (event) => {
            let rect = canvas.getBoundingClientRect();
            playerY = event.clientY - rect.top - paddleHeight / 2;
        });

        gameLoop();
    </script>
</body>
</html>