<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
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

/* =========================
   SCREENS
========================= */

.screen{
    width:100vw;
    height:100vh;
    display:none;
    flex-direction:column;
    align-items:center;
    overflow:auto;
    padding:20px;
}

.active{
    display:flex;
}

h1,h2,h3{
    margin:10px;
}

/* =========================
   STARTSCREEN
========================= */

.trophy{
    position:absolute;
    top:15px;
    right:20px;
    font-size:40px;
}

.teams{
    width:100%;
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(180px,1fr));
    gap:15px;
    margin-top:20px;
}

.team-card{
    background:#14592f;
    padding:20px;
    border-radius:12px;
    text-align:center;
    cursor:pointer;
    transition:0.2s;
    border:3px solid transparent;
}

.team-card:hover{
    transform:scale(1.05);
    border-color:gold;
}

.team-logo{
    font-size:42px;
    margin-bottom:10px;
}

/* =========================
   TURNIER
========================= */

.bracket{
    display:flex;
    gap:30px;
    margin-top:30px;
    flex-wrap:wrap;
    justify-content:center;
}

.round{
    background:#14592f;
    padding:20px;
    border-radius:12px;
    width:200px;
}

.match{
    background:#1d7741;
    margin-top:10px;
    padding:12px;
    border-radius:8px;
}

.current{
    border:2px solid gold;
}

button{
    margin-top:30px;
    padding:12px 30px;
    border:none;
    border-radius:10px;
    background:gold;
    color:black;
    font-weight:bold;
    cursor:pointer;
}

/* =========================
   SCOREBOARD
========================= */

#scoreboard{
    width:1000px;
    display:flex;
    justify-content:space-between;
    font-size:30px;
    margin-bottom:10px;
}

/* =========================
   SPIELFELD
========================= */

#gameContainer{
    position:relative;
    width:1000px;
    height:500px;
    overflow:hidden;
    border:6px solid white;

    background:
    repeating-linear-gradient(
        90deg,
        #2fa84f 0px,
        #2fa84f 60px,
        #2c9e49 60px,
        #2c9e49 120px
    );
}

/* Feldlinien */

.field-line{
    position:absolute;
    background:white;
}

#middleLine{
    width:4px;
    height:100%;
    left:50%;
    top:0;
}

#middleCircle{
    position:absolute;
    width:140px;
    height:140px;
    border:4px solid white;
    border-radius:50%;
    left:430px;
    top:180px;
}

/* Tore */

.goal{
    position:absolute;
    width:18px;
    height:170px;
    background:white;
    top:165px;
}

#goalLeft{
    left:-6px;
}

#goalRight{
    right:-6px;
}

/* =========================
   SPIELER
========================= */

.player{
    position:absolute;
    width:60px;
    height:100px;
    bottom:40px;
}

/* Kopf */

.head{
    position:absolute;
    width:26px;
    height:26px;
    background:#ffd2a1;
    border-radius:50%;
    left:17px;
    top:0;
}

/* Körper */

.body{
    position:absolute;
    width:34px;
    height:40px;
    left:13px;
    top:24px;
    border-radius:8px;
}

/* Farben */

#player .body{
    background:#0057ff;
}

#ai .body{
    background:#d90000;
}

/* Arme */

.arm{
    position:absolute;
    width:10px;
    height:30px;
    background:#ffd2a1;
    top:28px;
    border-radius:10px;
    transform-origin:top center;
}

.left-arm{
    left:2px;
}

.right-arm{
    right:2px;
}

/* Beine */

.leg{
    position:absolute;
    width:10px;
    height:38px;
    background:#111;
    top:60px;
    border-radius:10px;
    transform-origin:top center;
}

.left-leg{
    left:16px;
}

.right-leg{
    right:16px;
}

/* =========================
   LAUFANIMATION
========================= */

.running .left-leg{
    animation:runLeg1 0.25s infinite alternate;
}

.running .right-leg{
    animation:runLeg2 0.25s infinite alternate;
}

.running .left-arm{
    animation:runArm1 0.25s infinite alternate;
}

.running .right-arm{
    animation:runArm2 0.25s infinite alternate;
}

@keyframes runLeg1{
    from{transform:rotate(-25deg);}
    to{transform:rotate(25deg);}
}

@keyframes runLeg2{
    from{transform:rotate(25deg);}
    to{transform:rotate(-25deg);}
}

@keyframes runArm1{
    from{transform:rotate(20deg);}
    to{transform:rotate(-20deg);}
}

@keyframes runArm2{
    from{transform:rotate(-20deg);}
    to{transform:rotate(20deg);}
}

/* =========================
   SCHUSSANIMATION
========================= */

.kicking .right-leg{
    animation:kick 0.2s;
}

@keyframes kick{
    0%{transform:rotate(0deg);}
    50%{transform:rotate(-75deg);}
    100%{transform:rotate(0deg);}
}

/* =========================
   BALL
========================= */

#ball{
    position:absolute;
    width:34px;
    height:34px;
    border-radius:50%;
    background:white;
    border:3px solid black;
}

/* =========================
   CONTROLS
========================= */

#controls{
    margin-top:15px;
    font-size:18px;
}

</style>
</head>
<body>

<div id="startScreen" class="screen active">

    <div class="trophy" id="trophyDisplay"></div>

    <h1>🏆 Bundesliga Cup</h1>
    <h2>Wähle dein Team</h2>

    <div class="teams" id="teamsContainer"></div>

</div>

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

