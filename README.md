<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Adventure Game</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div id="gameArea">
        <h1>Snake Adventure Game</h1>
        <div id="playerName">Created by: Vipin Prajapati</div>
        <canvas id="gameCanvas"></canvas>
        <div id="score">Score: 0</div>
    </div>
    <script src="game.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    background-color: #222;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
    color: #fff;
}

#gameArea {
    text-align: center;
}

#playerName {
    font-size: 16px;
    color: #fff;
    margin-bottom: 10px;
    font-weight: bold;
}

canvas {
    border: 2px solid #fff;
    background-color: #000;
}

#score {
    margin-top: 10px;
    font-size: 20px;
}
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

const scale = 20;
const rows = canvas.height / scale;
const columns = canvas.width / scale;

let snake;
let fruit;
let score = 0;

(function setup() {
    canvas.width = 600;
    canvas.height = 400;
    snake = new Snake();
    fruit = new Fruit();
    window.setInterval(update, 1000 / 10);
})();

function update() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    snake.update();
    snake.draw();
    fruit.draw();

    if (snake.eat(fruit)) {
        score++;
        fruit.randomize();
        document.querySelector("#score").textContent = "Score: " + score;
    }

    if (snake.checkCollision()) {
        alert("Game Over! Your score: " + score);
        snake = new Snake();
        score = 0;
        document.querySelector("#score").textContent = "Score: " + score;
    }
}

window.addEventListener("keydown", e => {
    const direction = e.key.replace("Arrow", "");
    snake.changeDirection(direction);
});

function Snake() {
    this.snakeArray = [{x: 5, y: 5}];
    this.direction = "right";
    this.changeDirection = function(newDirection) {
        if (this.direction === "up" && newDirection === "down" || this.direction === "down" && newDirection === "up" || this.direction === "left" && newDirection === "right" || this.direction === "right" && newDirection === "left") {
            return;
        }
        this.direction = newDirection;
    };

    this.update = function() {
        let head = this.snakeArray[0];

        if (this.direction === "right") head = {x: head.x + 1, y: head.y};
        if (this.direction === "left") head = {x: head.x - 1, y: head.y};
        if (this.direction === "up") head = {x: head.x, y: head.y - 1};
        if (this.direction === "down") head = {x: head.x, y: head.y + 1};

        this.snakeArray.unshift(head);
        this.snakeArray.pop();
    };

    this.draw = function() {
        this.snakeArray.forEach((segment, index) => {
            ctx.fillStyle = index === 0 ? "green" : "white";
            ctx.fillRect(segment.x * scale, segment.y * scale, scale, scale);
        });
    };

    this.eat = function(fruit) {
        let head = this.snakeArray[0];
        if (head.x === fruit.x && head.y === fruit.y) {
            this.snakeArray.push({x: fruit.x, y: fruit.y});
            return true;
        }
        return false;
    };

    this.checkCollision = function() {
        let head = this.snakeArray[0];
        if (head.x < 0 || head.x >= columns || head.y < 0 || head.y >= rows) return true;

        for (let i = 1; i < this.snakeArray.length; i++) {
            if (head.x === this.snakeArray[i].x && head.y === this.snakeArray[i].y) return true;
        }

        return false;
    };
}

function Fruit() {
    this.x;
    this.y;

    this.randomize = function() {
        this.x = Math.floor(Math.random() * columns);
        this.y = Math.floor(Math.random() * rows);
    };

    this.draw = function() {
        ctx.fillStyle = "red";
        ctx.fillRect(this.x * scale, this.y * scale, scale, scale);
    };
}
