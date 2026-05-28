<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Bundesliga Cup</title>

  <style>
    *{
      margin:0;
      padding:0;
      box-sizing:border-box;
      font-family:Arial, sans-serif;
    }

    body{
      background:#0b5c2d;
      color:white;
      overflow:hidden;
    }

    h1,h2,h3{
      text-align:center;
      margin:10px;
    }

    .screen{
      width:100vw;
      height:100vh;
      display:none;
      flex-direction:column;
      align-items:center;
      justify-content:flex-start;
      overflow:auto;
      padding:20px;
    }

    .active{
      display:flex;
    }

    /* TEAM AUSWAHL */

    .teams{
      width:100%;
      display:grid;
      grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
      gap:15px;
      margin-top:20px;
    }

    .team-card{
      background:#114f2d;
      border:3px solid transparent;
      border-radius:12px;
      padding:15px;
      text-align:center;
      cursor:pointer;
      transition:0.2s;
    }

    .team-card:hover{
      transform:scale(1.05);
      border-color:gold;
    }

    .team-logo{
      font-size:42px;
      margin-bottom:10px;
    }

    .trophy{
      position:absolute;
      top:10px;
      right:20px;
      font-size:40px;
    }

    /* TURNIERBAUM */

    .bracket{
      margin-top:20px;
      width:100%;
      display:flex;
      justify-content:space-around;
      flex-wrap:wrap;
    }

    .round{
      background:#134f2d;
      padding:15px;
      border-radius:12px;
      min-width:180px;
      margin:10px;
    }

    .match{
      background:#1c6d40;
      margin:10px 0;
      padding:10px;
      border-radius:8px;
    }

    .current{
      border:2px solid gold;
    }

    /* SPIEL */

    #gameContainer{
      position:relative;
      width:1000px;
      height:500px;
      background:#2ca34f;
      overflow:hidden;
      border:6px solid white;
      margin-top:10px;
    }

    #fieldLine{
      position:absolute;
      left:50%;
      top:0;
      width:4px;
      height:100%;
      background:white;
    }

    .goal{
      position:absolute;
      width:30px;
      height:140px;
      background:white;
      top:180px;
    }

    #goalLeft{
      left:0;
    }

    #goalRight{
      right:0;
    }

    .player{
      position:absolute;
      width:50px;
      height:80px;
      border-radius:10px;
      bottom:40px;
    }

    #player{
      background:blue;
      left:150px;
    }

    #ai{
      background:red;
      right:150px;
    }

    #ball{
      position:absolute;
      width:35px;
      height:35px;
      border-radius:50%;
      background:white;
      border:3px solid black;
      left:480px;
      top:200px;
    }

    #scoreboard{
      width:1000px;
      display:flex;
      justify-content:space-between;
      font-size:26px;
      margin-top:10px;
    }

    #controls{
      margin-top:10px;
      font-size:18px;
    }

    button{
      padding:10px 20px;
      border:none;
      border-radius:8px;
      background:gold;
      cursor:pointer;
      font-weight:bold;
      margin-top:20px;
    }
  </style>
</head>
<body>

<!-- START -->

<div id="startScreen" class="screen active">
  <div class="trophy" id="trophyDisplay"></div>

  <h1>🏆 Bundesliga Cup</h1>
  <h2>Wähle dein Team</h2>

  <div class="teams" id="teamsContainer"></div>
</div>

<!-- TURNIER -->

<div id="tournamentScreen" class="screen">
  <h1>Turnierbaum</h1>

  <div class="bracket">
    <div class="round">
      <h3>Achtelfinale</h3>
      <div id="round16"></div>
    </div>

    <div class="round">
      <h3>Viertelfinale</h3>
      <div id="quarter"></div>
    </div>

    <div class="round">
      <h3>Halbfinale</h3>
      <div id="semi"></div>
    </div>

    <div class="round">
      <h3>Finale</h3>
      <div id="final"></div>
    </div>
  </div>

  <button onclick="startMatch()">Spiel starten</button>
</div>

<!-- SPIEL -->

