#   
<!doctype html>  
<html lang="ja">  
<head>  
<meta charset="utf-8">  
<meta name="viewport" content="width=device-width,initial-scale=1">  
<title>ゴブレットゴブラーズ（CPU対戦対応）</title>  
<style>  
body{  
  margin:0;  
  font-family:sans-serif;  
  background:#f4f6ff;  
  display:flex;  
  flex-direction:column;  
  align-items:center;  
}  
h1{ margin-top:20px; }  
  
.controls{  
  margin:10px;  
}  
button{  
  padding:8px 12px;  
  margin:5px;  
  font-weight:bold;  
  border-radius:8px;  
  cursor:pointer;  
}  
  
.board{  
  width:300px;  
  height:300px;  
  display:grid;  
  grid-template-columns:repeat(3,1fr);  
  grid-template-rows:repeat(3,1fr);  
  gap:5px;  
}  
  
.cell{  
  background:#fff;  
  border:2px solid #333;  
  display:flex;  
  align-items:center;  
  justify-content:center;  
  font-size:40px;  
  cursor:pointer;  
}  
  
.status{  
  margin:15px;  
  font-weight:bold;  
  font-size:18px;  
}  
</style>  
</head>  
<body>  
  
<h1>ゴブレットゴブラーズ</h1>  
  
<div class="controls">  
  <button id="vsHuman">👥 対人</button>  
  <button id="vsCPU">🤖 対CPU</button>  
  <button onclick="resetGame()">🔄 リセット</button>  
</div>  
  
<div class="status" id="status"></div>  
  
<div class="board" id="board"></div>  
  
<script>  
let board = Array(9).fill(null);  
let currentPlayer = "blue";  
let gameMode = "human";  
let cpuPlayer = "orange";  
let gameOver = false;  
  
const statusDiv = document.getElementById("status");  
const boardDiv = document.getElementById("board");  
  
function render(){  
  boardDiv.innerHTML="";  
  board.forEach((cell,i)=>{  
    const div=document.createElement("div");  
    div.className="cell";  
    div.textContent = cell==="blue"?"🔵":cell==="orange"?"🟠":"";  
    div.onclick=()=>handleClick(i);  
    boardDiv.appendChild(div);  
  });  
  
  if(!gameOver){  
    statusDiv.textContent =  
      currentPlayer==="blue"?"🔵 青の番":"🟠 オレンジの番";  
  }  
}  
  
function handleClick(index){  
  if(gameOver) return;  
  if(board[index]!==null) return;  
  
  board[index]=currentPlayer;  
  
  if(checkWinner(currentPlayer)){  
    statusDiv.textContent=  
      currentPlayer==="blue"?"🔵 青の勝ち！":"🟠 オレンジの勝ち！";  
    gameOver=true;  
    render();  
    return;  
  }  
  
  if(board.every(c=>c!==null)){  
    statusDiv.textContent="引き分け！";  
    gameOver=true;  
    render();  
    return;  
  }  
  
  currentPlayer = currentPlayer==="blue"?"orange":"blue";  
  render();  
  
  if(gameMode==="cpu" && currentPlayer===cpuPlayer){  
    setTimeout(cpuMove,500);  
  }  
}  
  
function checkWinner(player){  
  const wins=[  
    [0,1,2],[3,4,5],[6,7,8],  
    [0,3,6],[1,4,7],[2,5,8],  
    [0,4,8],[2,4,6]  
  ];  
  return wins.some(combo =>  
    combo.every(i=>board[i]===player)  
  );  
}  
  
function cpuMove(){  
  if(gameOver) return;  
  
  const empty=board  
    .map((c,i)=>c===null?i:null)  
    .filter(i=>i!==null);  
  
  // 勝てるなら勝つ  
  for(let i of empty){  
    board[i]=cpuPlayer;  
    if(checkWinner(cpuPlayer)){  
      board[i]=null;  
      handleClick(i);  
      return;  
    }  
    board[i]=null;  
  }  
  
  // 相手を止める  
  const opponent="blue";  
  for(let i of empty){  
    board[i]=opponent;  
    if(checkWinner(opponent)){  
      board[i]=null;  
      handleClick(i);  
      return;  
    }  
    board[i]=null;  
  }  
  
  // ランダム  
  const r=empty[Math.floor(Math.random()*empty.length)];  
  handleClick(r);  
}  
  
function resetGame(){  
  board=Array(9).fill(null);  
  currentPlayer="blue";  
  gameOver=false;  
  render();  
}  
  
document.getElementById("vsHuman").onclick=()=>{  
  gameMode="human";  
  resetGame();  
};  
  
document.getElementById("vsCPU").onclick=()=>{  
  gameMode="cpu";  
  resetGame();  
};  
  
render();  
</script>  
  
</body>  
</html>  
