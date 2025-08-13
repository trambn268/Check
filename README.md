<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Điểm danh GPS</title>
  <style>
    body { font-family: Arial, sans-serif; max-width: 400px; margin: auto; }
    input, button { width: 100%; padding: 10px; margin: 5px 0; }
  </style>
</head>
<body>
  <h2>Điểm danh sinh viên</h2>
  <input type="text" id="name" placeholder="Họ và tên" required>
  <input type="text" id="mssv" placeholder="MSSV" required>
  <input type="text" id="class" placeholder="Lớp" required>
  <input type="text" id="period" placeholder="Tiết học" required>
  <input type="text" id="subject" placeholder="Môn học" required>
  <button onclick="submitForm()">Gửi điểm danh</button>

  <p id="status"></p>

  <script>
    function submitForm() {
      if (!navigator.geolocation) {
        document.getElementById('status').innerText = "Trình duyệt không hỗ trợ GPS.";
        return;
      }

      navigator.geolocation.getCurrentPosition(function(pos) {
        var data = {
          name: document.getElementById('name').value,
          mssv: document.getElementById('mssv').value,
          class: document.getElementById('class').value,
          period: document.getElementById('period').value,
          subject: document.getElementById('subject').value,
          lat: pos.coords.latitude,
          lng: pos.coords.longitude
        };

        fetch(window.location.href, {
          method: 'POST',
          body: JSON.stringify(data)
        }).then(res => res.text())
          .then(txt => {
            document.getElementById('status').innerText = "Điểm danh thành công!";
          });
      }, function(err) {
        document.getElementById('status').innerText = "Không thể lấy vị trí GPS.";
      });
    }
  </script>
</body>
</html>
