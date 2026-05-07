<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>訂單查詢系統</title>
    <style>
        body { font-family: "Microsoft JhengHei", sans-serif; padding: 20px; color: #333; }
        h2 { margin-bottom: 20px; }
        .search-panel { 
            display: flex; flex-wrap: wrap; gap: 15px; align-items: center; 
            background: #f9f9f9; padding: 15px; border-radius: 5px; margin-bottom: 20px;
        }
        .input-group { display: flex; align-items: center; gap: 5px; }
        input { padding: 5px; border: 1px solid #ccc; border-radius: 3px; }
        button { 
            padding: 5px 15px; cursor: pointer; background: #e0e0e0; 
            border: 1px solid #999; border-radius: 3px; 
        }
        button:hover { background: #d0d0d0; }
        
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th { background-color: #eee; border: 1px solid #ddd; padding: 10px; text-align: center; }
        td { border: 1px solid #ddd; padding: 10px; text-align: center; }
        .loading { color: blue; font-style: italic; margin-bottom: 10px; }
    </style>
</head>
<body>

    <h2>訂單查詢系統</h2>

    <div class="search-panel">
        <div class="input-group">列碼：<input type="text" id="rowId"></div>
        <div class="input-group">訂單編號：<input type="text" id="orderId"></div>
        <div class="input-group">會員ID：<input type="text" id="memberId"></div>
        <div class="input-group">會員姓名：<input type="text" id="name"></div>
        <div class="input-group">商品：<input type="text" id="product"></div>
        <div class="input-group">金額：<input type="text" id="amount"></div>
        <div class="input-group">交易時間：<input type="date" id="tradeTime"></div>
        <button onclick="executeQuery()">查詢</button>
    </div>

    <div id="statusMsg"></div>

    <table>
        <thead>
            <tr>
                <th>列碼</th>
                <th>訂單編號</th>
                <th>會員ID</th>
                <th>會員姓名</th>
                <th>商品</th>
                <th>金額</th>
                <th>交易時間</th>
            </tr>
        </thead>
        <tbody id="resultBody">
            </tbody>
    </table>

    <script>
        async function executeQuery() {
            const statusMsg = document.getElementById('statusMsg');
            const resultBody = document.getElementById('resultBody');
            const webhookUrl = 'https://ejagf9pp.roamerhost.com/webhook-test/ec33a6e9-5571-47e3-a674-a1c6b65fa661';

            // 收集所有欄位資料
            const payload = {
                rowId: document.getElementById('rowId').value,
                orderId: document.getElementById('orderId').value,
                memberId: document.getElementById('memberId').value,
                name: document.getElementById('name').value, // 這是你要求的關鍵欄位
                product: document.getElementById('product').value,
                amount: document.getElementById('amount').value,
                tradeTime: document.getElementById('tradeTime').value
            };

            statusMsg.className = 'loading';
            statusMsg.innerText = '查詢中，請稍候...';
            resultBody.innerHTML = ''; // 清空舊資料

            try {
                const response = await fetch(webhookUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error('網路回應不正確');

                const data = await response.json();
                statusMsg.innerText = '查詢完成';
                statusMsg.className = '';

                // 假設 Webhook 回傳的是陣列資料，我們將其填入表格
                // 如果回傳格式不同，請根據實際 JSON 修改此處
                if (Array.isArray(data)) {
                    renderTable(data);
                } else {
                    // 若只回傳單一筆物件，也包成陣列處理
                    renderTable([data]);
                }

            } catch (error) {
                statusMsg.innerText = '發生錯誤：' + error.message;
                statusMsg.style.color = 'red';
            }
        }

        function renderTable(items) {
            const resultBody = document.getElementById('resultBody');
            items.forEach(item => {
                const row = `<tr>
                    <td>${item.rowId || '-'}</td>
                    <td>${item.orderId || '-'}</td>
                    <td>${item.memberId || '-'}</td>
                    <td>${item.name || '-'}</td>
                    <td>${item.product || '-'}</td>
                    <td>${item.amount || '-'}</td>
                    <td>${item.tradeTime || '-'}</td>
                </tr>`;
                resultBody.innerHTML += row;
            });
        }
    </script>
</body>
</html>
