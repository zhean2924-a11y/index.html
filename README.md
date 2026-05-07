<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>訂單查詢系統</title>
    <style>
        :root {
            --border-color: #ccc;
            --header-bg: #f2f2f2;
        }
        body { font-family: "Microsoft JhengHei", sans-serif; padding: 30px; background-color: #fff; }
        h1 { font-size: 24px; margin-bottom: 25px; font-weight: bold; }

        /* 查詢區域佈局 */
        .search-container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
            gap: 15px 10px;
            align-items: center;
            margin-bottom: 30px;
        }
        .form-group { display: flex; align-items: center; white-space: nowrap; }
        .form-group label { margin-right: 8px; font-size: 16px; }
        .form-group input {
            padding: 4px 8px;
            border: 1px solid var(--border-color);
            border-radius: 2px;
            width: 100%;
            height: 25px;
        }

        button {
            padding: 4px 15px;
            background-color: #efefef;
            border: 1px solid #767676;
            border-radius: 2px;
            cursor: pointer;
            font-size: 14px;
        }
        button:hover { background-color: #e5e5e5; }

        /* 表格樣式 */
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }
        th {
            background-color: var(--header-bg);
            border: 1px solid #ddd;
            padding: 12px 8px;
            font-weight: bold;
            text-align: center;
        }
        td {
            border: 1px solid #ddd;
            padding: 10px 8px;
            text-align: center;
        }
        tr:nth-child(even) { background-color: #fafafa; }

        .status-bar { margin-top: 10px; font-size: 14px; color: #666; }
    </style>
</head>
<body>

    <h1>訂單查詢系統</h1>

    <div class="search-container">
        <div class="form-group">
            <label>列碼：</label><input type="text" id="rowId">
        </div>
        <div class="form-group">
            <label>訂單編號：</label><input type="text" id="orderId">
        </div>
        <div class="form-group">
            <label>會員ID：</label><input type="text" id="memberId">
        </div>
        <div class="form-group">
            <label>會員姓名：</label><input type="text" id="name">
        </div>
        <div class="form-group">
            <label>商品：</label><input type="text" id="product">
        </div>
        <div class="form-group">
            <label>金額：</label><input type="text" id="amount">
        </div>
        <div class="form-group" style="grid-column: span 1;">
            <label>交易時間：</label><input type="text" id="tradeTime" placeholder="YYYY-MM-DD">
        </div>
        <button onclick="handleSearch()">查詢</button>
    </div>

    <div id="statusBar" class="status-bar"></div>

    <table>
        <thead>
            <tr>
                <th style="width: 12%;">列碼</th>
                <th style="width: 18%;">訂單編號</th>
                <th style="width: 15%;">會員ID</th>
                <th style="width: 15%;">會員姓名</th>
                <th style="width: 15%;">商品</th>
                <th style="width: 10%;">金額</th>
                <th style="width: 15%;">交易時間</th>
            </tr>
        </thead>
        <tbody id="dataTable">
            </tbody>
    </table>

    <script>
        async function handleSearch() {
            const webhookUrl = 'https://ejagf9pp.roamerhost.com/webhook-test/ec33a6e9-5571-47e3-a674-a1c6b65fa661';
            const statusBar = document.getElementById('statusBar');
            const dataTable = document.getElementById('dataTable');

            // 抓取所有欄位值
            const payload = {
                rowId: document.getElementById('rowId').value,
                orderId: document.getElementById('orderId').value,
                memberId: document.getElementById('memberId').value,
                name: document.getElementById('name').value, // 對應圖片要求的查詢姓名
                product: document.getElementById('product').value,
                amount: document.getElementById('amount').value,
                tradeTime: document.getElementById('tradeTime').value
            };

            statusBar.innerText = "查詢中...";

            try {
                const response = await fetch(webhookUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error("網路請求失敗");

                const result = await response.json();
                statusBar.innerText = "查詢成功";

                // 將回傳資料轉為表格行
                // 假設回傳格式為物件陣列 [{...}, {...}]
                const items = Array.isArray(result) ? result : [result];
                renderTable(items);

            } catch (error) {
                statusBar.innerText = "錯誤: " + error.message;
                console.error(error);
            }
        }

        function renderTable(dataList) {
            const dataTable = document.getElementById('dataTable');
            dataTable.innerHTML = ""; // 先清空表格

            dataList.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.rowId || '-'}</td>
                    <td>${item.orderId || '-'}</td>
                    <td>${item.memberId || '-'}</td>
                    <td>${item.name || '-'}</td>
                    <td>${item.product || '-'}</td>
                    <td>${item.amount || '-'}</td>
                    <td>${item.tradeTime || '-'}</td>
                `;
                dataTable.appendChild(row);
            });
        }
    </script>

</body>
</html>
