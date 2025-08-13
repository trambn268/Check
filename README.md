<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Điểm danh sinh viên</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 400px;
      margin: 20px auto;
      padding: 20px;
      border: 1px solid #ddd;
      border-radius: 10px;
      text-align: center;
    }
    img {
      max-width: 120px;
      margin-bottom: 15px;
    }
    input, button {
      width: 100%;
      padding: 10px;
      margin: 5px 0;
      font-size: 16px;
    }
    button {
      background-color: #28a745;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #218838;
    }
    #status {
      margin-top: 15px;
      color: blue;
    }
  </style>
</head>
<body>
  <!-- Logo -->
  <img src="https://upload.wikimedia.org/wikipedia/commons/4/4a/Logo_2013_Google.png" alt="Logo trường">

  <h2>Điểm danh sinh viên</h2>

  <input type="text" id="tenSV" placeholder="Nhập họ tên" required>
  <button onclick="diemDanh()">Điểm danh</button>

  <p id="status"></p>

  <script>
    function diemDanh() {
      const tenSV = document.getElementById('tenSV').value.trim();
      if (!tenSV) {
        alert('Vui lòng nhập họ tên');
        return;
      }

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(function(position) {
          const lat = position.coords.latitude;
          const lng = position.coords.longitude;

          google.script.run
            .withSuccessHandler(function(message) {
              document.getElementById('status').innerText = message;
            })
            .ghiDiemDanh(tenSV, lat, lng);

        }, function() {
          alert('Không thể lấy vị trí GPS');
        });
      } else {
        alert('Trình duyệt không hỗ trợ GPS');
      }
    }
  </script>
</body>
</html>
