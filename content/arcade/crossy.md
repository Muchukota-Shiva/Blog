---
title: "Crossy road"
description: "the crossy roads chicken wont last a minute on agra roads"
date: 2026-09-30T00:00:00Z
draft: false
---

<div style="text-align: center; font-family: monospace; border: 1px dashed #00ff66; background: #001100; padding: 20px; max-width: 400px; margin: 0 auto;">
  <p style="color: #00ff66; font-weight: bold; margin-top: 0;">[MINI-GAME: SAMOSA_CROSSING.EXE]</p>
  <p id="gameStatus" style="color: #fefe54; font-size: 13px;">Do your duty young one</p>

  <!-- Game Canvas -->
  <canvas id="crossyGame" width="300" height="350" style="background: #000000; border: 2px solid #00ff66; display: block; margin: 15px auto;"></canvas>
  
  <p style="color: #bbbbbb; font-size: 11px; margin-bottom: 15px;">Use Arrow Keys / WASD to Move</p>
  
  <a href="/bizarre/B_samosa_runs/" style="color: #00ff66; text-decoration: underline; font-size: 12px;">&lt;-- Back to the Samosa Story</a>
</div>

<script>
  const canvas = document.getElementById("crossyGame");
  const ctx = canvas.getContext("2d");
  const statusText = document.getElementById("gameStatus");

  const gridSize = 20;
  
  let player = {
    x: 7 * gridSize,
    y: 16 * gridSize,
    size: 14
  };

  let hasSamosa = false;
  let isGameOver = false;

  let lanes = [
    { y: 4 * gridSize, speed: 2, cars: [{x: 50, w: 80}, {x: 200, w: 80}] },
    { y: 7 * gridSize, speed: -2.5, cars: [{x: 0, w: 80}, {x: 180, w: 80}] },
    { y: 10 * gridSize, speed: 3, cars: [{x: 100, w: 80}, {x: 250, w: 80}] },
    { y: 13 * gridSize, speed: -1.5, cars: [{x: 50, w: 80}, {x: 220, w: 80}] }
  ];

  function resetPlayer() {
    player.x = 7 * gridSize;
    player.y = 16 * gridSize;
  }

  function update() {
    if (isGameOver) return;

    lanes.forEach(lane => {
      lane.cars.forEach(car => {
        car.x += lane.speed;
        if (lane.speed > 0 && car.x > canvas.width) {
          car.x = -car.w;
        } else if (lane.speed < 0 && car.x + car.w < 0) {
          car.x = canvas.width;
        }

        if (
          player.x < car.x + car.w &&
          player.x + player.size > car.x &&
          player.y < lane.y + gridSize &&
          player.y + player.size > lane.y
        ) {
          isGameOver = true;
          statusText.innerHTML = "<span style='color: #ff5555;'>well that wasnt how it went, try again</span>";
          setTimeout(restartGame, 2000);
        }
      });
    });

    if (player.y <= 2 * gridSize) {
      if (!hasSamosa) {
        hasSamosa = true;
        statusText.innerHTML = "<span style='color: #00ff66;'>GOT THE SAMOSA! NOW RUN BACK!</span>";
        player.y = 3 * gridSize;
      } else {
        isGameOver = true;
        statusText.innerHTML = "<span style='color: #fefe54;'>MISSION SUCCESS: OILY NEWSPAPER SECURED. 🏆</span>";
        setTimeout(restartGame, 3000);
      }
    }
  }

  function draw() {
    ctx.fillStyle = "#000000";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = hasSamosa ? "#001100" : "#000055";
    ctx.fillRect(0, 0, canvas.width, 3 * gridSize);
    ctx.fillStyle = "#ffffff";
    ctx.font = "10px monospace";
    ctx.fillText(hasSamosa ? "CANTEEN (SECURED)" : "CANTEEN (GET SAMOSA)", 35, 20);

    ctx.fillStyle = "#222222";
    ctx.fillRect(0, 15 * gridSize, canvas.width, 3 * gridSize);

    lanes.forEach(lane => {
      ctx.strokeStyle = "#333333";
      ctx.setLineDash([5, 5]);
      ctx.beginPath();
      ctx.moveTo(0, lane.y);
      ctx.lineTo(canvas.width, lane.y);
      ctx.stroke();
      ctx.setLineDash([]);

      ctx.fillStyle = "#ff5555";
      lane.cars.forEach(car => {
        ctx.fillRect(car.x, lane.y, car.w, gridSize - 4);
      });
    });

    ctx.fillStyle = hasSamosa ? "#fefe54" : "#00ff66";
    ctx.fillRect(player.x, player.y, player.size, player.size);
  }

  function gameLoop() {
    update();
    draw();
    if (!isGameOver) {
      requestAnimationFrame(gameLoop);
    }
  }

  function restartGame() {
    hasSamosa = false;
    isGameOver = false;
    resetPlayer();
    statusText.innerHTML = "OBJECTIVE: Cross the road, grab the samosa, and make it back!";
    gameLoop();
  }

  document.addEventListener("keydown", e => {
    if (isGameOver) return;
    const step = gridSize;
    if (e.key === "ArrowUp" || e.key === "w") { player.y -= step; }
    if (e.key === "ArrowDown" || e.key === "s") { player.y = Math.min(canvas.height - gridSize, player.y + step); }
    if (e.key === "ArrowLeft" || e.key === "a") { player.x = Math.max(0, player.x - step); }
    if (e.key === "ArrowRight" || e.key === "d") { player.x = Math.min(canvas.width - gridSize, player.x + step); }
  });

  gameLoop();
</script>