# Farm-management-app
Manage all employees and their payments 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Farm Management App</title>
    <link rel="manifest" href="manifest.json">
    <style>
        body { font-family: -apple-system, sans-serif; padding: 15px; background: #f8f9fa; color: #333; }
        .total-box { background: #2e7d32; color: white; padding: 20px; border-radius: 15px; text-align: center; margin-bottom: 20px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
        .total-box h1 { margin: 0; font-size: 32px; }
        .card { background: white; padding: 20px; border-radius: 15px; box-shadow: 0 2px 8px rgba(0,0,0,0.05); margin-bottom: 20px; }
        input { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ddd; border-radius: 10px; font-size: 16px; box-sizing: border-box; }
        button { background: #2e7d32; color: white; border: none; padding: 15px; border-radius: 10px; width: 100%; font-size: 16px; font-weight: bold; cursor: pointer; }
        .entry { background: white; padding: 15px; border-radius: 12px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; border-left: 6px solid #2e7d32; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }
        .entry-info { flex: 1; }
        .entry-info b { font-size: 18px; color: #1b5e20; }
        .date-label { font-size: 12px; color: #666; display: block; margin-top: 4px; }
        .delete-btn { background: #ff3b30; width: auto; padding: 8px 15px; font-size: 13px; margin-left: 10px; }
    </style>
</head>
<body>

    <div class="total-box">
        <small>Total Outstanding Payment</small>
        <h1>$<span id="grandTotal">0</span></h1>
    </div>

    <div class="card">
        <h3 style="margin-top:0">Add New Entry</h3>
        <input type="text" id="empName" placeholder="Employee Name">
        <input type="number" id="empPay" placeholder="Amount to Pay">
        <input type="date" id="workDate">
        <button onclick="addEntry()">Save Record</button>
    </div>

    <div id="logList"></div>

    <script>
        // Set today's date as default
        document.getElementById('workDate').valueAsDate = new Date();

        let records = JSON.parse(localStorage.getItem('farmRecords')) || [];

        function render() {
            const list = document.getElementById('logList');
            const totalDisplay = document.getElementById('grandTotal');
            list.innerHTML = '';
            let total = 0;

            // Sort by date (newest first)
            records.sort((a, b) => new Date(b.date) - new Date(a.date));

            records.forEach((item, index) => {
                total += parseFloat(item.pay);
                list.innerHTML += `
                    <div class="entry">
                        <div class="entry-info">
                            <b>${item.name}</b>
                            <span class="date-label">Date: ${item.date}</span>
                        </div>
                        <div style="text-align: right;">
                            <div style="font-weight:bold; color:#2e7d32">$${item.pay}</div>
                            <button class="delete-btn" onclick="removeEntry(${index})">Paid</button>
                        </div>
                    </div>
                `;
            });

            totalDisplay.innerText = total.toFixed(2);
            localStorage.setItem('farmRecords', JSON.stringify(records));
        }

        function addEntry() {
            const name = document.getElementById('empName').value;
            const pay = document.getElementById('empPay').value;
            const date = document.getElementById('workDate').value;
            
            if(name && pay && date) {
                records.push({ name, pay, date });
                document.getElementById('empName').value = '';
                document.getElementById('empPay').value = '';
                render();
            } else {
                alert("Please fill in all fields");
            }
        }

        function removeEntry(index) {
            if(confirm("Mark as paid and delete?")) {
                records.splice(index, 1);
                render();
            }
        }

        render();

        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js');
        }
    </script>
</body>
</html>
