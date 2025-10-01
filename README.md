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
  </style>
</head>
<body>
  <h1>🎲 Baccarat Pro Tracker 🎲</h1>
  <p>Nhấn nút để ghi lại kết quả ván chơi:</p>

  <button class="player" onclick="addResult('Người chơi')">Người chơi thắng</button>
  <button class="banker" onclick="addResult('Nhà cái')">Nhà cái thắng</button>
  <button class="tie" onclick="addResult('Hòa')">Hòa</button>

  <table id="resultsTable">
    <tr>
      <th>Ván</th>
      <th>Kết quả</th>
    </tr>
  </table>

  <script>
    let round = 0;
    function addResult(result) {
      round++;
      let table = document.getElementById("resultsTable");
      let row = table.insertRow();
      let cell1 = row.insertCell(0);
      let cell2 = row.insertCell(1);
      cell1.innerHTML = round;
      cell2.innerHTML = result;
    }
  </script>
</body>
</html>
