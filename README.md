<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>UGC Game Hub</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    font-family:Arial, sans-serif;
}

body{
    background:#bdefff;
    min-height:100vh;
    text-align:center;
    color:#003b5c;
}

header{
    background:#7dd3fc;
    padding:20px;
    box-shadow:0 2px 10px rgba(0,0,0,0.2);
}

h1{
    font-size:2.5rem;
}

.container{
    display:flex;
    flex-wrap:wrap;
    justify-content:center;
    gap:30px;
    padding:30px;
}

.game-card{
    background:white;
    border-radius:20px;
    padding:20px;
    width:400px;
    box-shadow:0 5px 15px rgba(0,0,0,0.15);
}

button{
    padding:12px 20px;
    margin:8px;
    border:none;
    border-radius:10px;
    background:#38bdf8;
    color:white;
    cursor:pointer;
    font-size:16px;
    transition:.3s;
}

button:hover{
    background:#0ea5e9;
}

.score{
    font-size:20px;
    margin:10px;
    font-weight:bold;
}

#board{
    display:grid;
    grid-template-columns:repeat(3,100px);
    gap:5px;
    justify-content:center;
    margin:20px auto;
}

.cell{
    width:100px;
    height:100px;
    background:#e0f7ff;
    border:2px solid #7dd3fc;
    display:flex;
    align-items:center;
    justify-content:center;
    font-size:40px;
    cursor:pointer;
}

#winnerScreen{
    position:fixed;
    inset:0;
    background:rgba(0,0,0,0.9);
    display:none;
    justify-content:center;
    align-items:center;
    flex-direction:column;
    color:white;
    z-index:999;
}

#winnerScreen h2{
    font-size:4rem;
    margin-bottom:20px;
}

#winnerScreen p{
    font-size:2rem;
    margin-bottom:20px;
}
</style>
</head>

<body>

<header>
<h1> silly lil games</h1>
<p>Win 3 rounds to become the champion!</p>
</header>

<div class="container">

<! Rock Paper Scissors >
<div class="game-card">
<h2>Rock Paper Scissors</h2>

<div class="score">
Wins: <span id="rpsScore">0</span>/3
</div>

<button onclick="playRPS('Rock')">Rock</button>
<button onclick="playRPS('Paper')">Paper</button>
<button onclick="playRPS('Scissors')">Scissors</button>

<p id="rpsResult" style="margin-top:15px;"></p>
</div>

<!-- Tic Tac Toe -->
<div class="game-card">
<h2>Tic Tac Toe</h2>

<div class="score">
Wins: <span id="tttScore">0</span>/3
</div>

<div id="board"></div>

<p id="tttResult"></p>

<button onclick="resetBoard()">New Round</button>
</div>

</div>

<div id="winnerScreen">
<h2>YUPIEE YOU WON SO as a reward ill giv yall my friends locations and insta (make sure to hunt them down😊)
insta :combat.lmao
location:8°35'43.21"N 76°52'15.42"E
insta :shi.yeah
location:8°35'42.23"N 76°52'02.25"E</h2>
<p id="winnerGame"></p>
<button onclick="restartEverything()">Play Again</button>
</div>

<script>

// ===========================
// ROCK PAPER SCISSORS
// ===========================

let rpsWins = 0;

function playRPS(player){

    const choices=["Rock","Paper","Scissors"];
    const computer=choices[Math.floor(Math.random()*3)];

    let result="";

    if(player===computer){
        result="Draw!";
    }
    else if(
        (player==="Rock" && computer==="Scissors") ||
        (player==="Paper" && computer==="Rock") ||
        (player==="Scissors" && computer==="Paper")
    ){
        result="yupieeee you won you retard";
        rpsWins++;
    }
    else{
        result="you fucking stupid retard who lost to a clanker";
    }

    document.getElementById("rpsResult").innerHTML=
        `You: ${player} | Computer: ${computer}<br>${result}`;

    document.getElementById("rpsScore").textContent=rpsWins;

    if(rpsWins>=3){
        showWinner("Rock Paper Scissors Champion!");
    }
}

// ===========================
// TIC TAC TOE
// ===========================

let board=["","","","","","","","",""];
let currentPlayer="X";
let tttWins=0;
let gameActive=true;

const boardElement=document.getElementById("board");

function createBoard(){
    boardElement.innerHTML="";

    board.forEach((cell,index)=>{
        const div=document.createElement("div");
        div.classList.add("cell");
        div.textContent=cell;

        div.addEventListener("click",()=>{
            playerMove(index);
        });

        boardElement.appendChild(div);
    });
}

createBoard();

function playerMove(index){

    if(board[index]!=="" || !gameActive){
        return;
    }

    board[index]="X";
    createBoard();

    if(checkWinner("X")){
        tttWins++;
        document.getElementById("tttScore").textContent=tttWins;
        document.getElementById("tttResult").textContent="You won this round!";
        gameActive=false;

        if(tttWins>=3){
            showWinner("Tic Tac Toe Champion!");
        }

        return;
    }

    if(board.every(cell=>cell!=="")){
        document.getElementById("tttResult").textContent="Draw!";
        gameActive=false;
        return;
    }

    computerMove();
}

function computerMove(){

    let empty=board
        .map((value,index)=> value==="" ? index : null)
        .filter(v=>v!==null);

    let move=empty[Math.floor(Math.random()*empty.length)];

    board[move]="O";
    createBoard();

    if(checkWinner("O")){
        document.getElementById("tttResult").textContent=
        "Computer won this round!";
        gameActive=false;
        return;
    }

    if(board.every(cell=>cell!=="")){
        document.getElementById("tttResult").textContent="Draw!";
        gameActive=false;
    }
}

function checkWinner(player){

    const wins=[
        [0,1,2],
        [3,4,5],
        [6,7,8],
        [0,3,6],
        [1,4,7],
        [2,5,8],
        [0,4,8],
        [2,4,6]
    ];

    return wins.some(combo=>{
        return combo.every(i=>board[i]===player);
    });
}

function resetBoard(){
    board=["","","","","","","","",""];
    gameActive=true;
    document.getElementById("tttResult").textContent="";
    createBoard();
}

// ===========================
// WIN SCREEN
// ===========================

function showWinner(game){
    document.getElementById("winnerScreen").style.display="flex";
    document.getElementById("winnerGame").textContent=game;
}

function restartEverything(){

    rpsWins=0;
    tttWins=0;

    document.getElementById("rpsScore").textContent=0;
    document.getElementById("tttScore").textContent=0;

    document.getElementById("rpsResult").textContent="";
    document.getElementById("tttResult").textContent="";

    resetBoard();

    document.getElementById("winnerScreen").style.display="none";
}

</script>

</body>
</html>
