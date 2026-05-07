<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>訂單查詢系統</title>
<style>
    body {
        font-family: "Microsoft JhengHei", sans-serif;
        padding: 20px;
        color: #333;
    }
    h2 {
        margin-bottom: 20px;
    }
    .search-panel {
        display: flex;
        flex-wrap: wrap;
        gap: 15px;
        align-items: center;
        background: #f9f9f9;
        padding: 15px;
        border-radius: 5px;
        margin-bottom: 20px;
    }
    .input-group {
        display: flex;
        align-items: center;
        gap: 5px;
    }
    input {
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 3px;
    }
    button {
        padding: 5px 15px;
        cursor: pointer;
        background: #e0e0e0;
        border: 1px solid #999;
        border-radius: 3px;
    }
    button:hover {
        background: #d0d0d0;
    }
    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 10px;
    }
    th, td {
        border: 1px solid #ddd;
        padding: 10px;
        text-align: left;
    }
    th {
        background-color: #eee;
    }
</style>
</head>
<body>

<h2>訂單查詢系統</h2>

<div class="search-panel">
    <div class="input-group">
        <label for="orderId">訂單編號：</label>
        <input type="text" id="orderId" placeholder="輸入訂單編號">
    </div>
    <div class="input-group">
        <label for="customer">客戶名稱：</label>
        <input type="text" id="customer" placeholder="輸入客戶名稱">
    </div>
    <button onclick="searchOrders()">查詢</button>
    <button onclick="resetSearch()">重置</button>
</div>

<table id="orderTable">
    <thead>
        <tr>
            <th>訂單編號</th>
            <th>客戶名稱</th>
            <th>日期</th>
            <th>金額</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ORD001</td>
            <td>王小明</td>
            <td>2026-05-01</td>
            <td>$1,200</td>
        </tr>
        <tr>
            <td>ORD002</td>
            <td>李小華</td>
            <td>2026-05-02</td>
            <td>$2,500</td>
        </tr>
        <tr>
            <td>ORD003</td>
            <td>陳大同</td>
            <td>2026-05-03</td>
            <td>$3,000</td>
        </tr>
    </tbody>
</table>

<script>
function searchOrders() {
    const orderId = document.getElementById("orderId").value.trim().toLowerCase();
    const customer = document.getElementById("customer").value.trim().toLowerCase();
    const table = document.getElementById("orderTable").getElementsByTagName("tbody")[0];
    const rows = table.getElementsByTagName("tr");

    for (let row of rows) {
        const idCell = row.cells[0].textContent.toLowerCase();
        const customerCell = row.cells[1].textContent.toLowerCase();
        if ((orderId && !idCell.includes(orderId)) ||
            (customer && !customerCell.includes(customer))) {
            row.style.display = "none";
        } else {
            row.style.display = "";
        }
    }
}

function resetSearch() {
    document.getElementById("orderId").value = "";
    document.getElementById("customer").value = "";
    const rows = document.querySelectorAll("#orderTable tbody tr");
    rows.forEach(row => row.style.display = "");
}
</script>

</body>
</html>
