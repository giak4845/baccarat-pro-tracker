<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Baccarat Pro Tracker</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f9f9f9;
      text-align: center;
      padding: 40px;
    }
    h1 {
      color: #2c3e50;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      margin: 10px;
      cursor: pointer;
      border: none;
      border-radius: 5px;
      color: white;
    }
    .player { background: #27ae60; }
    .banker { background: #c0392b; }
    .tie { background: #8e44ad; }
    .reset { background: #7f8c8d; }
    table {
      margin: 20px auto;
      border-collapse: collapse;
      width: 80%;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 8px;
    }
    th {
      background: #34495e;
      color: white;
    }
    .stats {
      margin-top: 30px;
      font-size: 18px;
      color: #2c3e50;
    }
  </style>
</head>
<body>
  <h1>🎲 Baccarat Pro Tracker 🎲</h1>
  <p>Nhấn nút để ghi lại kết quả ván chơi:</p>

  <button class="player" onclick="addResult('Người chơi')">Người chơi thắng</button>
  <button class="banker" onclick="addResult('Nhà cái')">Nhà cái thắng</button>
  <button class="tie" onclick="addResult('Hòa')">Hòa</button>
  <button class="reset" onclick="resetGame()">Reset</button>

  <table id="resultsTable">
    <tr>
      <th>Ván</th>
      <th>Kết quả</th>
    </tr>
  </table>

  <div class="stats" id="stats">
    <p>Người chơi: 0 | Nhà cái: 0 | Hòa: 0</p>
    <p>Tổng số ván: 0</p>
  </div>

  <script>
    let round = 0;
    let stats = { player: 0, banker: 0, tie: 0 };

    function addResult(result) {
      round++;
      let table = document.getElementById("resultsTable");
      let row = table.insertRow();
      let cell1 = row.insertCell(0);
      let cell2 = row.insertCell(1);
      cell1.innerHTML = round;
      cell2.innerHTML = result;

      if (result === "Người chơi") stats.player++;
      else if (result === "Nhà cái") stats.banker++;
      else stats.tie++;

      updateStats();
    }

    function updateStats() {
      let total = stats.player + stats.banker + stats.tie;
      document.getElementById("stats").innerHTML = `
        <p>Người chơi: ${stats.player} | Nhà cái: ${stats.banker} | Hòa: ${stats.tie}</p>
        <p>Tổng số ván: ${total}</p>
        <p>Tỷ lệ thắng - Người chơi: ${(stats.player/total*100).toFixed(1)}% | 
        Nhà cái: ${(stats.banker/total*100).toFixed(1)}% | 
        Hòa: ${(stats.tie/total*100).toFixed(1)}%</p>
      `;
    }

    function resetGame() {
      round = 0;
      stats = { player: 0, banker: 0, tie: 0 };
      document.getElementById("resultsTable").innerHTML = `
        <tr><th>Ván</th><th>Kết quả</th></tr>
      `;
      updateStats();
    }
  </script>
</body>
</html>
