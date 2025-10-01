<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Baccarat VIP Analyzer</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; background: #111; color: #fff; margin: 20px; }
    h1 { color: #f1c40f; }
    button { padding: 12px 20px; margin: 6px; font-size: 16px; border: none; border-radius: 5px; cursor: pointer; }
    .player { background: #3498db; color: white; }
    .banker { background: #e74c3c; color: white; }
    .tie { background: #2ecc71; color: white; }
    .reset { background: #7f8c8d; color: white; }
    table { margin: 20px auto; border-collapse: collapse; width: 85%; background: #222; }
    table, th, td { border: 1px solid #555; padding: 8px; }
    th { background: #333; }
    #suggestion { font-weight: bold; margin-top: 20px; font-size: 20px; color: #f39c12; }
    #confidence { font-size: 16px; color: #1abc9c; }
    canvas { margin-top: 20px; }
  </style>
</head>
<body>
  <h1>🔮 Baccarat VIP Analyzer</h1>

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
  <div id="confidence"></div>

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
                 Tổng số ván: ${total}`;
      document.getElementById("stats").innerText = txt;

      document.getElementById("suggestion").innerText = suggestNext().text;
      document.getElementById("confidence").innerText = "Độ tin cậy: " + suggestNext().confidence + "%";

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
      let total = history.length;
      if (total === 0) return { text: "👉 Chưa có dữ liệu để gợi ý", confidence: 0 };

      let last3 = history.slice(-3);
      let last5 = history.slice(-5);

      // Gợi ý theo chuỗi 3 gần nhất
      if (last3.length === 3) {
        if (last3.every(r => r === "Nhà cái")) return { text: "👉 Gợi ý: Nhà cái (chuỗi 3 gần nhất)", confidence: 85 };
        if (last3.every(r => r === "Người chơi")) return { text: "👉 Gợi ý: Người chơi (chuỗi 3 gần nhất)", confidence: 85 };
      }

      // Gợi ý theo xu hướng 5 ván gần nhất
      if (last5.length === 5) {
        let p = last5.filter(r => r === "Người chơi").length;
        let b = last5.filter(r => r === "Nhà cái").length;
        if (b >= 3) return { text: "👉 Gợi ý: Nhà cái (xu hướng 5 ván gần nhất)", confidence: 75 };
        if (p >= 3) return { text: "👉 Gợi ý: Người chơi (xu hướng 5 ván gần nhất)", confidence: 75 };
      }

      // Nếu không rõ xu hướng → gợi ý theo tỷ lệ tổng thể
      if (stats.banker > stats.player) return { text: "👉 Gợi ý: Nhà cái (tỷ lệ tổng thể)", confidence: 65 };
      if (stats.player > stats.banker) return { text: "👉 Gợi ý: Người chơi (tỷ lệ tổng thể)", confidence: 65 };

      return { text: "👉 Gợi ý: Có thể Hòa hoặc cân bằng", confidence: 50 };
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
