<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>经典贪吃蛇</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: "Arial", sans-serif;
        }
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            background-color: #f0f0f0;
        }
        .game-container {
            position: relative;
        }
        #gameCanvas {
            border: 3px solid #333;
            border-radius: 8px;
            background-color: #fff;
        }
        .game-info {
            margin-bottom: 15px;
            text-align: center;
        }
        .game-info h1 {
            color: #333;
            margin-bottom: 8px;
        }
        .game-info p {
            font-size: 18px;
            color: #666;
        }
        .control-tips {
            margin-top: 20px;
            font-size: 16px;
            color: #555;
        }
        .restart-btn {
            margin-top: 15px;
            padding: 8px 20px;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        .restart-btn:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <div class="game-info">
            <h1>贪吃蛇小游戏</h1>
            <p>得分: <span id="score">0</span> | 控制: 方向键</p>
        </div>
        <canvas id="gameCanvas" width="400" height="400"></canvas>
        <button class="restart-btn" onclick="restartGame()">重新开始</button>
    </div>
    <div class="control-tips">
        提示：使用键盘方向键控制蛇的移动，吃到食物得分并变长
    </div>

    <script>
        // 游戏基础配置
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const gridSize = 20; // 格子大小
        const tileCount = canvas.width / gridSize; // 格子数量
        let scoreElement = document.getElementById("score");

        // 蛇的状态
        let snake = [
            { x: 10, y: 10 } // 初始位置
        ];
        let direction = { x: 1, y: 0 }; // 初始方向（右）
        let nextDirection = { ...direction }; // 下一个方向（防止反向）
        let food = spawnFood(); // 食物位置
        let score = 0;
        let gameLoop;
        let gameRunning = true;

        // 生成随机食物（不与蛇重叠）
        function spawnFood() {
            let newFood;
            while (!newFood || isOnSnake(newFood)) {
                newFood = {
                    x: Math.floor(Math.random() * tileCount),
                    y: Math.floor(Math.random() * tileCount)
                };
            }
            return newFood;
        }

        // 判断位置是否在蛇身上
        function isOnSnake(position) {
            return snake.some(segment => segment.x === position.x && segment.y === position.y);
        }

        // 更新游戏状态
        function update() {
            // 更新方向（防止180度反向）
            direction = { ...nextDirection };
            
            // 计算新蛇头位置
            const head = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };
            
            // 碰撞检测（边界/自身）
            if (
                head.x < 0 || head.x >= tileCount || 
                head.y < 0 || head.y >= tileCount || 
                isOnSnake(head)
            ) {
                clearInterval(gameLoop);
                gameRunning = false;
                alert(`游戏结束！最终得分: ${score}\n点击"重新开始"继续`);
                return;
            }

            // 添加新蛇头
            snake.unshift(head);

            // 判断是否吃到食物
            if (head.x === food.x && head.y === food.y) {
                score += 10;
                scoreElement.textContent = score;
                food = spawnFood(); // 重新生成食物
            } else {
                snake.pop(); // 没吃到食物则移除蛇尾
            }
        }

        // 绘制游戏画面
        function draw() {
            // 清空画布
            ctx.fillStyle = "#fff";
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 绘制蛇
            ctx.fillStyle = "#4CAF50";
            snake.forEach(segment => {
                ctx.fillRect(
                    segment.x * gridSize, 
                    segment.y * gridSize, 
                    gridSize - 1, // -1 留间隙
                    gridSize - 1
                );
            });

            // 绘制食物
            ctx.fillStyle = "#f44336";
            ctx.fillRect(
                food.x * gridSize, 
                food.y * gridSize, 
                gridSize - 1, 
                gridSize - 1
            );
        }

        // 游戏主循环
        function gameUpdate() {
            if (gameRunning) {
                update();
                draw();
            }
        }

        // 监听方向键控制
        document.addEventListener("keydown", (e) => {
            switch (e.key) {
                case "ArrowUp":
                    if (direction.y !== 1) nextDirection = { x: 0, y: -1 };
                    break;
                case "ArrowDown":
                    if (direction.y !== -1) nextDirection = { x: 0, y: 1 };
                    break;
                case "ArrowLeft":
                    if (direction.x !== 1) nextDirection = { x: -1, y: 0 };
                    break;
                case "ArrowRight":
                    if (direction.x !== -1) nextDirection = { x: 1, y: 0 };
                    break;
            }
        });

        // 重新开始游戏
        function restartGame() {
            snake = [{ x: 10, y: 10 }];
            direction = { x: 1, y: 0 };
            nextDirection = { ...direction };
            food = spawnFood();
            score = 0;
            scoreElement.textContent = score;
            gameRunning = true;
            
            // 清除之前的循环，重新启动
            clearInterval(gameLoop);
            gameLoop = setInterval(gameUpdate, 150); // 150ms 每帧（速度可调整）
        }

        // 初始化游戏
        gameLoop = setInterval(gameUpdate, 150);
    </script>
</body>
</html>![IMG_0501](https://github.com/user-attachments/assets/1b28d331-90c5-4945-a43e-c25d85561d98)
