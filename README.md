<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Baccarat VIP Pro Max</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial, sans-serif; background:#0b0b0b; color:#fff; text-align:center; }
    h1 { color:#ffd700; text-shadow: 0 0 10px #ff0000; }
    button { margin:5px; padding:10px 18px; border:none; border-radius:5px; cursor:pointer; font-size:16px; }
    .player { background:#3498db; color:white; }
    .banker { background:#e74c3c; color:white; }
    .tie { background:#2ecc71; color:white; }
    .reset { background:#7f8c8d; color:white; }
    table { margin:20px auto; border-collapse: collapse; background:#1e1e1e; }
    th, td { border:1px solid #444; padding:5px; text-align:center; }
    th { background:#333; }
    #stats, #suggestion { margin:20px; font-size:18px; }
    .roadmap { display:inline-block; margin:10px; vertical-align:top; }
    .cell { width:22px; height:22px; border:1px solid #444; display:inline-block; margin:1px; border-radius:50%; }
    .bankerCell { background:#e74c3c; }
    .playerCell { background:#3498db; }
    .tieCell { background:#2ecc71; }
    h2 { color:#ffd700; }
    .alert { color:#ff4444; font-weight:bold; font-size:20px; margin:15px; }
  </style>
</head>
<body>
  <h1>🎲 Baccarat VIP Pro Max 🎲</h1>

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
  <div id="alerts" class="alert"></div>

  <h2>📊 Roadmap Baccarat</h2>
  <div class="roadmap" id="bigRoad"></div>
  <div class="roadmap" id="beadPlate"></div>
  <div class="roadmap" id="bigEye"></div>
  <div class="roadmap" id="smallRoad"></div>
  <div class="roadmap" id="cockroachPig"></div>

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
  drawDerived("bigEye",2);
  drawDerived("smallRoad",3);
  drawDerived("cockroachPig",4);
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
  document.getElementById("alerts").innerText=alertPattern();
}

// 🚨 Cảnh báo cầu mạnh
function alertPattern(){
  if(history.length<5) return "";
  let last5=history.slice(-5);
  if(last5.every(r=>r==="Nhà cái")) return "🚨 Cầu bệt Nhà cái!";
  if(last5.every(r=>r==="Người chơi")) return "🚨 Cầu bệt Người chơi!";
  if(last5.every((v,i,a)=>i===0||v!==a[i-1])) return "⚡ Cầu nhảy liên tục!";
  return "";
}

// 📊 Gợi ý xác suất
function suggestNext(){
  if(history.length===0) return "👉 Chưa có dữ liệu để gợi ý";

  let total=history.length;
  let p=stats.player/total;
  let b=stats.banker/total;
  let t=stats.tie/total;

  // Dựa vào cầu gần nhất
  let pattern=detectPattern();
  if(pattern) return `👉 Gợi ý: ${pattern}`;

  // Nếu không có cầu rõ → dựa vào xác suất
  return `👉 Dự đoán: Nhà cái ${(b*100).toFixed(1)}% | Người chơi ${(p*100).toFixed(1)}% | Hòa ${(t*100).toFixed(1)}%`;
}

function detectPattern(){
  if(history.length<3) return null;
  let last3=history.slice(-3).join("-");
  if(last3==="Nhà cái-Nhà cái-Nhà cái") return "Cầu bệt Nhà cái";
  if(last3==="Người chơi-Người chơi-Người chơi") return "Cầu bệt Người chơi";
  if(history.slice(-2)[0]!==history.slice(-2)[1]) return "Cầu nhảy";
  return null;
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

// 🟥 Big Road
function drawBigRoad(){
  let container=document.getElementById("bigRoad");
  container.innerHTML="<h3>Big Road</h3>";
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

  cols.forEach(col=>{
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

// 🟦 Bead Plate
function drawBeadPlate(){
  let container=document.getElementById("beadPlate");
  container.innerHTML="<h3>Bead Plate</h3>";
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

// 🔎 Derived roads (Big Eye, Small Road, Cockroach Pig)
function drawDerived(id,offset){
  let container=document.getElementById(id);
  container.innerHTML=`<h3>${id}</h3>`;
  // Logic đơn giản mô phỏng, có thể nâng cấp thêm
  history.forEach((r,i)=>{
    let cell=document.createElement("div");
    cell.className="cell";
    if(i%offset===0){
      if(r==="Nhà cái") cell.classList.add("bankerCell");
      if(r==="Người chơi") cell.classList.add("playerCell");
    }
    container.appendChild(cell);
  });
}

window.onload=loadData;
</script>
</body>
</html>
