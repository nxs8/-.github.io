# -.github.io
<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حاسبة الأسعار</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            direction: rtl;
            background-color: #000000;
            margin: 0;
            padding: 20px;
            min-height: 100vh;
        }
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
            max-width: 1200px;
            margin: 0 auto;
        }
        .table-container {
            width: 600px; /* عرض ثابت للجدول */
            background: white;
            padding: 15px;
            border-radius: 5px;
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.1);
        }
        table {
            width: 100%;
            border-collapse: separate;
            border-spacing: 0;
            margin-top: 10px;
            border-radius: 5px;
            table-layout: fixed; /* تثبيت عرض الجدول */
        }
        th {
            background-color: #002e20;
            color: white;
            font-size: 20px;
            padding: 12px;
            text-align: center;
            border-radius: 5px 5px 0 0;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            width: 100%; /* رأس الجدول يغطي العرض الكامل */
        }
        td {
            padding: 12px;
            font-size: 18px;
            text-align: center;
            position: relative;
            background-color: white;
            border: 1px solid rgba(0, 46, 32, 0.3);
            color: black;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
            width: 50%; /* كل خلية تأخذ نصف عرض الجدول */
        }
        .highlight {
            background-color: #e0f2f1;
        }
        input[type="text"] {
            width: 100%;
            padding: 5px;
            font-size: 18px;
            text-align: center;
            border: none;
            outline: none;
            background: transparent;
            direction: ltr;
            color: black;
            box-sizing: border-box; /* لضمان عدم تجاوز العرض */
        }
        .copy-btn {
            position: absolute;
            right: 5px;
            top: 50%;
            transform: translateY(-50%);
            background-color: #008CBA;
            color: white;
            border: none;
            padding: 5px 8px;
            cursor: pointer;
            font-size: 14px;
            border-radius: 4px;
            transition: background-color 0.3s;
        }
        .copy-btn:hover {
            background-color: #007bb5;
        }
        .copy-btn.flash {
            background-color: #d9534f !important;
        }
        .clear-btn {
            margin-top: 10px;
            padding: 8px 15px;
            font-size: 16px;
            background-color: #d9534f;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .clear-btn:hover {
            background-color: #c9302c;
        }
        .value-cell {
            background-color: white;
            color: black;
        }
        .value-cell span {
            display: inline-block;
            width: 100%;
            color: black;
        }
        .bold {
            font-weight: bold;
        }
        @media (max-width: 768px) {
            .table-container {
                width: 90%;
                min-width: 300px;
            }
            td {
                font-size: 16px;
            }
        }
    </style>
