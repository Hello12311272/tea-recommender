# tea-recommender
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>茶言觀色 · 客戶流失風險評估器</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Noto Sans TC', sans-serif; background-color: #f9f7f2; }
        .fade-in { animation: fadeIn 0.5s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .chart-container { position: relative; height: 250px; width: 100%; }
        /* 自定義滾動條 */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #047857; border-radius: 4px; }
    </style>
</head>
<body class="text-gray-800">

    <!-- 頂部導航 -->
    <nav class="bg-white shadow-sm sticky top-0 z-50">
        <div class="max-w-6xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <span class="text-2xl">🧋</span>
                <span class="font-bold text-xl text-green-900">茶言觀色 <span class="text-sm font-normal text-gray-500">| 客戶流失風險評估器</span></span>
            </div>
            <div class="hidden md:flex space-x-6 text-sm text-gray-600">
                <a href="#" class="hover:text-green-700">首頁</a>
                <a href="#" class="hover:text-green-700">數據分析</a>
                <a href="#" class="hover:text-green-700">風險預警</a>
            </div>
        </div>
    </nav>

    <!-- 主內容區 -->
    <main class="max-w-6xl mx-auto px-4 py-8">
        
        <!-- 標題區 -->
        <div class="bg-green-800 rounded-2xl p-6 mb-8 text-white shadow-lg">
            <h1 class="text-2xl md:text-3xl font-bold mb-2">🧋 茶言觀色 · 客戶流失風險評估器</h1>
            <p class="text-green-100 mb-4">輸入顧客消費數據，系統將自動分群並顯示流失風險等級，提供具體經營建議。</p>
            <div class="inline-flex items-center bg-green-700 px-3 py-1 rounded-full text-xs">
                <span class="mr-1">⚡</span> 基於 K-Means 分群模型 · 四大關鍵特徵
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            <!-- 左側：輸入表單 -->
            <div class="lg:col-span-4">
                <div class="bg-white rounded-xl shadow-md p-6 sticky top-24">
                    <h2 class="text-lg font-bold text-gray-800 mb-4 flex items-center">
                        <span class="mr-2">📋</span> 請輸入顧客資料
                    </h2>
                    <form id="churnForm" class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">🍵 月消費次數 <span class="text-xs text-gray-400">(0-50)</span></label>
                            <input type="number" id="freq" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none transition" placeholder="例如：8" min="0" max="50">
                            <p class="text-xs text-red-500 mt-1 hidden" id="freqError">請輸入有效數字 (0-50)</p>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">💰 平均消費金額 <span class="text-xs text-gray-400">(0-500)</span></label>
                            <input type="number" id="amount" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none transition" placeholder="例如：73" min="0" max="500">
                            <p class="text-xs text-red-500 mt-1 hidden" id="amountError">請輸入有效金額 (0-500)</p>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">📆 距上次到店天數 <span class="text-xs text-gray-400">(0-365)</span></label>
                            <input type="number" id="days" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none transition" placeholder="例如：70" min="0" max="365">
                            <p class="text-xs text-red-500 mt-1 hidden" id="daysError">請輸入有效天數 (0-365)</p>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">⭐ 顧客滿意度 <span class="text-xs text-gray-400">(1-5)</span></label>
                            <input type="number" id="satisfaction" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none transition" placeholder="例如：4" min="1" max="5" step="0.1">
                            <p class="text-xs text-red-500 mt-1 hidden" id="satError">請輸入 1-5 之間的評分</p>
                        </div>
                        
                        <div class="flex space-x-3 pt-2">
                            <button type="button" onclick="fillExample()" class="flex-1 px-4 py-2 bg-gray-100 text-gray-700 rounded-lg hover:bg-gray-200 transition text-sm font-medium">
                                🔄 使用範例
                            </button>
                            <button type="submit" class="flex-1 px-4 py-2 bg-green-700 text-white rounded-lg hover:bg-green-800 transition text-sm font-medium shadow-md">
                                🔍 評估此顧客
                            </button>
                        </div>
                    </form>
                </div>
            </div>

            <!-- 右側：結果展示 -->
            <div class="lg:col-span-8 space-y-6">
                
                <!-- 評估結果卡片 -->
                <div id="resultSection" class="bg-white rounded-xl shadow-md p-6 hidden fade-in">
                    <div class="flex justify-between items-center mb-4 border-b pb-4">
                        <h2 class="text-xl font-bold text-gray-800 flex items-center">
                            <span class="mr-2">📊</span> 評估結果
                            <span class="ml-3 px-3 py-1 bg-green-100 text-green-800 text-xs rounded-full" id="clusterBadge">群組 0</span>
                        </h2>
                        <span class="text-sm text-gray-400" id="timestamp">剛剛更新</span>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- 左側：客戶畫像 -->
                        <div class="space-y-4">
                            <div class="flex items-center justify-between">
                                <h3 class="text-lg font-bold text-gray-800" id="clusterName">高滿意沉睡客</h3>
                                <span class="px-3 py-1 bg-orange-100 text-orange-700 text-xs font-bold rounded-full">📉 流失比例 <span id="riskPercent">33%</span></span>
                            </div>
                            <div class="bg-gray-50 p-4 rounded-lg text-sm text-gray-600 space-y-1">
                                <p>📊 月消費 <span class="font-bold text-gray-800" id="resFreq">8.42</span> 次</p>
                                <p>💰 平均 <span class="font-bold text-gray-800" id="resAmount">$73.14</span></p>
                                <p>📆 <span class="font-bold text-gray-800" id="resDays">70.52</span> 天沒來</p>
                                <p>⭐ 滿意度 <span class="font-bold text-gray-800" id="resSat">4.23</span></p>
                            </div>
                            <div class="bg-yellow-50 border-l-4 border-yellow-400 p-3 text-sm text-yellow-800">
                                💡 <span id="insightText">滿意度高但許久未到，可能被其他品牌搶走，或近期生活型態改變。</span>
                            </div>
                        </div>

                        <!-- 右側：風險儀表 -->
                        <div class="flex flex-col items-center justify-center bg-gray-50 rounded-lg p-4">
                            <div class="relative w-40 h-40">
                                <canvas id="gaugeChart"></canvas>
                                <div class="absolute inset-0 flex items-center justify-center flex-col">
                                    <span class="text-3xl font-bold text-gray-800" id="gaugeValue">33%</span>
                                    <span class="text-xs text-gray-500">流失風險</span>
                                </div>
                            </div>
                            <div class="mt-2 text-center">
                                <p class="text-sm font-medium text-gray-700" id="riskLevel">⚠️ 中高流失風險</p>
                                <p class="text-xs text-gray-500">所屬群體平均流失率</p>
                            </div>
                        </div>
                    </div>

                    <!-- 行動建議 -->
                    <div class="mt-6 bg-green-50 rounded-xl p-5 border border-green-100">
                        <h4 class="font-bold text-green-900 mb-3 flex items-center">📌 經營行動建議</h4>
                        <ul class="space-y-2 text-sm text-gray-700" id="actionList">
                            <li class="flex items-start"><span class="mr-2 text-green-600">•</span> 發送「久未見面」專屬優惠，例如第 2 杯半價</li>
                            <li class="flex items-start"><span class="mr-2 text-green-600">•</span> 推薦季節新品，利用高滿意度吸引回購</li>
                            <li class="flex items-start"><span class="mr-2 text-green-600">•</span> 可考慮簡訊問候 + 贈送小配料</li>
                        </ul>
                        <div class="mt-4 flex items-center justify-between bg-white px-4 py-2 rounded-lg shadow-sm">
                            <span class="text-xs text-gray-500">🎯 目標：提升到店頻率，轉為「穩定客」</span>
                            <button class="text-xs bg-green-600 text-white px-3 py-1 rounded hover:bg-green-700 transition">發送促銷訊息</button>
                        </div>
                    </div>
                </div>

                <!-- 圖表區域 -->
                <div id="chartsSection" class="grid grid-cols-1 md:grid-cols-2 gap-6 hidden fade-in">
                    <!-- 趨勢圖 -->
                    <div class="bg-white rounded-xl shadow-md p-5">
                        <h3 class="font-bold text-gray-700 mb-4 text-sm">📈 歷史消費趨勢</h3>
                        <div class="chart-container">
                            <canvas id="trendChart"></canvas>
                        </div>
                    </div>
                    <!-- 雷達圖 -->
                    <div class="bg-white rounded-xl shadow-md p-5">
                        <h3 class="font-bold text-gray-700 mb-4 text-sm">📊 四維度綜合評分</h3>
                        <div class="chart-container">
                            <canvas id="radarChart"></canvas>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </main>

    <!-- 頁脚 -->
    <footer class="bg-white border-t mt-12 py-8">
        <div class="max-w-6xl mx-auto px-4 text-center text-xs text-gray-400">
            <p class="mb-2">📊 基於 Colab 分析結果 · 四大特徵標準化後 K-Means 聚類 (K=4)</p>
            <div class="flex justify-center space-x-4">
                <a href="#" class="hover:text-green-600">隱私權條款</a>
                <a href="#" class="hover:text-green-600">API 文檔</a>
                <span>© 2026 茶言觀色 Analytics</span>
            </div>
        </div>
    </footer>

    <!-- JavaScript 邏輯 -->
    <script>
        // 初始化圖表變量
        let gaugeChartInst = null;
        let trendChartInst = null;
        let radarChartInst = null;

        // 填充範例數據
        function fillExample() {
            document.getElementById('freq').value = 8.42;
            document.getElementById('amount').value = 73.14;
            document.getElementById('days').value = 70.52;
            document.getElementById('satisfaction').value = 4.23;
        }

        // 表單提交處理
        document.getElementById('churnForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            // 獲取輸入
            const freq = parseFloat(document.getElementById('freq').value);
            const amount = parseFloat(document.getElementById('amount').value);
            const days = parseFloat(document.getElementById('days').value);
            const sat = parseFloat(document.getElementById('satisfaction').value);

            // 驗證
            let isValid = true;
            if (isNaN(freq) || freq < 0 || freq > 50) { document.getElementById('freqError').classList.remove('hidden'); isValid = false; } else { document.getElementById('freqError').classList.add('hidden'); }
            if (isNaN(amount) || amount < 0 || amount > 500) { document.getElementById('amountError').classList.remove('hidden'); isValid = false; } else { document.getElementById('amountError').classList.add('hidden'); }
            if (isNaN(days) || days < 0 || days > 365) { document.getElementById('daysError').classList.remove('hidden'); isValid = false; } else { document.getElementById('daysError').classList.add('hidden'); }
            if (isNaN(sat) || sat < 1 || sat > 5) { document.getElementById('satError').classList.remove('hidden'); isValid = false; } else { document.getElementById('satError').classList.add('hidden'); }

            if (!isValid) return;

            // 模擬計算邏輯 (實際應連接後端)
            calculateRisk(freq, amount, days, sat);
        });

        // 計算風險並更新 UI
        function calculateRisk(freq, amount, days, sat) {
            // 簡單算法模擬：天數越長風險越高，頻率越低風險越高
            let riskScore = 0;
            riskScore += (days / 365) * 40; // 天數佔 40%
            riskScore += (1 - (freq / 50)) * 30; // 頻率佔 30%
            riskScore += (1 - (sat / 5)) * 30; // 滿意度佔 30%
            
            const riskPercent = Math.min(100, Math.round(riskScore * 100));
            
            // 判斷群組
            let clusterName = "一般客戶";
            let insight = "消費行為正常，保持關注即可。";
            let actions = ["發送月度電子報", "邀請參與會員日"];
            let riskLevel = "✅ 低風險";
            let riskColor = "#10b981";

            if (days > 60 && sat > 4) {
                clusterName = "高滿意沉睡客";
                insight = "滿意度高但許久未到，可能被其他品牌搶走，或近期生活型態改變。";
                actions = ["發送「久未見面」專屬優惠", "推薦季節新品", "簡訊問候 + 贈送小配料"];
                riskLevel = "⚠️ 中高流失風險";
                riskColor = "#f59e0b";
            } else if (days > 90) {
                clusterName = "流失高危客";
                insight = "已長時間未消費，流失風險極高，需緊急挽回。";
                actions = ["發送大幅折扣券", "電話回訪調查原因", "提供免費升級服務"];
                riskLevel = "🚨 高風險";
                riskColor = "#ef4444";
            } else if (freq > 10 && sat > 4.5) {
                clusterName = "忠誠穩定客";
                insight = "高頻高滿意，是品牌的核心資產。";
                actions = ["邀請成為 VIP 會員", "提供新品試喝權", "生日專屬禮遇"];
                riskLevel = "✅ 低風險";
                riskColor = "#10b981";
            }

            // 更新文字內容
            document.getElementById('clusterName').innerText = clusterName;
            document.getElementById('riskPercent').innerText = riskPercent + "%";
            document.getElementById('insightText').innerText = insight;
            document.getElementById('riskLevel').innerText = riskLevel;
            document.getElementById('riskLevel').className = `text-sm font-medium ${riskColor === '#ef4444' ? 'text-red-600' : riskColor === '#f59e0b' ? 'text-orange-600' : 'text-green-600'}`;
            
            document.getElementById('resFreq').innerText = freq;
            document.getElementById('resAmount').innerText = '$' + amount.toFixed(2);
            document.getElementById('resDays').innerText = days;
            document.getElementById('resSat').innerText = sat;

            // 更新建議列表
            const actionList = document.getElementById('actionList');
            actionList.innerHTML = '';
            actions.forEach(action => {
                const li = document.createElement('li');
                li.className = "flex items-start";
                li.innerHTML = `<span class="mr-2 text-green-600">•</span> ${action}`;
                actionList.appendChild(li);
            });

            // 顯示結果區
            document.getElementById('resultSection').classList.remove('hidden');
            document.getElementById('chartsSection').classList.remove('hidden');

            // 渲染圖表
            renderGauge(riskPercent, riskColor);
            renderTrendChart();
            renderRadarChart(freq, amount, days, sat);
            
            // 滾動到結果區
            document.getElementById('resultSection').scrollIntoView({ behavior: 'smooth' });
        }

        // 渲染儀表盤
        function renderGauge(percent, color) {
            const ctx = document.getElementById('gaugeChart').getContext('2d');
            if (gaugeChartInst) gaugeChartInst.destroy();

            gaugeChartInst = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: ['風險', '安全'],
                    datasets: [{
                        data: [percent, 100 - percent],
                        backgroundColor: [color, '#e5e7eb'],
                        borderWidth: 0,
                        circumference: 180,
                        rotation: 270,
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false }, tooltip: { enabled: false } },
                    cutout: '70%'
                }
            });
            document.getElementById('gaugeValue').innerText = percent + "%";
        }

        // 渲染趨勢圖
        function renderTrendChart() {
            const ctx = document.getElementById('trendChart').getContext('2d');
            if (trendChartInst) trendChartInst.destroy();

            trendChartInst = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['1 月', '2 月', '3 月', '4 月', '5 月', '6 月'],
                    datasets: [{
                        label: '消費金額',
                        data: [120, 150, 180, 100, 80, 0],
                        borderColor: '#047857',
                        backgroundColor: 'rgba(4, 120, 87, 0.1)',
                        fill: true,
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: { y: { beginAtZero: true, grid: { display: false } }, x: { grid: { display: false } } }
                }
            });
        }

        // 渲染雷達圖
        function renderRadarChart(freq, amount, days, sat) {
            const ctx = document.getElementById('radarChart').getContext('2d');
            if (radarChartInst) radarChartInst.destroy();

            // 標準化數據用於顯示 (模擬)
            const normFreq = Math.min(100, (freq / 20) * 100);
            const normAmount = Math.min(100, (amount / 100) * 100);
            const normDays = Math.min(100, (1 - (days / 90)) * 100); // 天數越少分越高
            const normSat = (sat / 5) * 100;

            radarChartInst = new Chart(ctx, {
                type: 'radar',
                data: {
                    labels: ['消費頻率', '消費金額', '近期活躍', '滿意度'],
                    datasets: [{
                        label: '此顧客',
                        data: [normFreq, normAmount, normDays, normSat],
                        backgroundColor: 'rgba(4, 120, 87, 0.2)',
                        borderColor: '#047857',
                        pointBackgroundColor: '#047857'
                    }, {
                        label: '平均水準',
                        data: [50, 50, 50, 50],
                        backgroundColor: 'rgba(156, 163, 175, 0.1)',
                        borderColor: '#9ca3af',
                        pointBackgroundColor: '#9ca3af',
                        borderDash: [5, 5]
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { r: { min: 0, max: 100, grid: { color: '#e5e7eb' }, pointLabels: { font: { size: 10 } } } },
                    plugins: { legend: { position: 'bottom', labels: { boxWidth: 10, font: { size: 10 } } } }
                }
            });
        }
    </script>
</body>
</html>
