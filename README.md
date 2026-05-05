<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CrispyMax LED Error Diagnostic</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;800&family=IBM+Plex+Mono:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --purple-primary: #5B2C91;
            --purple-dark: #3d1d5f;
            --purple-light: #7b4db8;
            --gray-dark: #4a5568;
            --gray-med: #6b7280;
            --gray-light: #9ca3af;
            --bg-light: #f8f9fa;
            --bg-white: #ffffff;
            --text-dark: #1a202c;
            --text-light: #4a5568;
            --accent-amber: #ff9500;
            --accent-red: #ff3b30;
            --border: #e2e8f0;
            --success: #10b981;
        }

        body {
            font-family: 'IBM Plex Mono', monospace;
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            color: var(--text-dark);
            min-height: 100vh;
            padding: 2rem 1rem;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        .header {
            text-align: center;
            margin-bottom: 2rem;
            animation: slideDown 0.8s cubic-bezier(0.16, 1, 0.3, 1);
            padding: 0 1rem;
        }

        @keyframes slideDown {
            from {
                opacity: 0;
                transform: translateY(-30px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        h1 {
            font-family: 'Outfit', sans-serif;
            font-weight: 800;
            font-size: clamp(1.5rem, 5vw, 2.5rem);
            color: var(--text-dark);
            margin-bottom: 0.75rem;
            line-height: 1.2;
        }

        .subtitle {
            font-family: 'IBM Plex Mono', monospace;
            color: var(--gray-med);
            font-size: clamp(0.75rem, 2.5vw, 1rem);
            font-weight: 400;
            letter-spacing: 0.05em;
        }

        .diagnostic-panel {
            background: var(--bg-white);
            border: 2px solid var(--border);
            border-radius: 16px;
            padding: clamp(1.25rem, 4vw, 2.5rem);
            margin-bottom: 2rem;
            box-shadow: 
                0 10px 40px rgba(91, 44, 145, 0.08),
                0 2px 8px rgba(0, 0, 0, 0.04);
            position: relative;
            overflow: hidden;
            animation: fadeInScale 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.2s backwards;
        }

        @keyframes fadeInScale {
            from {
                opacity: 0;
                transform: scale(0.95);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }

        .diagnostic-panel::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 5px;
            background: linear-gradient(90deg, 
                var(--purple-primary) 0%, 
                var(--purple-light) 50%, 
                var(--purple-primary) 100%);
            background-size: 200% 100%;
            animation: shimmer 4s linear infinite;
        }

        @keyframes shimmer {
            to {
                background-position: 200% 0;
            }
        }

        .section-title {
            font-family: 'Outfit', sans-serif;
            font-size: clamp(0.875rem, 2.5vw, 1rem);
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            color: var(--purple-primary);
            margin-bottom: 1.25rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        .section-title::before {
            content: '';
            width: 4px;
            height: 20px;
            background: var(--purple-primary);
            border-radius: 2px;
            flex-shrink: 0;
        }

        .input-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(min(100%, 280px), 1fr));
            gap: 1.25rem;
            margin-bottom: 1.5rem;
        }

        .input-group {
            position: relative;
        }

        .input-label {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            font-size: clamp(0.75rem, 2vw, 0.875rem);
            font-weight: 600;
            margin-bottom: 0.625rem;
            color: var(--text-dark);
            text-transform: uppercase;
            letter-spacing: 0.05em;
        }

        .led-indicator {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            animation: pulse 2s ease-in-out infinite;
            flex-shrink: 0;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.6; transform: scale(0.95); }
        }

        .led-amber {
            background: var(--accent-amber);
            box-shadow: 0 0 8px rgba(255, 149, 0, 0.6);
        }

        .led-red {
            background: var(--accent-red);
            box-shadow: 0 0 8px rgba(255, 59, 48, 0.6);
        }

        select {
            width: 100%;
            padding: clamp(0.875rem, 2.5vw, 1rem) 1rem;
            font-family: 'IBM Plex Mono', monospace;
            font-size: clamp(1rem, 2.5vw, 1.125rem);
            font-weight: 600;
            background: var(--bg-light);
            color: var(--text-dark);
            border: 2px solid var(--border);
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
            appearance: none;
            background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24' viewBox='0 0 24 24' fill='none' stroke='%235B2C91' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpolyline points='6 9 12 15 18 9'%3E%3C/polyline%3E%3C/svg%3E");
            background-repeat: no-repeat;
            background-position: right 0.875rem center;
            background-size: 20px;
            padding-right: 2.75rem;
        }

        select:hover {
            border-color: var(--purple-primary);
            background: white;
        }

        select:focus {
            outline: none;
            border-color: var(--purple-primary);
            box-shadow: 0 0 0 4px rgba(91, 44, 145, 0.1);
            background: white;
        }

        .key-display {
            background: linear-gradient(135deg, 
                rgba(91, 44, 145, 0.05) 0%, 
                rgba(123, 77, 184, 0.05) 100%);
            border: 3px solid var(--purple-primary);
            border-radius: 12px;
            padding: clamp(1.25rem, 4vw, 2rem);
            text-align: center;
            margin-bottom: 1.5rem;
            position: relative;
            overflow: hidden;
        }

        .key-label {
            font-family: 'Outfit', sans-serif;
            font-size: clamp(0.625rem, 2vw, 0.75rem);
            text-transform: uppercase;
            letter-spacing: 0.15em;
            color: var(--gray-med);
            margin-bottom: 0.625rem;
            font-weight: 600;
        }

        .key-value {
            font-family: 'Outfit', sans-serif;
            font-size: clamp(1.75rem, 6vw, 2.5rem);
            font-weight: 800;
            color: var(--purple-primary);
            letter-spacing: 0.15em;
        }

        .results-grid {
            display: grid;
            gap: 1rem;
        }

        .result-item {
            background: var(--bg-light);
            border: 2px solid var(--border);
            border-left: 4px solid var(--purple-primary);
            border-radius: 10px;
            padding: clamp(1rem, 3vw, 1.25rem) clamp(1.25rem, 3.5vw, 1.5rem);
            transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);
            animation: slideInRight 0.5s cubic-bezier(0.16, 1, 0.3, 1) backwards;
        }

        @keyframes slideInRight {
            from {
                opacity: 0;
                transform: translateX(20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        .result-item:nth-child(1) { animation-delay: 0.1s; }
        .result-item:nth-child(2) { animation-delay: 0.15s; }
        .result-item:nth-child(3) { animation-delay: 0.2s; }
        .result-item:nth-child(4) { animation-delay: 0.25s; }
        .result-item:nth-child(5) { animation-delay: 0.3s; }

        .result-item:hover {
            transform: translateX(4px);
            border-left-width: 6px;
            background: white;
            box-shadow: 0 4px 12px rgba(91, 44, 145, 0.15);
        }

        .result-label {
            font-family: 'Outfit', sans-serif;
            font-size: clamp(0.625rem, 2vw, 0.75rem);
            text-transform: uppercase;
            letter-spacing: 0.1em;
            color: var(--gray-med);
            margin-bottom: 0.5rem;
            font-weight: 600;
        }

        .result-value {
            font-size: clamp(0.875rem, 2.5vw, 1rem);
            color: var(--text-dark);
            font-weight: 400;
            line-height: 1.6;
            word-wrap: break-word;
        }

        .result-value.highlight {
            font-weight: 600;
            color: var(--purple-primary);
        }

        .reference-table {
            background: var(--bg-white);
            border: 2px solid var(--border);
            border-radius: 16px;
            padding: clamp(1.25rem, 4vw, 2.5rem);
            margin-top: 2rem;
            box-shadow: 
                0 10px 40px rgba(91, 44, 145, 0.08),
                0 2px 8px rgba(0, 0, 0, 0.04);
            animation: fadeInScale 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.4s backwards;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            font-size: clamp(0.75rem, 2vw, 0.875rem);
        }

        thead {
            background: linear-gradient(135deg, 
                rgba(91, 44, 145, 0.08) 0%, 
                rgba(123, 77, 184, 0.08) 100%);
            border-bottom: 2px solid var(--purple-primary);
        }

        th {
            font-family: 'Outfit', sans-serif;
            font-weight: 800;
            text-transform: uppercase;
            letter-spacing: 0.05em;
            font-size: clamp(0.625rem, 1.75vw, 0.75rem);
            color: var(--purple-primary);
            padding: clamp(0.75rem, 2.5vw, 1.25rem) clamp(0.5rem, 2vw, 1rem);
            text-align: left;
        }

        td {
            padding: clamp(0.75rem, 2.5vw, 1.25rem) clamp(0.5rem, 2vw, 1rem);
            border-bottom: 1px solid var(--border);
            color: var(--text-light);
            word-wrap: break-word;
        }

        tbody tr {
            transition: all 0.2s ease;
        }

        tbody tr:hover {
            background: var(--bg-light);
        }

        tbody tr.matched {
            background: linear-gradient(135deg, 
                rgba(91, 44, 145, 0.12) 0%, 
                rgba(123, 77, 184, 0.12) 100%);
            border-left: 4px solid var(--purple-primary);
        }

        tbody tr.matched td {
            color: var(--text-dark);
            font-weight: 600;
        }

        .key-cell {
            font-family: 'Outfit', sans-serif;
            font-weight: 800;
            color: var(--purple-primary);
            font-size: clamp(0.875rem, 2.5vw, 1rem);
        }

        .no-match {
            text-align: center;
            padding: clamp(2rem, 5vw, 3rem) 1.5rem;
            color: var(--gray-med);
            font-style: italic;
            font-size: clamp(0.875rem, 2.5vw, 1rem);
        }

        @media (max-width: 640px) {
            body {
                padding: 1rem 0.75rem;
            }

            .diagnostic-panel::before {
                height: 3px;
            }

            .input-grid {
                gap: 1rem;
            }

            .key-display {
                border-width: 2px;
            }

            .result-item {
                border-left-width: 3px;
            }

            .result-item:hover {
                transform: translateX(2px);
                border-left-width: 4px;
            }

            /* Make table scrollable on small screens */
            .reference-table {
                overflow-x: auto;
                -webkit-overflow-scrolling: touch;
            }

            table {
                min-width: 600px;
            }
        }

        .footer {
            text-align: center;
            margin-top: 2rem;
            padding: 1.5rem;
            color: var(--gray-light);
            font-size: clamp(0.75rem, 2vw, 0.875rem);
        }
    </style>
</head>
<body>
    <div class="container">
        <header class="header">
            <h1>CrispyMax LED Error Diagnostic</h1>
            <p class="subtitle">Troubleshooting System</p>
        </header>

        <div class="diagnostic-panel">
            <h2 class="section-title">
                <span>Input Flash Counts</span>
            </h2>

            <div class="input-grid">
                <div class="input-group">
                    <label class="input-label">
                        <span class="led-indicator led-amber"></span>
                        Amber Flashes (Identifies Lane)
                    </label>
                    <select id="amberFlashes">
                        <option value="">-- Select --</option>
                        <option value="0">0</option>
                        <option value="1">1</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                    </select>
                </div>

                <div class="input-group">
                    <label class="input-label">
                        <span class="led-indicator led-red"></span>
                        Red Flashes
                    </label>
                    <select id="redFlashes">
                        <option value="">-- Select --</option>
                        <option value="2">2</option>
                        <option value="3">3</option>
                        <option value="4">4</option>
                        <option value="5">5 (No Amber Flashes)</option>
                        <option value="7">7 (No Amber Flashes)</option>
                    </select>
                </div>
            </div>

            <div class="key-display">
                <div class="key-label">Generated Error Key</div>
                <div class="key-value" id="errorKey">--</div>
            </div>

            <h2 class="section-title">
                <span>Diagnostic Results</span>
            </h2>

            <div class="results-grid" id="results">
                <div class="no-match">Select flash counts above to view diagnostic information</div>
            </div>
        </div>

        <div class="reference-table">
            <h2 class="section-title">
                <span>Quick Reference — All Error Codes</span>
            </h2>
            <div style="overflow-x: auto;">
                <table id="referenceTable">
                    <thead>
                        <tr>
                            <th>Key</th>
                            <th>Zone</th>
                            <th>Error Type</th>
                            <th>Condition</th>
                            <th>Service Action</th>
                        </tr>
                    </thead>
                    <tbody id="referenceBody"></tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        const errorDatabase = [
            { key: "1-2", zone: "Zone 1", type: "Temp HIGH or LOW", condition: "More than 25°F from setpoint", cause: "Heater / AC Harness / Control Board", action: "Check heater, harness, board" },
            { key: "2-2", zone: "Zone 2", type: "Temp HIGH or LOW", condition: "More than 25°F from setpoint", cause: "Heater / AC Harness / Control Board", action: "Check heater, harness, board" },
            { key: "3-2", zone: "Zone 3", type: "Temp HIGH or LOW", condition: "More than 25°F from setpoint", cause: "Heater / AC Harness / Control Board", action: "Check heater, harness, board" },
            { key: "4-2", zone: "Zone 4", type: "Temp HIGH or LOW", condition: "More than 25°F from setpoint", cause: "Heater / AC Harness / Control Board", action: "Check heater, harness, board" },
            { key: "1-3", zone: "Zone 1", type: "Thermocouple", condition: "Open thermocouple", cause: "Thermocouple / Control Board", action: "Check thermocouple" },
            { key: "2-3", zone: "Zone 2", type: "Thermocouple", condition: "Open thermocouple", cause: "Thermocouple / Control Board", action: "Check thermocouple" },
            { key: "3-3", zone: "Zone 3", type: "Thermocouple", condition: "Open thermocouple", cause: "Thermocouple / Control Board", action: "Check thermocouple" },
            { key: "4-3", zone: "Zone 4", type: "Thermocouple", condition: "Open thermocouple", cause: "Thermocouple / Control Board", action: "Check thermocouple" },
            { key: "1-4", zone: "Zone 1", type: "Fan Speed HIGH or LOW", condition: "Fan speed out of range", cause: "Fan / Control Board", action: "Check fan" },
            { key: "2-4", zone: "Zone 2", type: "Fan Speed HIGH or LOW", condition: "Fan speed out of range", cause: "Fan / Control Board", action: "Check fan" },
            { key: "3-4", zone: "Zone 3", type: "Fan Speed HIGH or LOW", condition: "Fan speed out of range", cause: "Fan / Control Board", action: "Check fan" },
            { key: "4-4", zone: "Zone 4", type: "Fan Speed HIGH or LOW", condition: "Fan speed out of range", cause: "Fan / Control Board", action: "Check fan" },
            { key: "0-5", zone: "—", type: "Comm Error", condition: "Board 2 communication failure", cause: "DC Cable / Control Board", action: "Check DC cable" },
            { key: "0-7", zone: "—", type: "Overtemp", condition: "Compartment too hot", cause: "Control Board Overheating", action: "Check airflow" }
        ];

        const amberSelect = document.getElementById('amberFlashes');
        const redSelect = document.getElementById('redFlashes');
        const errorKeyEl = document.getElementById('errorKey');
        const resultsEl = document.getElementById('results');
        const referenceBody = document.getElementById('referenceBody');

        function updateDiagnostic() {
            const amber = amberSelect.value;
            const red = redSelect.value;

            if (!amber || !red) {
                errorKeyEl.textContent = '--';
                resultsEl.innerHTML = '<div class="no-match">Select flash counts above to view diagnostic information</div>';
                updateReferenceTable(null);
                return;
            }

            const key = `${amber}-${red}`;
            errorKeyEl.textContent = key;

            const match = errorDatabase.find(e => e.key === key);

            if (match) {
                resultsEl.innerHTML = `
                    <div class="result-item">
                        <div class="result-label">Zone</div>
                        <div class="result-value">${match.zone}</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Error Type</div>
                        <div class="result-value highlight">${match.type}</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Condition</div>
                        <div class="result-value">${match.condition}</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Probable Cause</div>
                        <div class="result-value">${match.cause}</div>
                    </div>
                    <div class="result-item">
                        <div class="result-label">Service Action</div>
                        <div class="result-value highlight">${match.action}</div>
                    </div>
                `;
            } else {
                resultsEl.innerHTML = '<div class="no-match">⚠ No match found — verify flash counts</div>';
            }

            updateReferenceTable(key);
        }

        function updateReferenceTable(matchedKey) {
            referenceBody.innerHTML = errorDatabase.map(err => `
                <tr class="${err.key === matchedKey ? 'matched' : ''}">
                    <td class="key-cell">${err.key}</td>
                    <td>${err.zone}</td>
                    <td>${err.type}</td>
                    <td>${err.condition}</td>
                    <td>${err.action}</td>
                </tr>
            `).join('');
        }

        amberSelect.addEventListener('change', updateDiagnostic);
        redSelect.addEventListener('change', updateDiagnostic);

        // Initialize reference table
        updateReferenceTable(null);
    </script>
</body>
</html>