<div id="gameScreen" class="screen">

    <h1 id="matchTitle">Spiel</h1>

    <div id="scoreboard">
        <div id="scoreLeft">0</div>
        <div id="timer">90</div>
        <div id="scoreRight">0</div>
    </div>

    <div id="gameContainer">

        <div class="field-line" id="middleLine"></div>
        <div id="middleCircle"></div>

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

const rounds = [
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

function showScreen(id){

    document.querySelectorAll(".screen")
    .forEach(s => s.classList.remove("active"));

    document.getElementById(id)
    .classList.add("active");
}

function selectTeam(team){

    selectedTeam = team;
    currentRound = 0;

    renderBracket();

    showScreen("tournamentScreen");
}

function renderBracket(){

    document.getElementById("round16").innerHTML = "";
    document.getElementById("quarter").innerHTML = "";
    document.getElementById("semi").innerHTML = "";
    document.getElementById("final").innerHTML = "";

    const ids = ["round16","quarter","semi","final"];

    rounds.forEach((round,index)=>{

        const match = document.createElement("div");

        match.className = "match";

        if(index === currentRound){
            match.classList.add("current");
        }

        if(index < currentRound){
            match.innerHTML = "✅ Gewonnen";
        }
        else if(index === currentRound){
            match.innerHTML = `${selectedTeam.name} vs Gegner`;
        }
        else{
            match.innerHTML = "???";
        }

        document.getElementById(ids[index]).appendChild(match);
    });
}

function createHuman(id){

    const p = document.getElementById(id);

    p.innerHTML = `
        <div class="head"></div>
        <div class="body"></div>

        <div class="arm left-arm"></div>
        <div class="arm right-arm"></div>

        <div class="leg left-leg"></div>
        <div class="leg right-leg"></div>
    `;
}

createHuman("player");
createHuman("ai");

const player = document.getElementById("player");
const ai = document.getElementById("ai");
const ball = document.getElementById("ball");

let playerX = 120;
let aiX = 820;

let playerY = 0;
let aiY = 0;

let velocityY = 0;
let aiVelocityY = 0;

let ballX = 480;
let ballY = 220;

let ballVX = 0;
let ballVY = 0;

let scoreLeft = 0;
let scoreRight = 0;

let difficulty = 1;

let gameTimer = 90;

let gameLoop;
let timerInterval;

let keys = {};

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

    playerX = 120;
    aiX = 820;

    playerY = 0;
    aiY = 0;

    resetBall();

    updateScore();

    document.getElementById("matchTitle").innerText =
    rounds[currentRound];

    showScreen("gameScreen");

    startTimer();
    startGameLoop();
}

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

    let moving = false;

    if(keys["ArrowLeft"]){

        playerX -= 5;
        moving = true;
    }

    if(keys["ArrowRight"]){

        playerX += 5;
        moving = true;
    }

    if(moving){
        player.classList.add("running");
    }
    else{
        player.classList.remove("running");
    }

    if(keys["ArrowUp"] && playerY === 0){
        velocityY = 14;
    }

    if(keys["ArrowDown"]){

        if(Math.abs(ballX - playerX) < 90){

            ballVX = 11;
            ballVY = -7;

            player.classList.add("kicking");

            setTimeout(()=>{
                player.classList.remove("kicking");
            },200);
        }
    }

    velocityY -= 0.7;
    playerY += velocityY;

    if(playerY < 0){

        playerY = 0;
        velocityY = 0;
    }

    const aiSpeed = 1 + (difficulty * 0.5);

    if(ballX > aiX + 20){

        aiX += aiSpeed;
        ai.classList.add("running");
    }
    else if(ballX < aiX - 20){

        aiX -= aiSpeed;
        ai.classList.add("running");
    }
    else{
        ai.classList.remove("running");
    }

    if(Math.random() < 0.003 && aiY === 0){
        aiVelocityY = 10;
    }

    aiVelocityY -= 0.7;
    aiY += aiVelocityY;

    if(aiY < 0){

        aiY = 0;
        aiVelocityY = 0;
    }

    if(Math.abs(ballX - aiX) < 60){

        ballVX = -4 - difficulty;
        ballVY = -3;

        ai.classList.add("kicking");

        setTimeout(()=>{
            ai.classList.remove("kicking");
        },200);
    }

    ballVY += 0.35;

    ballX += ballVX;
    ballY += ballVY;

    ballVX *= 0.992;

    if(ballY > 425){

        ballY = 425;
        ballVY *= -0.75;
    }

    if(collide(playerX,360-playerY,ballX,ballY)){

        ballVX = 7;
        ballVY = -5;
    }

    if(collide(aiX,360-aiY,ballX,ballY)){

        ballVX = -5;
        ballVY = -5;
    }

    if(ballX < -10){

        scoreRight++;
        updateScore();

        resetBall();
    }

    if(ballX > 975){

        scoreLeft++;
        updateScore();

        resetBall();
    }

    if(playerX < 0) playerX = 0;
    if(playerX > 940) playerX = 940;

    if(aiX < 0) aiX = 0;
    if(aiX > 940) aiX = 940;
}

function collide(px,py,bx,by){

    return (
        bx < px + 60 &&
        bx + 35 > px &&
        by < py + 100 &&
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

    ballVX = (Math.random() * 4) - 2;
    ballVY = -2;
}

function updateScore(){

    document.getElementById("scoreLeft").innerText = scoreLeft;
    document.getElementById("scoreRight").innerText = scoreRight;
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
        }
        else{

            alert("Gewonnen! Nächste Runde!");

            renderBracket();

            showScreen("tournamentScreen");
        }
    }
    else{

        alert("Verloren! Du bist ausgeschieden.");

        showScreen("startScreen");
    }
}

</script>

</body>
</html>