</head>
<body>

    <div class="container">
        <!-- جدول الحاسبة -->
        <div class="table-container">
            <table>
                <tr><th colspan="2">حاسبة</th></tr> <!-- تغيير colspan إلى 2 -->
                <tr>
                    <td class="bold">السعر شامل الضريبة</td>
                    <td>
                        <input type="text" id="priceWithTax" oninput="calculatePrices()" onwheel="this.blur()">
                    </td>
                </tr>
                <tr class="highlight">
                    <td class="bold">خصم المورد</td>
                    <td>
                        <input type="text" id="discountPercent" oninput="formatPercentage(this); calculatePrices()" maxlength="5" onwheel="this.blur()">
                    </td>
                </tr>
                <tr>
                    <td class="bold">سعر التكلفة بدون الضريبة</td>
                    <td class="value-cell">
                        <span id="costPrice">0.00</span>
                        <button class="copy-btn" onclick="copyToClipboard(this, 'costPrice')">📋</button>
                    </td>
                </tr>
                <tr class="highlight">
                    <td class="bold">سعر البيع بدون الضريبة</td>
                    <td class="value-cell">
                        <span id="salePrice">0.00</span>
                        <button class="copy-btn" onclick="copyToClipboard(this, 'salePrice')">📋</button>
                    </td>
                </tr>
            </table>
            <button class="clear-btn" onclick="clearFields('priceWithTax', 'discountPercent', 'costPrice', 'salePrice')">إفراغ الأرقام</button>
        </div>

        <!-- جدول التخفيض -->
        <div class="table-container">
            <table>
                <tr><th colspan="2">تخفيض</th></tr> <!-- تغيير colspan إلى 2 -->
                <tr>
                    <td class="bold">السعر غير شامل الضريبة</td>
                    <td>
                        <input type="text" id="productPriceWithoutTax" oninput="calculatePrices()" onwheel="this.blur()">
                    </td>
                </tr>
                <tr class="highlight">
                    <td class="bold">نسبة التخفيض</td>
                    <td>
                        <input type="text" id="discountOnProduct" oninput="formatPercentage(this); calculatePrices()" maxlength="5" onwheel="this.blur()">
                    </td>
                </tr>
                <tr>
                    <td class="bold">سعر التخفيض غير شامل الضريبة</td>
                    <td class="value-cell">
                        <span id="discountedPrice">0.00</span>
                        <button class="copy-btn" onclick="copyToClipboard(this, 'discountedPrice')">📋</button>
                    </td>
                </tr>
                <tr class="highlight">
                    <td class="bold">السعر المخفض شامل الضريبة</td>
                    <td class="value-cell">
                        <span id="finalPriceWithTax">0.00</span>
                        <button class="copy-btn" onclick="copyToClipboard(this, 'finalPriceWithTax')">📋</button>
                    </td>
                </tr>
            </table>
            <button class="clear-btn" onclick="clearFields('productPriceWithoutTax', 'discountOnProduct', 'discountedPrice', 'finalPriceWithTax')">إفراغ الأرقام</button>
        </div>
    </div>

    <script>
        function calculatePrices() {
            const taxRate = 15;
            
            const formatValue = (id) => parseFloat(document.getElementById(id).value.replace(/[^0-9.]/g, '')) || 0;
            
            // حاسبة الأسعار
            const priceWithTax = formatValue("priceWithTax");
            const discountPercent = formatValue("discountPercent");
            let priceWithoutTax = priceWithTax / 1.15;
            const discountAmount = priceWithoutTax * (discountPercent / 100);
            const costPrice = priceWithoutTax - discountAmount;
            const salePrice = priceWithoutTax;

            // تخفيض الأسعار
            const productPriceWithoutTax = formatValue("productPriceWithoutTax");
            const discountOnProduct = formatValue("discountOnProduct");
            const discountAmountOnProduct = productPriceWithoutTax * (discountOnProduct / 100);
            const newPriceWithoutTax = productPriceWithoutTax - discountAmountOnProduct;
            const newPriceWithTax = newPriceWithoutTax * 1.15;

            // تحديث النتائج
            updateField("costPrice", costPrice);
            updateField("salePrice", salePrice);
            updateField("discountedPrice", newPriceWithoutTax);
            updateField("finalPriceWithTax", newPriceWithTax);
        }

        function updateField(id, value) {
            const element = document.getElementById(id);
            const copyBtn = element.parentElement.querySelector('.copy-btn');
            
            if (element && copyBtn) {
                element.textContent = value.toString().replace(/(\.\d*?[1-9])0+$/, '$1').replace(/\.$/, '');
                copyBtn.style.display = "inline-block";
            }
        }

        function copyToClipboard(button, elementId) {
            const text = document.getElementById(elementId).textContent;
            
            // تأثير الوميض
            button.classList.add('flash');
            setTimeout(() => button.classList.remove('flash'), 500);
            
            navigator.clipboard.writeText(text);
        }

        function clearFields(...ids) {
            ids.forEach(id => {
                const element = document.getElementById(id);
                if (element) element.value = '';
            });
            calculatePrices();
        }

        function formatPercentage(input) {
            let value = input.value.replace(/[^0-9.]/g, '');
            
            if (value.endsWith('%')) {
                value = value.slice(0, -1);
            }
            
            if (value === '') {
                input.value = '';
                return;
            }
            
            // منع أكثر من فاصلة عشرية
            const parts = value.split('.');
            if (parts.length > 2) {
                value = parts[0] + '.' + parts.slice(1).join('');
            }
            
            input.value = value + '%';
        }
    </script>

</body>
</html>
