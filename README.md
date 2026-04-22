<!DOCTYPE html>
<html>
<head>
  <title>Abir Dino Runner</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: #eee;
      touch-action: none;
    }
    canvas {
      display: block;
      width: 100vw;
      height: 100vh;
      background: white;
    }
  </style>
</head>
<body>

<canvas id="gameCanvas"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// 📱 screen fit
function resize() {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
}
resize();
window.addEventListener("resize", resize);

// 🎮 GAME STATE
let started = false;
let gameOver = false;

// 🗣️ 👉 ডায়ালগ লেখার আলাদা জায়গা (এখানেই লিখবে)
let abirDialogue = "monika amay aktu chudte daw";
let monikaDialogue = "tui chudte paros na. ami hasan ar kase gelam ";

// 🧍 Abir
let abir = {
  x: 60,
  y: 0,
  w: 40,
  h: 40,
  dy: 0,
  gravity: 0.8,
  jump: -14,
  grounded: false
};

// ground
function groundY() {
  return canvas.height - 120;
}

// 🧕 Monika (safe runner)
let monika = {
  x: 120,
  y: 0,
  w: 40,
  h: 40,
  speed: 4
};

// 🌵 obstacles
let obstacles = [];

function spawnObstacle() {
  if (started && !gameOver) {
    obstacles.push({
      x: canvas.width,
      y: groundY(),
      w: 30,
      h: 50,
      speed: 6
    });
  }
}
setInterval(spawnObstacle, 1400);

// 🎮 update
function update() {
  if (!started || gameOver) return;

  // Abir physics
  abir.dy += abir.gravity;
  abir.y += abir.dy;

  if (abir.y >= groundY()) {
    abir.y = groundY();
    abir.dy = 0;
    abir.grounded = true;
  }

  // Monika auto run (safe)
  monika.x += monika.speed;
  if (monika.x > canvas.width) monika.x = 100;

  // obstacles
  obstacles.forEach((o, i) => {
    o.x -= o.speed;

    // collision
    if (
      abir.x < o.x + o.w &&
      abir.x + abir.w > o.x &&
      abir.y < o.y + o.h &&
      abir.y + abir.h > o.y
    ) {
      gameOver = true;

      // 👉 এখানে game over dialogue
      monikaDialogue = "👉 hasan tor theke onek valo chude. tor bicci nai";
      abirDialogue = "👉 amamr ta choto ta ami jani.but aktu chusar jonno daw";
    }

    if (o.x < -50) obstacles.splice(i, 1);
  });
}

// 🎨 draw
function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // ground
  ctx.fillStyle = "black";
  ctx.fillRect(0, groundY() + 40, canvas.width, 2);

  // start screen
  if (!started) {
    ctx.fillStyle = "black";
    ctx.font = "22px Arial";
    ctx.fillText("Tap Anywhere to Start", canvas.width/2 - 120, canvas.height/2);
    return;
  }

  // Abir
  ctx.fillStyle = "blue";
  ctx.fillRect(abir.x, abir.y, abir.w, abir.h);

  // Monika
  ctx.fillStyle = "hotpink";
  ctx.fillRect(monika.x, monika.y, monika.w, monika.h);

  // obstacles
  ctx.fillStyle = "green";
  obstacles.forEach(o => {
    ctx.fillRect(o.x, o.y, o.w, o.h);
  });

  // 🗣️ dialogue box (game over)
  if (gameOver) {
    ctx.fillStyle = "red";
    ctx.font = "28px Arial";
    ctx.fillText("Game Over", canvas.width/2 - 80, canvas.height/2 - 40);

    ctx.fillStyle = "black";
    ctx.font = "16px Arial";

    // Monika dialogue
    ctx.fillText(monikaDialogue, canvas.width/2 - 120, canvas.height/2);

    // Abir dialogue
    ctx.fillText(abirDialogue, canvas.width/2 - 120, canvas.height/2 + 30);
  }
}

// loop
function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}
loop();

// 📱 TAP ANYWHERE = START + JUMP
document.addEventListener("touchstart", () => {
  if (!started) started = true;

  if (!gameOver && abir.grounded) {
    abir.dy = abir.jump;
    abir.grounded = false;
  }
});

document.addEventListener("click", () => {
  if (!started) started = true;

  if (!gameOver && abir.grounded) {
    abir.dy = abir.jump;
    abir.grounded = false;
  }
});
</script>

</body>
</html>
