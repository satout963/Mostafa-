
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>مطابقة جماعية للأرصدة</title>
    <style>
        body { font-family: Arial, sans-serif; padding: 20px; background-color: #f4f4f4; }
        .logo-container { text-align: right; margin-bottom: 20px; }
        .logo-container img { width: 120px; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; background: #fff; }
        th, td { border: 1px solid #ccc; padding: 10px; text-align: center; }
        input { width: 100%; padding: 8px; }
        button { margin-top: 10px; padding: 10px 20px; font-size: 16px; margin-right: 5px; }
        #results { margin-top: 30px; background: #fff; padding: 15px; border: 1px solid #ccc; white-space: pre-line; }
    </style>
</head>
<body>

<div class="logo-container">
    <img src="bersan_logo_resized.png" alt="Bersan Logo">
</div>

<h2>مطابقة جماعية للأرصدة</h2>

<table id="dataTable">
    <thead>
        <tr>
            <th>الطرف الأول</th>
            <th>الطرف الثاني</th>
            <th>دائن</th>
            <th>مدين</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><input type="text" value="شركة برسان" readonly></td>
            <td><input type="text" value="أبو قصي" readonly></td>
            <td><input type="number" placeholder="8000"></td>
            <td><input type="number" placeholder="12000"></td>
        </tr>
    </tbody>
</table>

<button onclick="addRow()">➕ أضف عملية جديدة</button>
<button onclick="calculateAll()">✅ احسب الكل</button>
<button onclick="printResults()">🖨️ طباعة PDF</button>

<div id="results"></div>

<script>
function addRow() {
    let tableBody = document.getElementById("dataTable").getElementsByTagName("tbody")[0];
    let newRow = document.createElement("tr");

    const names = ["شركة برسان", "أبو قصي"];

    for (let i = 0; i < 4; i++) {
        let newCell = document.createElement("td");
        let input = document.createElement("input");

        if (i < 2) {
            input.type = "text";
            input.value = names[i];
            input.readOnly = true;
        } else {
            input.type = "number";
            input.placeholder = i === 2 ? "8000" : "12000";
        }

        newCell.appendChild(input);
        newRow.appendChild(newCell);
    }

    tableBody.appendChild(newRow);
}

function calculateAll() {
    let table = document.getElementById("dataTable");
    let results = '';
    let now = new Date().toLocaleString('ar-EG');

    const rows = table.getElementsByTagName("tbody")[0].getElementsByTagName("tr");
    for (let i = 0; i < rows.length; i++) {
        const inputs = rows[i].getElementsByTagName("input");
        let c1 = inputs[0].value;
        let c2 = inputs[1].value;
        let credit = parseFloat(inputs[2].value) || 0;
        let debit = parseFloat(inputs[3].value) || 0;

        results += `📌 عملية ${i + 1} - (${now})
`;
        if (credit > debit) {
            let diff = (credit - debit).toFixed(2);
            results += `${c1} له ${diff} دولار على ${c2}
`;
        } else if (debit > credit) {
            let diff = (debit - credit).toFixed(2);
            results += `${c2} له ${diff} دولار على ${c1}
`;
        } else {
            results += `الرصيد متطابق بين ${c1} و ${c2}
`;
        }
        results += "--------------------------\n";
    }

    document.getElementById("results").innerText = results;

    let whatsappLink = "https://wa.me/?text=" + encodeURIComponent(results);
    document.getElementById("results").innerHTML += `<br><br><a href="${whatsappLink}" target="_blank">مشاركة عبر واتساب</a>`;
}

function printResults() {
    const content = document.getElementById("results").innerHTML;
    const w = window.open('', '', 'width=800,height=600');
    w.document.write(`<html><head><title>نتائج المطابقة</title></head><body dir="rtl" style="font-family: Arial;">${content}</body></html>`);
    w.document.close();
    w.print();
}
</script>

</body>
</html>
