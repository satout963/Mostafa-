
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>مطابقة جماعية للأرصدة</title>
  <style>
    body { font-family: Arial; padding: 20px; background-color: #f4f4f4; }
    .logo-container { text-align: right; margin-bottom: 20px; }
    .logo-container img { width: 120px; }
    table { width: 100%; border-collapse: collapse; background: #fff; margin-top: 20px; }
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
      <th>الاسم</th>
      <th>الطرف الأول</th>
      <th>الطرف الثاني</th>
      <th>دائن</th>
      <th>أجور</th>
      <th>مدين</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><input type="text" placeholder="اسم العملية أو الشخص"></td>
      <td><input type="text" value="شركة برسان" readonly></td>
      <td><input type="text" value="شركة الأمين" readonly></td>
      <td><input type="number" placeholder="8000"></td>
      <td><input type="number" placeholder="0"></td>
      <td><input type="number" placeholder="12000"></td>
    </tr>
  </tbody>
</table>

<p style="margin-top: 15px; font-size: 14px; color: #555;">
  ملاحظة: <strong>الدائن</strong> هو من له مبلغ على الطرف الآخر، و<strong>المدين</strong> هو من عليه دفع ذلك المبلغ.
</p>

<button onclick="addRow()">➕ أضف عملية جديدة</button>
<button onclick="calculateAll()">✅ احسب الكل</button>
<button onclick="printResults()">🖨️ طباعة PDF</button>
<button onclick="downloadPDF()">⬇️ تنزيل PDF</button>

<div style="margin-top: 20px; font-size: 15px;" id="totalsArea">
  <strong>مجموع الدائن مع الأجور:</strong> <span id="totalCredit">0</span> دولار<br>
  <strong>مجموع المدين:</strong> <span id="totalDebit">0</span> دولار
</div>

<div id="results"></div>

<script>
function addRow() {
  let tableBody = document.getElementById("dataTable").getElementsByTagName("tbody")[0];
  let newRow = document.createElement("tr");

  const names = ["", "شركة برسان", "شركة الأمين"];

  for (let i = 0; i < 6; i++) {
    let newCell = document.createElement("td");
    let input = document.createElement("input");

    if (i === 0) {
      input.type = "text";
      input.placeholder = "اسم العملية أو الشخص";
    } else if (i === 1 || i === 2) {
      input.type = "text";
      input.value = names[i];
      input.readOnly = true;
    } else {
      input.type = "number";
      input.placeholder = i === 3 ? "8000" : i === 4 ? "0" : "12000";
    }

    newCell.appendChild(input);
    newRow.appendChild(newCell);
  }

  tableBody.appendChild(newRow);
}

function calculateAll() {
  let table = document.getElementById("dataTable");
  let results = '';
  let styledResults = '';
  let now = new Date().toLocaleString('ar-EG');
  let totalCreditSum = 0, totalDebitSum = 0;

  const rows = table.getElementsByTagName("tbody")[0].getElementsByTagName("tr");
  for (let i = 0; i < rows.length; i++) {
    const inputs = rows[i].getElementsByTagName("input");
    let name = inputs[0].value || `عملية ${i + 1}`;
    let c1 = inputs[1].value;
    let c2 = inputs[2].value;
    let credit = parseFloat(inputs[3].value) || 0;
    let fees = parseFloat(inputs[4].value) || 0;
    let debit = parseFloat(inputs[5].value) || 0;
    let totalCredit = credit + fees;

    totalCreditSum += totalCredit;
    totalDebitSum += debit;

    results += `📌 ${name} - (${now})\n`;
    styledResults += `<div style="margin-bottom:10px;"><strong>📌 ${name}</strong> - (${now})<br>`;

    if (totalCredit > debit) {
      let diff = (totalCredit - debit).toFixed(2);
      results += `${c1} له ${diff} دولار على ${c2}\n`;
      styledResults += `<span style="color: green;">${c1} له ${diff} دولار على ${c2}</span><br>`;
    } else if (debit > totalCredit) {
      let diff = (debit - totalCredit).toFixed(2);
      results += `${c2} له ${diff} دولار على ${c1}\n`;
      styledResults += `<span style="color: red;">${c2} له ${diff} دولار على ${c1}</span><br>`;
    } else {
      results += `الرصيد متطابق بين ${c1} و ${c2}\n`;
      styledResults += `<span style="color: gray;">الرصيد متطابق بين ${c1} و ${c2}</span><br>`;
    }

    results += "--------------------------\n";
    styledResults += `<hr></div>`;
  }

  document.getElementById("results").innerHTML = styledResults;
  document.getElementById("totalCredit").innerText = totalCreditSum.toFixed(2);
  document.getElementById("totalDebit").innerText = totalDebitSum.toFixed(2);

  let whatsappLink = "https://wa.me/?text=" + encodeURIComponent(results);
  document.getElementById("results").innerHTML += `<br><a href="${whatsappLink}" target="_blank">مشاركة عبر واتساب</a>`;
}

function printResults() {
  const content = document.getElementById("results").innerHTML;
  const w = window.open('', '', 'width=800,height=600');
  w.document.write(`<html><head><title>نتائج المطابقة</title></head><body dir="rtl" style="font-family: Arial;">${content}</body></html>`);
  w.document.close();
  w.print();
}

function downloadPDF() {
  const text = document.getElementById("results").innerText;
  const blob = new Blob([text], { type: 'application/pdf' });
  const link = document.createElement('a');
  link.href = URL.createObjectURL(blob);
  link.download = "مطابقة_أرصدة.pdf";
  link.click();
}
</script>

</body>
</html>
