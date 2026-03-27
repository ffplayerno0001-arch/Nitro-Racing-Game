<!DOCTYPE html>
<html>
<head>
    <title>Cartoon Car Racer</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <style>
        body { margin: 0; background: #333; overflow: hidden; font-family: 'Arial', sans-serif; }
        canvas { display: block; margin: 0 auto; background: #444; border-left: 10px solid white; border-right: 10px solid white; }
        #ui { position: absolute; top: 10px; left: 10px; color: white; font-size: 24px; pointer-events: none; }
    </style>
</head>
<body>
    <div id="ui">Score: 0</div>
    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreElement = document.getElementById("ui");

        // Match your Python Width/Height
        canvas.width = 400;
        canvas.height = 600;

        // Game Variables (From your Python code)
        let player = { x: 175, y: 500, w: 50, h: 80, speed: 5 };
        let enemy = { x: Math.random() * 300 + 50, y: -100, w: 50, h: 80, speed: 7 };
        let score = 0;
        let running = true;
        let moveLeft = false;
        let moveRight = false;

        // Draw a "Cartoon" Car
        function drawCar(x, y, color, isPlayer) {
            // Main Body
            ctx.fillStyle = color;
            ctx.beginPath();
            ctx.roundRect(x, y, 50, 80, 10);
            ctx.fill();

            // Windshield
            ctx.fillStyle = "#87CEEB";
            ctx.fillRect(x + 5, y + 15, 40, 20);

            // Wheels
            ctx.fillStyle = "black";
            ctx.fillRect(x - 5, y + 10, 8, 15); // Top left
            ctx.fillRect(x + 47, y + 10, 8, 15); // Top right
            ctx.fillRect(x - 5, y + 55, 8, 15); // Bottom left
            ctx.fillRect(x + 47, y + 55, 8, 15); // Bottom right

            // Headlights
            ctx.fillStyle = "yellow";
            if(isPlayer) {
                ctx.fillRect(x + 5, y + 5, 10, 5);
                ctx.fillRect(x + 35, y + 5, 10, 5);
            } else {
                ctx.fillRect(x + 5, y + 70, 10, 5);
                ctx.fillRect(x + 35, y + 70, 10, 5);
            }
        }

        function update() {
            if (!running) return;

            // Logic 4: Movement (Keyboard + Touch)
            if (moveLeft && player.x > 50) player.x -= player.speed;
            if (moveRight && player.x < canvas.width - 100) player.x += player.speed;

            // Move Enemy
            enemy.y += enemy.speed;
            if (enemy.y > canvas.height) {
                enemy.y = -80;
                enemy.x = Math.random() * (canvas.width - 100) + 50;
                score++;
                enemy.speed += 0.2; // Make game harder
                scoreElement.innerHTML = "Score: " + score;
            }

            // Logic 5: Collision Detection
            if (player.x < enemy.x + enemy.w &&
                player.x + player.w > enemy.x &&
                player.y < enemy.y + enemy.h &&
                player.y + player.h > enemy.y) {
                alert("GAME OVER! Score: " + score);
                location.reload(); // Restart
                running = false;
            }

            draw();
            requestAnimationFrame(update);
        }

        function draw() {
            // Draw Road
            ctx.fillStyle = "#444";
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Road Lines (Yellow middle line)
            ctx.strokeStyle = "yellow";
            ctx.setLineDash([40, 20]);
            ctx.lineWidth = 10;
            ctx.beginPath();
            ctx.moveTo(canvas.width / 2, 0);
            ctx.lineTo(canvas.width / 2, canvas.height);
            ctx.stroke();

            // Draw Cars
            drawCar(player.x, player.y, "#007bff", true);  // Blue Player
            drawCar(enemy.x, enemy.y, "#ff4444", false); // Red Enemy
        }

        // --- Controls for Mobile & Desktop ---
        window.onkeydown = (e) => {
            if (e.keyCode == 37) moveLeft = true;
            if (e.keyCode == 39) moveRight = true;
        };
        window.onkeyup = (e) => {
            if (e.keyCode == 37) moveLeft = false;
            if (e.keyCode == 39) moveRight = false;
        };

        // Touch controls for your iPhone
        canvas.addEventListener("touchstart", (e) => {
            let touchX = e.touches[0].clientX;
            if (touchX < window.innerWidth / 2) moveLeft = true;
            else moveRight = true;
        });
        canvas.addEventListener("touchend", () => {
            moveLeft = false;
            moveRight = false;
        });

        update();
    </script>
</body>
</html>
