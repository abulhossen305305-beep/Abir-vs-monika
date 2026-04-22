<!DOCTYPE html>
<html>
<head>
  <title>Abir Runner</title>
  <style>
    body { margin: 0; overflow: hidden; background: #eee; }
    canvas { display: block; margin: auto; background: white; }
  </style>
</head>
<body>

<canvas id="gameCanvas" width="800" height="300"></canvas>

<script>
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Game states: start, dialogue, countdown, play, gameover
let gameState = "start";

let dialogueIndex = 0;
let countdown = 3;

// 👉 তুমি এখানে নিজের ডায়ালগ বসাবে
let dialogues = [
  "Tap korle start hobe", 
  "Parle amay dhore chude  dekhao bici chara abir 😄",
  "" // পারলে আমাকে ধরে চু*দে দেখাও
];

// 👉 Game Over dialogue
let monikaDialogue = "ami jantam tumi je amay chudte parba na amay ke to hasan a onek valo chude.gay abir";

// Abir
let abir = {
  x: 50,
  y: 200,
  width: 50,
  height: 50,
  dy: 0,
  gravity: 0.8,
  jumpPower: -12,
  grounded: true
};

// Monika
let monika = {
  x: 600,
  y: 200,
  width: 50,
  height: 50,
  speed: 4
};

// Obstacles
let obstacles = [];

function spawnObstacle() {
  if (gameState === "play") {
    obstacles.push({
      x: 800,
      y: 210,
      width: 30,
      height: 40,
      speed: 6
    });
  }
}

setInterval(spawnObstacle, 1400);

// Images
let abirImg = new Image();
abirImg.src = "abir.png";

let monikaImg = new Image();
monikaImg.src = "monika.png";

function update() {
  if (gameState !== "play") return;

  abir.dy += abir.gravity;
  abir.y += abir.dy;

  if (abir.y >= 200) {
    abir.y = 200;
    abir.dy = 0;
    abir.grounded = true;
  }

  monika.x -= monika.speed;
  if (monika.x < 400) {
    monika.x = 700;
  }

  obstacles.forEach((obs, index) => {
    obs.x -= obs.speed;

    if (
      abir.x < obs.x + obs.width &&
      abir.x + abir.width > obs.x &&
      abir.y < obs.y + obs.height &&
      abir.y + abir.height > obs.y
    ) {
      gameState = "gameover";
    }

    if (obs.x < -50) {
      obstacles.splice(index, 1);
    }
  });
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (gameState === "start") {
    ctx.font = "24px Arial";
    ctx.fillText("Tap to Start", 320, 150);

  } else if (gameState === "dialogue") {
    ctx.drawImage(monikaImg, 350, 150, 50, 50);
    ctx.font = "18px Arial";
    ctx.fillText(dialogues[dialogueIndex], 200, 120);

  } else if (gameState === "countdown") {
    ctx.font = "40px Arial";
    ctx.fillText(countdown, 380, 150);

  } else if (gameState === "play") {
    ctx.drawImage(abirImg, abir.x, abir.y, abir.width, abir.height);
    ctx.drawImage(monikaImg, monika.x, monika.y, monika.width, monika.height);

    ctx.fillStyle = "red";
    obstacles.forEach(obs => {
      ctx.fillRect(obs.x, obs.y, obs.width, obs.height);
    });

  } else if (gameState === "gameover") {
    ctx.font = "22px Arial";
    ctx.fillText("Game Over", 330, 80);

    ctx.drawImage(monikaImg, 350, 150, 50, 50);

    ctx.font = "18px Arial";
    ctx.fillText(monikaDialogue, 200, 120);
  }
}

function gameLoop() {
  update();
  draw();
  requestAnimationFrame(gameLoop);
}

// Tap / Click control
document.addEventListener("click", () => {

  if (gameState === "start") {
    gameState = "dialogue";

  } else if (gameState === "dialogue") {
    dialogueIndex++;

    if (dialogueIndex >= dialogues.length) {
      gameState = "countdown";

      let interval = setInterval(() => {
        countdown--;
        if (countdown === 0) {
          clearInterval(interval);
          gameState = "play";
        }
      }, 1000);
    }
  }
});

// Jump
document.addEventListener("keydown", function(e) {
  if (e.code === "Space" && abir.grounded && gameState === "play") {
    abir.dy = abir.jumpPower;
    abir.grounded = false;
  }
});

gameLoop();
</script>

</body>
</html>
