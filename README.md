<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Car Dodge Game</title>
  <style>
    * {
      box-sizing: border-box;
    }

    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      background: #111;
      font-family: sans-serif;
      color: white;
    }

    h1, #scoreboard {
      text-align: center;
      margin: 10px 0;
    }

    #gameArea {
      position: relative;
      width: 100%;
      max-width: 400px;
      height: 70vh;
      margin: 0 auto;
      background: #333;
      border: 4px solid white;
      overflow: hidden;
      touch-action: none;
    }

    #car {
      position: absolute;
      bottom: 5%;
      width: 12%;
      aspect-ratio: 1/2;
      background: red;
      border-radius: 5px;
      left: 44%;
      transition: left 0.05s linear;
    }

    .obstacle {
      position: absolute;
      top: -20%;
      width: 10%;
      aspect-ratio: 1/2;
      background: yellow;
      border-radius: 5px;
    }

    .controls {
      display: flex;
      justify-content: center;
      max-width: 400px;
      margin: 15px auto 0 auto;
      gap: 15px;
    }

    .btn {
      flex: 1;
      padding: 20px;
      font-size: 20px;
      background: #555;
      color: white;
      border: none;
      border-radius: 10px;
    }

    .btn:active {
      background: #777;
    }
  </style>
</head>
<body>

<h1>Car Dodge Game</h1>
<div id="scoreboard">
  Punkte: <span id="score">0</span> | Highscore: <span id="highscore">0</span>
</div>

<div id="gameArea">
  <div id="car"></div>
</div>

<div class="controls">
  <button class="btn" id="leftBtn">← Links</button>
  <button class="btn" id="rightBtn">Rechts →</button>
</div>

<script>
  const car = document.getElementById("car");
  const gameArea = document.getElementById("gameArea");
  const scoreEl = document.getElementById("score");
  const highscoreEl = document.getElementById("highscore");
  const leftBtn = document.getElementById("leftBtn");
  const rightBtn = document.getElementById("rightBtn");

  let carPosPercent = 44;
  let gameRunning = true;
  let score = 0;
  let moveLeft = false;
  let moveRight = false;
  let speed = 2; // obstacle speed
  let interval = 1000;

  const highscore = localStorage.getItem("highscore") || 0;
  highscoreEl.innerText = highscore;

  function updateCarPosition() {
    if (!gameRunning) return;
    if (moveLeft && carPosPercent > 0) {
      carPosPercent -= 0.7;
    }
    if (moveRight && carPosPercent < 88) {
      carPosPercent += 0.7;
    }
    car.style.left = `${carPosPercent}%`;
  }

  setInterval(updateCarPosition, 16);

  document.addEventListener("keydown", (e) => {
    if (e.key === "ArrowLeft") moveLeft = true;
    if (e.key === "ArrowRight") moveRight = true;
  });

  document.addEventListener("keyup", (e) => {
    if (e.key === "ArrowLeft") moveLeft = false;
    if (e.key === "ArrowRight") moveRight = false;
  });

  leftBtn.addEventListener("touchstart", () => moveLeft = true);
  rightBtn.addEventListener("touchstart", () => moveRight = true);
  leftBtn.addEventListener("touchend", () => moveLeft = false);
  rightBtn.addEventListener("touchend", () => moveRight = false);

  function createObstacle() {
    const obs = document.createElement("div");
    obs.classList.add("obstacle");
    obs.style.left = Math.floor(Math.random() * 10) * 10 + "%";
    gameArea.appendChild(obs);

    let top = -20;
    const move = setInterval(() => {
      if (!gameRunning) return clearInterval(move);

      top += speed;
      obs.style.top = top + "%";

      const carBox = car.getBoundingClientRect();
      const obsBox = obs.getBoundingClientRect();

      if (
        carBox.left < obsBox.left + obsBox.width &&
        carBox.right > obsBox.left &&
        carBox.top < obsBox.top + obsBox.height &&
        carBox.bottom > obsBox.top
      ) {
        gameOver();
        clearInterval(move);
      }

      if (top > 120) {
        obs.remove();
        clearInterval(move);
      }
    }, 30);
  }

  function startScore() {
    return setInterval(() => {
      if (!gameRunning) return;
      score++;
      scoreEl.innerText = score;
    }, 1000);
  }

  function startObstacles() {
    return setInterval(createObstacle, interval);
  }

  function speedUp() {
    return setInterval(() => {
      if (interval > 400) interval -= 100;
      speed += 0.3;
      clearInterval(obstacleInterval);
      obstacleInterval = startObstacles();
    }, 10000);
  }

  function gameOver() {
    gameRunning = false;
    if (score > highscore) {
      localStorage.setItem("highscore", score);
    }
    alert("Game Over! Punkte: " + score);
    location.reload();
  }

  const scoreInterval = startScore();
  let obstacleInterval = startObstacles();
  const speedUpInterval = speedUp();
</script>

</body>
</html>
