<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Phân tích Baccarat</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; background: #f4f6f9; margin: 20px; }
    h1 { color: #2c3e50; }
    button { padding: 10px 20px; margin: 5px; font-size: 16px; border: none; border-radius: 5px; cursor: pointer; }
    .player { background: #3498db; color: white; }
    .banker { background: #e74c3c; color: white; }
    .tie { background: #2ecc71; color: white; }
    .reset { background: #7f8c8d; color: white; }
    table { margin: 20px auto; border-collapse: collapse; width: 80%; }
    table, th, td { border: 1px solid #ccc; padding: 8px; }
    th { background: #eee; }
    #suggestion { font-weight: bold; margin-top: 20px; font-size: 18px; color: #8e44ad; }
    canvas { margin-top: 20px; }
  </style>
</head>
<body>
  <h1>📊 Phân tích Baccarat</h1>

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

  <h2>Thống kê</h2>
  <p id="stats"></p>
  <div id="suggestion">👉 Gợi ý sẽ hiển thị ở đây</div>

  <canvas id="resultChart" width="400" height="200"></canvas>

  <script>
    let history = JSON.parse(localStorage.getItem("history")) || [];
    let stats = { player: 0, banker: 0, tie: 0 };

    function updateStats() {
      stats = { player: 0, banker: 0, tie: 0 };
      history.forEach(r => {
        if (r === "Người chơi") stats.player++;
        if (r === "Nhà cái") stats.banker++;
        if (r === "Hòa") stats.tie++;
      });

      let total = history.length;
      let txt = `Người chơi: ${stats.player} (${((stats.player/total)*100 || 0).toFixed(1)}%) | 
                 Nhà cái: ${stats.banker} (${((stats.banker/total)*100 || 0).toFixed(1)}%) | 
                 Hòa: ${stats.tie} (${((stats.tie/total)*100 || 0).toFixed(1)}%) | 
                 Tổng: ${total}`;
      document.getElementById("stats").innerText = txt;

      document.getElementById("suggestion").innerText = suggestNext();
      drawChart();
    }

    function recordResult(result) {
      history.push(result);
      localStorage.setItem("history", JSON.stringify(history));
      renderHistory();
      updateStats();
    }

    function renderHistory() {
      let table = document.getElementById("historyTable");
      table.innerHTML = "<tr><th>Ván</th><th>Kết quả</th></tr>";
      history.forEach((r,i) => {
        let row = table.insertRow();
        row.insertCell(0).innerText = i+1;
        row.insertCell(1).innerText = r;
      });
    }

    function suggestNext() {
      if (history.length < 3) return "👉 Chưa đủ dữ liệu để gợi ý";

      let last3 = history.slice(-3);
      if (last3.every(r => r === "Nhà cái")) return "👉 Gợi ý: Nhà cái (theo chuỗi)";
      if (last3.every(r => r === "Người chơi")) return "👉 Gợi ý: Người chơi (theo chuỗi)";

      if (stats.banker > stats.player) return "👉 Gợi ý: Nhà cái (tỷ lệ cao hơn)";
      if (stats.player > stats.banker) return "👉 Gợi ý: Người chơi (tỷ lệ cao hơn)";
      return "👉 Gợi ý: Cân bằng, có thể Hòa";
    }

    function resetAll() {
      history = [];
      localStorage.removeItem("history");
      renderHistory();
      updateStats();
    }

    function drawChart() {
      let ctx = document.getElementById("resultChart").getContext("2d");
      if (window.resultChart) window.resultChart.destroy();
      window.resultChart = new Chart(ctx, {
        type: "pie",
        data: {
          labels: ["Người chơi", "Nhà cái", "Hòa"],
          datasets: [{
            data: [stats.player, stats.banker, stats.tie],
            backgroundColor: ["#3498db", "#e74c3c", "#2ecc71"]
          }]
        }
      });
    }

    renderHistory();
    updateStats();
  </script>
</body>
</html>
