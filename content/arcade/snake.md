---
title: "SNAKE"
description: "One of the games of all time"
date: 2026-09-30T00:00:00Z
draft: false
---

<div style="text-align: center; font-family: monospace;">
  <p style="color: #fefe54;">&gt; INITIALIZING ARCADE MODULE...</p>
  <p style="color: #bbbbbb; font-size: 13px;">Use Arrow Keys to steer. Don't hit the walls.</p>

  <!-- Game Container -->
  <div style="display: inline-block; border: 2px solid #00ff66; background: #001100; padding: 10px; margin-top: 10px;">
    <canvas id="snakeGame" width="300" height="300" style="background: #000000; display: block; margin: 0 auto;"></canvas>
    <p id="scoreBoard" style="color: #00ff66; margin-top: 10px; font-weight: bold;">SCORE: 0</p>
  </div>

  <div style="margin-top: 20px;">
    <a href="/" style="color: #fefe54; text-decoration: underline;">&lt;Return?</a>
  </div>
</div>

<script>
  const canvas = document.getElementById("snakeGame");
  const ctx = canvas.getContext("2d");
  const scoreBoard = document.getElementById("scoreBoard");

  const gridSize = 15;
  const tileCount = canvas.width / gridSize;

  let snake = [{ x: 10, y: 10 }];
  let food = { x: 5, y: 5 };
  let dx = 0;
  let dy = 0;
  let score = 0;
  let gameInterval;

  function resetGame() {
    snake = [{ x: 10, y: 10 }];
    score = 0;
    scoreBoard.innerText = "SCORE: " + score;
    dx = 0;
    dy = 0;
    spawnFood();
  }

  function spawnFood() {
    food.x = Math.floor(Math.random() * tileCount);
    food.y = Math.floor(Math.random() * tileCount);
  }

  function main() {
    update();
    if (isGameOver()) {
      alert("CRITICAL ERROR: CRASHED INTO BOUNDARY. SCORE: " + score);
      resetGame();
      return;
    }
    draw();
  }

  function update() {
    const head = { x: snake[0].x + dx, y: snake[0].y + dy };
    snake.unshift(head);
    if (head.x === food.x && head.y === food.y) {
      score += 10;
      scoreBoard.innerText = "SCORE: " + score;
      spawnFood();
    } else {
      snake.pop();
    }
  }

  function isGameOver() {
    const head = snake[0];
    if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
      return true;
    }
    for (let i = 1; i < snake.length; i++) {
      if (head.x === snake[i].x && head.y === snake[i].y) {
        return true;
      }
    }
    return false;
  }

  function draw() {
    // Clear screen
    ctx.fillStyle = "#000000";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Draw Food (Retro Red/Magenta)
    ctx.fillStyle = "#fe54fe";
    ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize - 2, gridSize - 2);

    // Draw Snake (Retro Green)
    ctx.fillStyle = "#00ff66";
    snake.forEach(part => {
      ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize - 2, gridSize - 2);
    });
  }

  document.addEventListener("keydown", e => {
    if (e.key === "ArrowLeft" && dx === 0) { dx = -1; dy = 0; }
    if (e.key === "ArrowRight" && dx === 0) { dx = 1; dy = 0; }
    if (e.key === "ArrowUp" && dy === 0) { dx = 0; dy = -1; }
    if (e.key === "ArrowDown" && dy === 0) { dx = 0; dy = 1; }
  });

  spawnFood();
  gameInterval = setInterval(main, 100);
</script>