<div id="gameScreen" class="screen">
  <h1 id="matchTitle">Spiel</h1>

  <div id="scoreboard">
    <div id="scoreLeft">0</div>
    <div id="timer">90</div>
    <div id="scoreRight">0</div>
  </div>

  <div id="gameContainer">
    <div id="fieldLine"></div>

    <div class="goal" id="goalLeft"></div>
    <div class="goal" id="goalRight"></div>

    <div id="player" class="player"></div>
    <div id="ai" class="player"></div>

    <div id="ball"></div>
  </div>

  <div id="controls">
    ⬅ ➡ = Laufen |
    ⬆ = Springen/Kopfball |
    ⬇ = Schießen
  </div>
</div>

<script>
/* =========================
   TEAMS
========================= */

const teams = [
  {name:"Bayern", logo:"🔴"},
  {name:"Dortmund", logo:"🟡"},
  {name:"Leverkusen", logo:"⚫"},
  {name:"Leipzig", logo:"🔵"},
  {name:"Stuttgart", logo:"⚪"},
  {name:"Frankfurt", logo:"🦅"},
  {name:"Wolfsburg", logo:"🐺"},
  {name:"Freiburg", logo:"⚫"},
  {name:"Union Berlin", logo:"🔴"},
  {name:"Gladbach", logo:"🟢"},
  {name:"Mainz", logo:"🔴"},
  {name:"Bochum", logo:"🔵"},
  {name:"Köln", logo:"🐐"},
  {name:"Augsburg", logo:"🟢"},
  {name:"Bremen", logo:"🟩"},
  {name:"Heidenheim", logo:"🔴"},
  {name:"Hoffenheim", logo:"🔵"},
  {name:"Hamburg", logo:"⚫"}
];

const teamsContainer = document.getElementById("teamsContainer");

let selectedTeam = null;
let currentRound = 0;
let trophies = 0;

let rounds = [
  "Achtelfinale",
  "Viertelfinale",
  "Halbfinale",
  "Finale"
];

teams.forEach(team => {
  const div = document.createElement("div");
  div.className = "team-card";
  div.innerHTML = `
    <div class="team-logo">${team.logo}</div>
    <h3>${team.name}</h3>
  `;

  div.onclick = () => selectTeam(team);

  teamsContainer.appendChild(div);
});

/* =========================
   TURNIER
========================= */

function selectTeam(team){
  selectedTeam = team;
  currentRound = 0;

  showScreen("tournamentScreen");

  renderBracket();
}

function renderBracket(){

  document.getElementById("round16").innerHTML = "";
  document.getElementById("quarter").innerHTML = "";
  document.getElementById("semi").innerHTML = "";
  document.getElementById("final").innerHTML = "";

  const ids = ["round16","quarter","semi","final"];

  rounds.forEach((round, index)=>{

    const container = document.getElementById(ids[index]);

    const match = document.createElement("div");
    match.className = "match";

    if(index === currentRound){
      match.classList.add("current");
    }

    if(index < currentRound){
      match.innerHTML = "✅ Gewonnen";
    } else if(index === currentRound){
      match.innerHTML = `${selectedTeam.name} vs Gegner`;
    } else {
      match.innerHTML = "???";
    }

    container.appendChild(match);
  });
}

/* =========================
   SPIEL
========================= */

const gameContainer = document.getElementById("gameContainer");

const player = document.getElementById("player");
const ai = document.getElementById("ai");
const ball = document.getElementById("ball");

let playerX = 150;
let aiX = 800;

let playerY = 0;
let aiY = 0;

let velocityY = 0;
let aiVelocityY = 0;

let ballX = 480;
let ballY = 200;

let ballVX = 0;
let ballVY = 0;

let keys = {};

let scoreLeft = 0;
let scoreRight = 0;

let gameTimer = 90;

let difficulty = 1;

document.addEventListener("keydown", e=>{
  keys[e.key] = true;
});

document.addEventListener("keyup", e=>{
  keys[e.key] = false;
});

function startMatch(){

  difficulty = currentRound + 1;

  scoreLeft = 0;
  scoreRight = 0;
  gameTimer = 90;

  playerX = 150;
  aiX = 800;

  resetBall();

  document.getElementById("scoreLeft").innerText = scoreLeft;
  document.getElementById("scoreRight").innerText = scoreRight;

  document.getElementById("matchTitle").innerText =
    rounds[currentRound];

  showScreen("gameScreen");

  startGameLoop();

  startTimer();
}

let gameLoop;

