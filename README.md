<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>資料查詢介面</title>
    <style>
        body { font-family: sans-serif; padding: 20px; }
        .container { max-width: 400px; margin: auto; border: 1px solid #ccc; padding: 20px; border-radius: 8px; }
        input { width: 100%; padding: 8px; margin: 10px 0; box-sizing: border-box; }
        button { width: 100%; padding: 10px; background-color: #007bff; color: white; border: none; cursor: pointer; }
        #result { margin-top: 20px; white-space: pre-wrap; background: #f4f4f4; padding: 10px; border-radius: 4px; }
    </style>
</head>
<body>

<div class="container">
    <h3>查詢使用者</h3>
    <label for="userName">請輸入使用者名稱：</label>
    <input type="text" id="userName" placeholder="例如：王小明">
    <button onclick="sendData()">送出查詢</button>

    <div id="result">等待查詢結果...</div>
</div>

<script>
    async function sendData() {
        const nameValue = document.getElementById('userName').value;
        const resultDiv = document.getElementById('result');
        const webhookUrl = 'https://ejagf9pp.roamerhost.com/webhook-test/ec33a6e9-5571-47e3-a674-a1c6b65fa661';

        if (!nameValue) {
            alert('請輸入名稱！');
            return;
        }

        resultDiv.innerText = '處理中...';

        try {
            const response = await fetch(webhookUrl, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    name: nameValue
                })
            });

            const data = await response.json();
            resultDiv.innerText = '回傳結果：\n' + JSON.stringify(data, null, 2);
        } catch (error) {
            resultDiv.innerText = '發生錯誤：' + error;
            console.error('Error:', error);
        }
    }
</script>

</body>
</html>
