<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<title>訂單查詢系統</title>

<style>
body { font-family: Arial; padding: 20px; }
input, button { margin: 5px; padding: 6px; }
table { border-collapse: collapse; width: 100%; margin-top: 20px; }
th, td { border: 1px solid #ccc; padding: 8px; text-align: center; }
th { background: #f2f2f2; }
.info { margin-top: 10px; font-weight: bold; }
</style>
</head>

<body>

<h2>訂單查詢系統</h2>

<!-- 🔹 查詢條件 -->
列碼：
<input type="text" id="row">

訂單編號：
<input type="text" id="order">

會員ID：
<input type="text" id="memberId">

會員姓名：
<input type="text" id="name">

商品：
<input type="text" id="product">

金額：
<input type="text" id="amount">

交易時間：
<input type="text" id="time" placeholder="YYYY-MM-DD">

<button onclick="search()">查詢</button>

<div class="info" id="info"></div>

<!-- 🔹 表格 -->
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
<tbody id="tbody"></tbody>
</table>

<script>

// 🔹 金額格式化
function formatMoney(num){
  if(!num) return "-";
  return Number(num).toLocaleString();
}

// 🔹 時間格式
function formatTime(t){
  if(!t) return "-";
  return t.replace("T"," ");
}

// 🔹 查詢
async function search(){

  const row = document.getElementById("row").value.trim();
  const order = document.getElementById("order").value.trim();
  const memberId = document.getElementById("memberId").value.trim();
  const name = document.getElementById("name").value.trim();
  const product = document.getElementById("product").value.trim();
  const amount = document.getElementById("amount").value.trim();
  const time = document.getElementById("time").value.trim();

  try{
    const res = await fetch("https://vydism8n.roamerhost.com/webhook-test/44488b0c-0c0d-4f6f-8f20-86b15d5032f2",{
      method:"POST",
      headers:{ "Content-Type":"application/json" },
      body: JSON.stringify({
        "row_number": row,
        "訂單編號": order,
        "會員ID": memberId,
        "會員姓名": name,
        "購買商品": product,
        "購買金額": amount,
        "交易時間": time
      })
    });

    let data = await res.json();
    if(!Array.isArray(data)) data = [data];

    const tbody = document.getElementById("tbody");
    tbody.innerHTML = "";

    let count = 0;

    data.forEach(item=>{

      // 🔹 前端篩選
      if(row && String(item["row_number"]) !== row) return;
      if(order && item["訂單編號"] !== order) return;
      if(memberId && item["會員ID"] !== memberId) return;
      if(name && !item["會員姓名"]?.includes(name)) return;
      if(product && !item["購買商品"]?.includes(product)) return;
      if(amount && String(item["購買金額"]) !== amount) return;
      if(time && !item["交易時間"]?.includes(time)) return;

      count++;

      const tr = `
      <tr>
        <td>${item["row_number"] ?? "-"}</td>
        <td>${item["訂單編號"] ?? "-"}</td>
        <td>${item["會員ID"] ?? "-"}</td>
        <td>${item["會員姓名"] ?? "-"}</td>
        <td>${item["購買商品"] ?? "-"}</td>
        <td>${formatMoney(item["購買金額"])}</td>
        <td>${formatTime(item["交易時間"])}</td>
      </tr>
      `;

      tbody.innerHTML += tr;
    });

    document.getElementById("info").innerHTML =
      `查詢結果：${count} 筆`;

    if(count === 0){
      tbody.innerHTML = `<tr><td colspan="7">查無資料</td></tr>`;
    }

  }catch(err){
    alert("錯誤："+err);
  }
}

</script>

</body>
</html>