function startGameLoop(){

  cancelAnimationFrame(gameLoop);

  function loop(){

    update();
    draw();

    gameLoop = requestAnimationFrame(loop);
  }

  loop();
}

function update(){

  /* SPIELER */

  if(keys["ArrowLeft"]){
    playerX -= 6;
  }

  if(keys["ArrowRight"]){
    playerX += 6;
  }

  if(keys["ArrowUp"] && playerY === 0){
    velocityY = 14;
  }

  if(keys["ArrowDown"]){

    const dx = ballX - playerX;

    if(Math.abs(dx) < 80){
      ballVX = 10;
      ballVY = -5;
    }
  }

  velocityY -= 0.7;
  playerY += velocityY;

  if(playerY < 0){
    playerY = 0;
    velocityY = 0;
  }

  /* KI */

  if(ballX > aiX){
    aiX += 2 + difficulty;
  } else {
    aiX -= 2 + difficulty;
  }

  if(ballY < 220 && aiY === 0){
    aiVelocityY = 12;
  }

  aiVelocityY -= 0.7;
  aiY += aiVelocityY;

  if(aiY < 0){
    aiY = 0;
    aiVelocityY = 0;
  }

  /* KI SCHUSS */

  if(Math.abs(ballX - aiX) < 70){
    ballVX = -7 - difficulty;
    ballVY = -4;
  }

  /* BALL */

  ballVY += 0.4;

  ballX += ballVX;
  ballY += ballVY;

  ballVX *= 0.99;

  if(ballY > 425){
    ballY = 425;
    ballVY *= -0.8;
  }

  /* KOLLISION */

  if(collide(playerX, 380-playerY, ballX, ballY)){
    ballVX = 7;
    ballVY = -6;
  }

  if(collide(aiX, 380-aiY, ballX, ballY)){
    ballVX = -7;
    ballVY = -6;
  }

  /* TORE */

  if(ballX < 20){
    scoreRight++;
    updateScore();
    resetBall();
  }

  if(ballX > 945){
    scoreLeft++;
    updateScore();
    resetBall();
  }

  /* GRENZEN */

  if(playerX < 0) playerX = 0;
  if(playerX > 950) playerX = 950;

  if(aiX < 0) aiX = 0;
  if(aiX > 950) aiX = 950;
}

function collide(px, py, bx, by){

  return (
    bx < px + 60 &&
    bx + 35 > px &&
    by < py + 90 &&
    by + 35 > py
  );
}

function draw(){

  player.style.left = playerX + "px";
  player.style.bottom = (40 + playerY) + "px";

  ai.style.left = aiX + "px";
  ai.style.bottom = (40 + aiY) + "px";

  ball.style.left = ballX + "px";
  ball.style.top = ballY + "px";
}

function resetBall(){

  ballX = 480;
  ballY = 220;

  ballVX = (Math.random()*6)-3;
  ballVY = -2;
}

function updateScore(){

  document.getElementById("scoreLeft").innerText = scoreLeft;
  document.getElementById("scoreRight").innerText = scoreRight;
}

/* =========================
   TIMER
========================= */

let timerInterval;

function startTimer(){

  clearInterval(timerInterval);

  document.getElementById("timer").innerText = gameTimer;

  timerInterval = setInterval(()=>{

    gameTimer--;

    document.getElementById("timer").innerText = gameTimer;

    if(gameTimer <= 0){

      clearInterval(timerInterval);

      endMatch();
    }

  },1000);
}

function endMatch(){

  cancelAnimationFrame(gameLoop);

  if(scoreLeft > scoreRight){

    currentRound++;

    if(currentRound >= rounds.length){

      trophies++;

      document.getElementById("trophyDisplay").innerText =
        "🏆".repeat(trophies);

      alert("DU HAST DAS TURNIER GEWONNEN!");

      showScreen("startScreen");

    } else {

      alert("Gewonnen! Nächste Runde!");

      renderBracket();

      showScreen("tournamentScreen");
    }

  } else {

    alert("Verloren! Du bist ausgeschieden.");

    showScreen("startScreen");
  }
}

/* =========================
   SCREENS
========================= */

function showScreen(id){

  document.querySelectorAll(".screen")
    .forEach(s=>s.classList.remove("active"));

  document.getElementById(id)
    .classList.add("active");
}
</script>

</body>
</html>
