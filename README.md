<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Baccarat VIP++</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial, sans-serif; background:#0d0d0d; color:#fff; text-align:center; }
    h1 { color:#ffd700; }
    button { margin:5px; padding:10px 18px; border:none; border-radius:5px; cursor:pointer; font-size:16px; }
    .player { background:#3498db; color:white; }
    .banker { background:#e74c3c; color:white; }
    .tie { background:#2ecc71; color:white; }
    .reset { background:#7f8c8d; color:white; }
    table { margin:20px auto; border-collapse: collapse; background:#1e1e1e; }
    th, td { border:1px solid #444; padding:5px; text-align:center; }
    th { background:#333; }
    #stats, #suggestion { margin:20px; font-size:18px; }
    .roadmap, .beadplate { display:inline-block; margin:10px; }
    .cell { width:30px; height:30px; border:1px solid #555; display:inline-block; margin:1px; border-radius:50%; }
    .bankerCell { background:#e74c3c; }
    .playerCell { background:#3498db; }
    .tieCell { background:#2ecc71; }
  </style>
</head>
<body>
  <h1>🎲 Baccarat VIP++ Tracker 🎲</h1>

  <div>
    <button class="player" onclick="recordResult('Người chơi')">Người chơi</button>
    <button class="banker" onclick="recordResult('Nhà cái')">Nhà cái</button>
    <button class="tie" onclick="recordResult('Hòa')">Hòa</button>
    <button class="reset" onclick="resetAll()">Reset</button>
  </div>

  <h2>Lịch sử kết quả</h2>
  <table id="historyTable">
    <tr><th>Ván</th><th>Kết quả</th></tr>
  </table>

  <div id="stats"></div>
  <div id="suggestion"></div>

  <h2>📊 Roadmap Baccarat</h2>
  <div class="roadmap" id="bigRoad"></div>
  <div class="beadplate" id="beadPlate"></div>

  <h2>📈 Biểu đồ thống kê</h2>
  <canvas id="pieChart" width="400" height="400"></canvas>

<script>
let history = [];
let stats = { player:0, banker:0, tie:0 };

function saveData(){
  localStorage.setItem("baccaratHistory", JSON.stringify(history));
  localStorage.setItem("baccaratStats", JSON.stringify(stats));
}

function loadData(){
  let h = localStorage.getItem("baccaratHistory");
  let s = localStorage.getItem("baccaratStats");
  if(h) history = JSON.parse(h);
  if(s) stats = JSON.parse(s);
  updateAll();
}

function recordResult(result) {
  history.push(result);
  if (result==="Người chơi") stats.player++;
  else if (result==="Nhà cái") stats.banker++;
  else stats.tie++;
  updateAll();
  saveData();
}

function updateAll(){
  updateTable();
  updateStats();
  updateCharts();
  drawBigRoad();
  drawBeadPlate();
}

function updateTable(){
  let table=document.getElementById("historyTable");
  table.innerHTML="<tr><th>Ván</th><th>Kết quả</th></tr>";
  history.forEach((r,i)=>{
    let row=table.insertRow();
    row.insertCell(0).innerText=i+1;
    row.insertCell(1).innerText=r;
  });
}

function updateStats(){
  let total=history.length;
  let p=(stats.player/total*100).toFixed(1)||0;
  let b=(stats.banker/total*100).toFixed(1)||0;
  let t=(stats.tie/total*100).toFixed(1)||0;
  document.getElementById("stats").innerHTML=
    `Người chơi: ${stats.player} | Nhà cái: ${stats.banker} | Hòa: ${stats.tie}<br>
     Tổng số ván: ${total}<br>
     Tỷ lệ thắng - Người chơi: ${p}% | Nhà cái: ${b}% | Hòa: ${t}%`;
  document.getElementById("suggestion").innerText=suggestNext();
}

function detectPattern(){
  if(history.length<3) return null;
  let last3=history.slice(-3).join("-");
  if(last3==="Nhà cái-Nhà cái-Nhà cái") return "cầu bệt Nhà cái";
  if(last3==="Người chơi-Người chơi-Người chơi") return "cầu bệt Người chơi";
  if(history.slice(-2).every((r,i,a)=>r!==a[(i+1)%a.length])) return "cầu nhảy";
  return null;
}

function suggestNext(){
  if(history.length===0) return "👉 Chưa có dữ liệu để gợi ý";

  let pattern=detectPattern();
  if(pattern) return "👉 Gợi ý: theo " + pattern;

  if(stats.banker>stats.player) return "👉 Gợi ý: Nhà cái (tỷ lệ cao hơn)";
  if(stats.player>stats.banker) return "👉 Gợi ý: Người chơi (tỷ lệ cao hơn)";

  let sim=Math.random();
  if(sim<0.45) return "👉 Gợi ý: Người chơi (ngẫu nhiên)";
  else if(sim<0.9) return "👉 Gợi ý: Nhà cái (ngẫu nhiên)";
  else return "👉 Gợi ý: Hòa (ngẫu nhiên)";
}

function resetAll(){
  history=[]; stats={player:0, banker:0, tie:0};
  updateAll();
  localStorage.removeItem("baccaratHistory");
  localStorage.removeItem("baccaratStats");
}

let pieChart;
function updateCharts(){
  let ctx=document.getElementById("pieChart").getContext("2d");
  if(pieChart) pieChart.destroy();
  pieChart=new Chart(ctx,{type:'pie',
    data:{labels:['Người chơi','Nhà cái','Hòa'],
    datasets:[{data:[stats.player,stats.banker,stats.tie],
    backgroundColor:['#3498db','#e74c3c','#2ecc71']}]}})
}

// Vẽ Big Road (6 hàng, xuống khi cùng kết quả, sang cột khi đổi)
function drawBigRoad(){
  let container=document.getElementById("bigRoad");
  container.innerHTML="";
  let cols=[]; let currentCol=[];
  history.forEach((r,i)=>{
    if(i===0 || r===history[i-1]){
      currentCol.push(r);
    } else {
      cols.push(currentCol);
      currentCol=[r];
    }
  });
  if(currentCol.length) cols.push(currentCol);

  cols.forEach((col,colIndex)=>{
    let div=document.createElement("div");
    div.style.display="inline-block";
    div.style.verticalAlign="top";
    for(let row=0; row<6; row++){
      let cell=document.createElement("div");
      cell.className="cell";
      if(col[row]==="Nhà cái") cell.classList.add("bankerCell");
      if(col[row]==="Người chơi") cell.classList.add("playerCell");
      if(col[row]==="Hòa") cell.classList.add("tieCell");
      div.appendChild(cell);
    }
    container.appendChild(div);
  });
}

// Vẽ Bead Plate (grid 6xN)
function drawBeadPlate(){
  let container=document.getElementById("beadPlate");
  container.innerHTML="";
  let cols=Math.ceil(history.length/6);
  for(let c=0;c<cols;c++){
    let div=document.createElement("div");
    div.style.display="inline-block";
    div.style.verticalAlign="top";
    for(let r=0;r<6;r++){
      let cell=document.createElement("div");
      cell.className="cell";
      let index=c*6+r;
      if(history[index]==="Nhà cái") cell.classList.add("bankerCell");
      if(history[index]==="Người chơi") cell.classList.add("playerCell");
      if(history[index]==="Hòa") cell.classList.add("tieCell");
      div.appendChild(cell);
    }
    container.appendChild(div);
  }
}

window.onload=loadData;
</script>
</body>
</html>
