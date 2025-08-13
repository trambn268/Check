<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Điểm danh sinh viên</title>
  <style>
    body { font-family: Arial, sans-serif; max-width: 500px; margin: auto; padding: 20px; }
    input, select, button { width: 100%; padding: 10px; margin: 5px 0; font-size: 16px; }
    #status { margin-top: 10px; color: green; }
    #error { margin-top: 10px; color: red; }
  </style>
</head>
<body>
  <h2>Điểm danh sinh viên</h2>

  <input type="text" id="name" placeholder="Họ và tên">
  <input type="text" id="mssv" placeholder="MSSV">
  <input type="text" id="class" placeholder="Lớp">

  <select id="period">
    <option value="">-- Chọn tiết học --</option>
    <option value="1-3">Tiết 1-3</option>
    <option value="4-6">Tiết 4-6</option>
    <option value="7-9">Tiết 7-9</option>
  </select>

  <input type="text" id="subject" placeholder="Môn học">

  <button onclick="submitAttendance()">Điểm danh</button>
  <div id="status"></div>
  <div id="error"></div>

  <script>
    const sheetURL = "YOUR_GOOGLE_SCRIPT_URL"; // Thay bằng URL Web App
    const CLASS_LAT = 10.762622; // Vĩ độ lớp học
    const CLASS_LON = 106.660172; // Kinh độ lớp học
    const ALLOWED_DISTANCE = 50; // Giới hạn 50m

    function getDistance(lat1, lon1, lat2, lon2) {
      const R = 6371e3; // bán kính Trái Đất (m)
      const toRad = deg => deg * Math.PI / 180;
      const φ1 = toRad(lat1), φ2 = toRad(lat2);
      const Δφ = toRad(lat2 - lat1);
      const Δλ = toRad(lon2 - lon1);

      const a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
                Math.cos(φ1) * Math.cos(φ2) *
                Math.sin(Δλ/2) * Math.sin(Δλ/2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      return R * c;
    }

    function submitAttendance() {
      const name = document.getElementById("name").value.trim();
      const mssv = document.getElementById("mssv").value.trim();
      const className = document.getElementById("class").value.trim();
      const period = document.getElementById("period").value.trim();
      const subject = document.getElementById("subject").value.trim();
      const status = document.getElementById("status");
      const error = document.getElementById("error");

      status.innerText = "";
      error.innerText = "";

      if (!name || !mssv || !className || !period || !subject) {
        alert("Vui lòng nhập đầy đủ thông tin");
        return;
      }

      if (!navigator.geolocation) {
        alert("Trình duyệt không hỗ trợ GPS");
        return;
      }

      status.innerText = "Đang lấy vị trí...";
      navigator.geolocation.getCurrentPosition(async (pos) => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;

        const distance = getDistance(CLASS_LAT, CLASS_LON, lat, lon);

        if (distance > ALLOWED_DISTANCE) {
          error.innerText = "Bạn không ở trong khu vực điểm danh (cách " + Math.round(distance) + "m)";
          status.innerText = "";
          return;
        }

        // Lấy địa chỉ từ OpenStreetMap
        let address = "";
        try {
          const res = await fetch(`https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`);
          const data = await res.json();
          address = data.display_name || "";
        } catch {}

        const payload = { name, mssv, class: className, period, subject, lat, lon, address };

        await fetch(sheetURL, {
          method: "POST",
          body: JSON.stringify(payload)
        });

        status.innerText = "Điểm danh thành công!";
      }, (err) => {
        alert("Không thể lấy vị trí: " + err.message);
      });
    }
  </script>
</body>
</html>
# Check
