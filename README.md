<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Baccarat VIP Tracker</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; background:#101820; color:#fff; }
    h1 { color:#ffd700; }
    button { margin:5px; padding:10px 20px; border:none; border-radius:5px; cursor:pointer; font-size:16px; }
    .player { background:#3498db; color:white; }
    .banker { background:#e74c3c; color:white; }
    .tie { background:#2ecc71; color:white; }
    .reset { background:#7f8c8d; color:white; }
    table { margin:20px auto; border-collapse: collapse; width:80%; background:#1e272e; }
    th, td { border:1px solid #555; padding:6px; text-align:center; }
    th { background:#273c75; }
    #stats, #suggestion { margin:20px; font-size:18px; }
    canvas { background:#fff; margin:20px auto; padding:10px; border-radius:10px; }
  </style>
</head>
<body>
  <h1>🎲 Baccarat VIP Tracker 🎲</h1>
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

  <h2>📊 Biểu đồ phân tích</h2>
  <canvas id="pieChart" width="400" height="400"></canvas>
  <canvas id="lineChart" width="600" height="300"></canvas>

<script>
let history = [];
let stats = { player:0, banker:0, tie:0 };

function recordResult(result) {
  history.push(result);
  if (result==="Người chơi") stats.player++;
  else if (result==="Nhà cái") stats.banker++;
  else stats.tie++;
  updateTable();
  updateStats();
  updateCharts();
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

  // Nếu không có cầu thì dựa vào thống kê
  if(stats.banker>stats.player) return "👉 Gợi ý: Nhà cái (tỷ lệ cao hơn)";
  if(stats.player>stats.banker) return "👉 Gợi ý: Người chơi (tỷ lệ cao hơn)";

  // Nếu hòa thì random giả lập
  let sim=Math.random();
  if(sim<0.45) return "👉 Gợi ý: Người chơi (ngẫu nhiên)";
  else if(sim<0.9) return "👉 Gợi ý: Nhà cái (ngẫu nhiên)";
  else return "👉 Gợi ý: Hòa (ngẫu nhiên)";
}

function resetAll(){
  history=[]; stats={player:0, banker:0, tie:0};
  updateTable(); updateStats(); updateCharts();
}

let pieChart, lineChart;
function updateCharts(){
  let ctx1=document.getElementById("pieChart").getContext("2d");
  let ctx2=document.getElementById("lineChart").getContext("2d");
  if(pieChart) pieChart.destroy();
  if(lineChart) lineChart.destroy();

  pieChart=new Chart(ctx1,{type:'pie',
    data:{labels:['Người chơi','Nhà cái','Hòa'],
    datasets:[{data:[stats.player,stats.banker,stats.tie],
    backgroundColor:['#3498db','#e74c3c','#2ecc71']}]}});

  lineChart=new Chart(ctx2,{type:'line',
    data:{labels:history.map((_,i)=>i+1),
    datasets:[{label:'Người chơi',data:history.map(r=>r==="Người chơi"?1:0),
              borderColor:'#3498db',fill:false},
             {label:'Nhà cái',data:history.map(r=>r==="Nhà cái"?1:0),
              borderColor:'#e74c3c',fill:false},
             {label:'Hòa',data:history.map(r=>r==="Hòa"?1:0),
              borderColor:'#2ecc71',fill:false}]},
    options:{scales:{y:{beginAtZero:true,max:1}}}});
}
</script>
</body>
</html>
