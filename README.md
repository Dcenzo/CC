<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cân Thông Minh ESP32</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 0; 
            padding: 20px; 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            min-height: 100vh;
        }
        .container { 
            max-width: 600px; 
            margin: 0 auto; 
            background: rgba(255,255,255,0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.3);
        }
        .weight-display { 
            text-align: center; 
            font-size: 4em; 
            margin: 30px 0;
            font-weight: bold;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }
        .status { 
            text-align: center; 
            margin: 20px 0; 
            font-size: 1.2em;
        }
        button { 
            background: linear-gradient(45deg, #FF6B6B, #4ECDC4);
            color: white; 
            border: none; 
            padding: 15px 30px; 
            font-size: 18px; 
            border-radius: 50px;
            cursor: pointer; 
            margin: 10px;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        button:hover { 
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.3);
        }
        .history {
            margin-top: 30px;
            background: rgba(255,255,255,0.1);
            border-radius: 15px;
            padding: 20px;
        }
        .history-item {
            display: flex;
            justify-content: space-between;
            padding: 10px 0;
            border-bottom: 1px solid rgba(255,255,255,0.2);
        }
        .stable { color: #4ECDC4; }
        .unstable { color: #FF6B6B; }
    </style>
</head>
<body>
    <div class="container">
        <h1 style="text-align: center; margin-bottom: 40px;">🏠 CÂN THÔNG MINH ESP32</h1>
        
        <div class="weight-display" id="weight">0.0 kg</div>
        
        <div class="status" id="status">Sẵn sàng</div>
        
        <div style="text-align: center;">
            <button onclick="startWeigh()">🎯 Bắt đầu cân</button>
            <button onclick="tare()">⚖️ Reset về 0</button>
            <button onclick="loadHistory()">📊 Xem lịch sử</button>
        </div>
        
        <div class="history" id="history" style="display: none;">
            <h3>📈 Lịch sử cân nặng</h3>
            <div id="historyList"></div>
        </div>
        
        <div style="margin-top: 30px; text-align: center;">
            <h4>🔧 Hiệu chỉnh cân</h4>
            <input type="number" id="knownWeight" placeholder="Nhập trọng lượng chuẩn (kg)" 
                   style="padding: 10px; border-radius: 10px; border: none; margin: 10px;">
            <br>
            <button onclick="calibrate()">⚙️ Hiệu chỉnh</button>
        </div>
    </div>

    <script>
        let isWeighing = false;
        
        function updateWeight() {
            fetch('/api/weight')
            .then(response => response.json())
            .then(data => {
                document.getElementById('weight').innerText = data.weight.toFixed(1) + ' kg';
                
                if (data.is_stable) {
                    document.getElementById('status').innerHTML = '<span class="stable">✅ Ổn định</span>';
                } else {
                    document.getElementById('status').innerHTML = '<span class="unstable">⏳ Chưa ổn định</span>';
                }
            })
            .catch(error => {
                console.error('Lỗi:', error);
                document.getElementById('status').innerText = '❌ Lỗi kết nối';
            });
        }
        
        function startWeigh() {
            if (isWeighing) return;
            
            isWeighing = true;
            document.getElementById('status').innerText = '🔄 Đang cân...';
            
            fetch('/api/weigh', { method: 'POST' })
            .then(response => response.json())
            .then(data => {
                setTimeout(() => {
                    isWeighing = false;
                    document.getElementById('status').innerText = '✅ Hoàn thành';
                }, 3000);
            })
            .catch(error => {
                isWeighing = false;
                document.getElementById('status').innerText = '❌ Lỗi';
            });
        }
        
        function tare() {
            document.getElementById('status').innerText = '🔄 Đang reset...';
            
            fetch('/api/tare', { method: 'POST' })
            .then(response => response.json())
            .then(data => {
                document.getElementById('status').innerText = '✅ Đã reset về 0';
            })
            .catch(error => {
                document.getElementById('status').innerText = '❌ Lỗi reset';
            });
        }
        
        function loadHistory() {
            const historyDiv = document.getElementById('history');
            const historyList = document.getElementById('historyList');
            
            fetch('/api/history')
            .then(response => response.json())
            .then(data => {
                historyList.innerHTML = '';
                
                if (data.history.length === 0) {
                    historyList.innerHTML = '<p>Chưa có dữ liệu</p>';
                } else {
                    data.history.forEach(entry => {
                        const date = new Date(entry.timestamp);
                        const item = document.createElement('div');
                        item.className = 'history-item';
                        item.innerHTML = `
                            <span>${entry.weight.toFixed(1)} kg</span>
                            <span>${date.toLocaleString('vi-VN')}</span>
                        `;
                        historyList.appendChild(item);
                    });
                }
                
                historyDiv.style.display = historyDiv.style.display === 'none' ? 'block' : 'none';
            })
            .catch(error => {
                console.error('Lỗi:', error);
            });
        }
        
        function calibrate() {
            const knownWeight = document.getElementById('knownWeight').value;
            if (!knownWeight) {
                alert('Vui lòng nhập trọng lượng chuẩn');
                return;
            }
            
            if (confirm(`Đặt vật ${knownWeight}kg lên cân và nhấn OK để bắt đầu hiệu chỉnh`)) {
                document.getElementById('status').innerText = '🔄 Đang hiệu chỉnh...';
                
                fetch('/api/calibrate', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
                    body: `weight=${knownWeight}`
                })
                .then(response => response.json())
                .then(data => {
                    document.getElementById('status').innerText = '✅ Hiệu chỉnh hoàn tất';
                    alert('Hiệu chỉnh thành công! Hãy tháo vật chuẩn ra.');
                })
                .catch(error => {
                    document.getElementById('status').innerText = '❌ Lỗi hiệu chỉnh';
                });
            }
        }
        
        // Cập nhật cân nặng mỗi giây
        setInterval(updateWeight, 1000);
        updateWeight(); // Gọi ngay lần đầu
    </script>
</body>
</html>
  )";
}
