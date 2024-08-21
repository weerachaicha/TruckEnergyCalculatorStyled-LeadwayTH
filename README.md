<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Truck Energy Consumption Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            color: #333;
            margin: 0;
            padding: 0;
        }
        h2 {
            background-color: #4CAF50;
            color: white;
            padding: 20px;
            text-align: center;
            margin: 0;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        h2 img {
            height: 50px;
            margin-right: 10px;
        }
        #language-selector {
            margin: 20px auto;
            text-align: center;
        }
        form {
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            margin: 20px auto;
            padding: 20px;
            width: 50%;
            max-width: 600px;
        }
        label {
            display: block;
            margin: 10px 0 5px;
            font-weight: bold;
        }
        input[type="number"], select {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        input[type="button"] {
            background-color: #2196F3; /* สีฟ้า */
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        input[type="button"]:hover {
            background-color: #1976D2; /* สีฟ้าเข้ม */
        }
        table {
            width: 50%;
            margin: 20px auto;
            border-collapse: collapse;
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        table, th, td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: left;
        }
        th {
            background-color: #2196F3; /* สีฟ้า */
            color: white;
        }
        td {
            background-color: #f1f8ff; /* สีพื้นหลังฟ้าอ่อน */
        }
    </style>
</head>
<body>
    <h2>
        Truck Energy Consumption Calculator
    </h2>
    
    <div id="language-selector">
        <label for="language">Choose Language / เลือกภาษา / 选择语言 / 言語を選択:</label>
        <select id="language" onchange="changeLanguage()">
            <option value="en">English</option>
            <option value="th">ไทย</option>
            <option value="zh">中文</option>
            <option value="ja">日本語</option>
        </select>
    </div>

    <form id="calcForm">
        <label for="truckModel" data-en="Select Truck Model:" data-th="เลือกรุ่นรถ:" data-zh="选择卡车型号:" data-ja="トラックモデルを選択:">Select Truck Model:</label>
        <select id="truckModel" name="truckModel" required>
            <option value="350">EV490-25 (350 kWh)</option>
            <option value="423">EV490-25 (423 kWh)</option>
            <option value="85">TYPE FR601 (85 kWh)</option>
        </select>

        <label for="distance" data-en="Distance (km):" data-th="ระยะทาง (กม.):" data-zh="距离 (公里):" data-ja="距離 (キロメートル):">Distance (km):</label>
        <input type="number" id="distance" name="distance" value="200" required>

        <label for="weight" data-en="Load Weight (tons):" data-th="น้ำหนักบรรทุก (ตัน):" data-zh="载重量 (吨):" data-ja="積載重量 (トン):">Load Weight (tons):</label>
        <input type="number" id="weight" name="weight" value="50.5" required>

        <label for="rate" data-en="Enter Charging Rate (Baht/kWh):" data-th="กรอกอัตราค่าชาร์จ (บาท/กิโลวัตต์ชั่วโมง):" data-zh="输入充电费率 (泰铢/千瓦时):" data-ja="充電料金を入力 (バーツ/キロワット時):">Enter Charging Rate (Baht/kWh):</label>
        <input type="number" id="rate" name="rate" value="7.5" step="0.01" required>

        <input type="button" value="Calculate" data-en="Calculate" data-th="คำนวณ" data-zh="计算" data-ja="計算" onclick="calculateEnergy()">
    </form>

    <table>
        <tr>
            <th data-en="Category" data-th="หมวดหมู่" data-zh="类别" data-ja="カテゴリー">Category</th>
            <th data-en="Amount" data-th="จำนวน" data-zh="数量" data-ja="金額">Amount</th>
        </tr>
        <tr>
            <td data-en="Total Energy Used" data-th="พลังงานที่ใช้ทั้งหมด" data-zh="总能耗" data-ja="使用された総エネルギー">Total Energy Used</td>
            <td id="energyResult"></td>
        </tr>
        <tr>
            <td data-en="Cost during Peak (09:00-22:00)" data-th="METER TOU-Peak (09:00-22:00)" data-zh="高峰时段费用 (09:00-22:00)" data-ja="ピーク時のコスト (09:00-22:00)">Cost during Peak (09:00-22:00)</td>
            <td id="costPeakResult"></td>
        </tr>
        <tr>
            <td data-en="Cost during Off-Peak (22:00-09:00)" data-th="METER TOU-Off Peak (22:00-09:00)" data-zh="非高峰时段费用 (22:00-09:00)" data-ja="オフピーク時のコスト (22:00-09:00)">Cost during Off-Peak (22:00-09:00)</td>
            <td id="costOffPeakResult"></td>
        </tr>
        <tr>
            <td data-en="Cost at External Station" data-th="ค่าใช้จ่ายที่สถานีภายนอก" data-zh="外部站点的费用" data-ja="外部ステーションでのコスト">Cost at External Station</td>
            <td id="costExternalResult"></td>
        </tr>
        <tr>
            <td data-en="Battery Usage" data-th="การใช้แบตเตอรี่" data-zh="电池使用情况" data-ja="バッテリー使用量">Battery Usage</td>
            <td id="batteryUsage"></td>
        </tr>
    </table>

    <script>
        function calculateEnergy() {
            // รับค่าจากฟอร์ม
            var distance = parseFloat(document.getElementById('distance').value);
            var weight = parseFloat(document.getElementById('weight').value);
            var truckModel = document.getElementById('truckModel').value;
            var rate = parseFloat(document.getElementById('rate').value);
            var energyUsed;
            var batteryCapacity;

            // คำนวณการใช้พลังงานตามรุ่นรถ
            if (truckModel === "85") {
                // สำหรับ TYPE FR601 (85 kWh)
                energyUsed = distance * (0.0216 * weight + 0.2504);
                batteryCapacity = 85; // kWh
            } else if (truckModel === "350") {
                // สำหรับ EV490-25 (350 kWh)
                energyUsed = distance * (0.0151 * weight + 0.635);
                batteryCapacity = 350; // kWh
            } else {
                // สำหรับ EV490-25 (423 kWh)
                energyUsed = distance * (0.0151 * weight + 0.635);
                batteryCapacity = 423; // kWh
            }

            // คำนวณค่าไฟฟ้า
            var costPeak = energyUsed * 4.13; // Peak rate
            var costOffPeak = energyUsed * 2.6; // Off-Peak rate
            var costExternal = energyUsed * rate; // User-defined charging station rate

            // คำนวณเปอร์เซ็นต์การใช้แบตเตอรี่
            var batteryUsagePercent = (energyUsed / batteryCapacity) * 100;

            // แสดงผลลัพธ์ในตาราง
            document.getElementById('energyResult').innerText = energyUsed.toFixed(2) + " kWh (" + truckModel + " kWh model)";
            document.getElementById('costPeakResult').innerText = costPeak.toFixed(2) + " Baht";
            document.getElementById('costOffPeakResult').innerText = costOffPeak.toFixed(2) + " Baht";
            document.getElementById('costExternalResult').innerText = costExternal.toFixed(2) + " Baht";
            document.getElementById('batteryUsage').innerText = batteryUsagePercent.toFixed(2) + "% of total battery capacity";
        }

        function changeLanguage() {
            var lang = document.getElementById('language').value;
            document.querySelectorAll('label, th, td, input[type="button"]').forEach(function(element) {
                var text = element.getAttribute('data-' + lang);
                if (text) {
                    element.textContent = text;
                }
            });
        }
    </script>
</body>
</html>
