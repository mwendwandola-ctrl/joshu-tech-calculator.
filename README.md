<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Joshu>Tech Golden Global Calculator</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #000;
            margin: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .calculator {
            background: linear-gradient(135deg, #bf953f, #fcf6ba, #b38728, #fbf5b7, #aa771c);
            padding: 25px;
            border-radius: 20px;
            box-shadow: 0 0 50px rgba(212, 175, 55, 0.4);
            width: 360px;
            border: 2px solid #846721;
        }

        #display {
            width: 100%;
            height: 65px;
            background: #000;
            color: #ffd700;
            border: 2px solid #5d4410;
            border-radius: 10px;
            margin-bottom: 15px;
            font-size: 26px;
            text-align: right;
            padding: 10px;
            box-sizing: border-box;
            box-shadow: inset 0 0 10px #0f0f00;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 8px;
        }

        button {
            padding: 15px;
            background: #111;
            color: #fcf6ba;
            border: 1px solid #444;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            font-size: 14px;
            transition: 0.2s;
        }

        button:hover { background: #222; border-color: #ffd700; transform: scale(1.05); }
        button:active { transform: scale(0.95); }
        .equals { background: #d4af37; color: #000; border: none; }
        .clear { background: #700; color: #fff; border: none; }

        .currency-zone {
            margin-top: 15px;
            background: rgba(0,0,0,0.3);
            padding: 15px;
            border-radius: 12px;
            text-align: center;
            border: 1px solid #846721;
        }

        #currency-code {
            width: 80px;
            padding: 8px;
            border-radius: 5px;
            border: 1px solid #846721;
            background: #000;
            color: #ffd700;
            text-transform: uppercase;
            text-align: center;
        }

        .convert-btn {
            background: #846721;
            color: #000;
            padding: 8px 15px;
            margin-left: 5px;
        }

        .signature {
            margin-top: 25px;
            color: #d4af37;
            font-weight: bold;
            text-align: center;
            font-size: 14px;
            letter-spacing: 1px;
            text-shadow: 0 0 8px rgba(212, 175, 55, 0.6);
        }

        .phone-link {
            color: #ffd700;
            text-decoration: none;
            display: block;
            margin-top: 5px;
        }
    </style>
</head>
<body>

    <div class="calculator">
        <input type="text" id="display" readonly placeholder="0">
        
        <div class="grid">
            <button>sin</button><button>cos</button><button>sqrt</button><button>/</button>
            <button>7</button><button>8</button><button>9</button><button>*</button>
            <button>4</button><button>5</button><button>6</button><button>-</button>
            <button>1</button><button>2</button><button>3</button><button>+</button>
            <button>0</button><button>.</button><button class="clear">C</button><button class="equals">=</button>
        </div>

        <div class="currency-zone">
            <div style="font-size: 11px; color: #fcf6ba; margin-bottom: 8px;">CONVERT KES TO ANY CURRENCY</div>
            <input type="text" id="currency-code" placeholder="USD" maxlength="3">
            <button class="convert-btn" onclick="convertCurrency()">CONVERT</button>
            <p id="rate-info" style="font-size: 10px; color: #fcf6ba; margin-top: 8px; opacity: 0.8;"></p>
        </div>
    </div>

    <div class="signature">
        (Joshu&gt;Tech)
        <a href="tel:+254700723129" class="phone-link">+254 700 723 129</a>
    </div>

    <script>
        const display = document.getElementById('display');
        const buttons = document.querySelectorAll('.grid button');

        // Normal Calculator Logic
        buttons.forEach(btn => {
            btn.addEventListener('click', () => {
                const val = btn.innerText;
                if (val === 'C') {
                    display.value = '';
                } else if (val === '=') {
                    try {
                        let eq = display.value
                            .replace(/sin/g, 'Math.sin')
                            .replace(/cos/g, 'Math.cos')
                            .replace(/sqrt/g, 'Math.sqrt');
                        display.value = eval(eq);
                    } catch { display.value = "Error"; }
                } else {
                    display.value += val;
                }
            });
        });

        // Professional Live Currency Logic
        async function convertCurrency() {
            const targetCode = document.getElementById('currency-code').value.toUpperCase();
            const amount = parseFloat(display.value);

            if (isNaN(amount)) {
                alert("Please type a number in KES first!");
                return;
            }

            try {
                // Fetching real-time rates from KES (Kenyan Shillings)
                const response = await fetch(`https://open.er-api.com/v6/latest/KES`);
                const data = await response.json();
                
                if (data.rates && data.rates[targetCode]) {
                    const rate = data.rates[targetCode];
                    const converted = (amount * rate).toFixed(2);
                    display.value = `${converted} ${targetCode}`;
                    document.getElementById('rate-info').innerText = `Live Rate: 1 KES = ${rate.toFixed(4)} ${targetCode}`;
                } else {
                    alert("Currency code not found. Use USD, EUR, GBP, etc.");
                }
            } catch (error) {
                alert("Connection error. Check your internet!");
            }
        }
    </script>
</body>
</html>
