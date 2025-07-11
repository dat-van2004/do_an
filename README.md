<script type="text/javascript">
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ Thống Giám Sát Nhiệt Độ</title>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 flex items-center justify-center h-screen">
    <div id="login-page" class="bg-white p-8 rounded-lg shadow-lg w-full max-w-md">
        <h2 class="text-2xl font-bold mb-6 text-center">Đăng Nhập</h2>
        <div class="mb-4">
            <label class="block text-gray-700">Tài khoản</label>
            <input id="username" type="text" class="w-full p-2 border rounded" placeholder="Nhập tài khoản">
        </div>
        <div class="mb-6">
            <label class="block text-gray-700">Mật khẩu</label>
            <input id="password" type="password" class="w-full p-2 border rounded" placeholder="Nhập mật khẩu">
        </div>
        <button onclick="login()" class="w-full bg-blue-500 text-white p-2 rounded hover:bg-blue-600">Đăng Nhập</button>
        <p id="error" class="text-red-500 text-center mt-4 hidden">Sai tài khoản hoặc mật khẩu!</p>
    </div>

    <div id="main-page" class="hidden bg-white p-8 rounded-lg shadow-lg w-full max-w-lg">
        <h2 class="text-2xl font-bold mb-6 text-center">Giám Sát Nhiệt Độ</h2>
        <div class="mb-4">
            <p class="text-lg">Nhiệt độ hiện tại: <span id="temperature" class="font-bold">25°C</span></p>
        </div>
        <div id="admin-controls" class="hidden">
            <h3 class="text-lg font-semibold mb-2">Cài Đặt Thông Số</h3>
            <div class="mb-4">
                <label class="block text-gray-700">Ngưỡng nhiệt độ tối đa (°C):</label>
                <input id="max-temp" type="number" class="w-full p-2 border rounded" value="30">
            </div>
            <div class="mb-4">
                <label class="block text-gray-700">Ngưỡng nhiệt độ tối thiểu (°C):</label>
                <input id="min-temp" type="number" class="w-full p-2 border rounded" value="20">
            </div>
            <button onclick="saveSettings()" class="w-full bg-green-500 text-white p-2 rounded hover:bg-green-600">Lưu Cài Đặt</button>
        </div>
        <button onclick="logout()" class="w-full bg-red-500 text-white p-2 rounded mt-6 hover:bg-red-600">Đăng Xuất</button>
    </div>

    <script>
        // Mô phỏng dữ liệu nhiệt độ
        let temperature = 25;
        let isAdmin = false;

        // Mô phỏng tài khoản (thực tế nên dùng backend để xác thực)
        const users = {
            admin: { password: "admin123", role: "admin" },
            guest: { password: "guest123", role: "guest" }
        };

        // Cập nhật nhiệt độ mỗi 5 giây
        setInterval(() => {
            temperature = Math.floor(Math.random() * (30 - 20 + 1)) + 20; // Nhiệt độ ngẫu nhiên từ 20-30°C
            document.getElementById('temperature').textContent = `${temperature}°C`;
        }, 5000);

        function login() {
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            const error = document.getElementById('error');

            if (users[username] && users[username].password === password) {
                isAdmin = users[username].role === 'admin';
                document.getElementById('login-page').classList.add('hidden');
                document.getElementById('main-page').classList.remove('hidden');
                if (isAdmin) {
                    document.getElementById('admin-controls').classList.remove('hidden');
                }
                error.classList.add('hidden');
            } else {
                error.classList.remove('hidden');
            }
        }

        function saveSettings() {
            const maxTemp = document.getElementById('max-temp').value;
            const minTemp = document.getElementById('min-temp').value;
            alert(`Đã lưu: Ngưỡng tối đa ${maxTemp}°C, tối thiểu ${minTemp}°C`);
            // Thực tế nên gửi dữ liệu này đến backend hoặc thiết bị
        }

        function logout() {
            document.getElementById('login-page').classList.remove('hidden');
            document.getElementById('main-page').classList.add('hidden');
            document.getElementById('admin-controls').classList.add('hidden');
            document.getElementById('username').value = '';
            document.getElementById('password').value = '';
            document.getElementById('error').classList.add('hidden');
        }
    </script>
</body>
</html>
 
 
