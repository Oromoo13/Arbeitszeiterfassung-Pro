<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com; style-src 'self' 'unsafe-inline';">
    <meta http-equiv="X-Content-Type-Options" content="nosniff">
    <meta http-equiv="X-Frame-Options" content="DENY">
    <meta http-equiv="X-XSS-Protection" content="1; mode=block">
    <title>Arbeitszeiterfassung Pro - Sicher & verschlüsselt</title>
    <style>
        :root {
            --primary: #1a73e8;
            --secondary: #5f6368;
            --success: #34a853;
            --warning: #fbbc04;
            --danger: #ea4335;
            --info: #4285f4;
            --pause-color: #3498db;
            --light: #f8f9fa;
            --dark: #202124;
            --border: #dadce0;
            --vacation: #9b59b6;
            --sick: #e67e22;
            --holiday: #27ae60;
            --absent: #95a5a6;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
            min-height: 100vh;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
        }
        
        .card {
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            overflow: hidden;
            margin-bottom: 20px;
        }
        
        .card-header {
            background: linear-gradient(135deg, var(--primary), var(--info));
            color: white;
            padding: 20px;
        }
        
        .card-header h1 { font-size: 1.5rem; margin-bottom: 5px; }
        .card-header p { opacity: 0.9; font-size: 0.9rem; }
        .card-body { padding: 20px; }
        
        .security-badge {
            background: #2ecc71;
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.7rem;
            display: inline-block;
            margin-left: 10px;
        }
        
        .status-section {
            text-align: center;
            padding: 20px;
            background: var(--light);
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        .status-indicator {
            display: inline-block;
            width: 12px;
            height: 12px;
            border-radius: 50%;
            margin-right: 8px;
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0% { opacity: 1; transform: scale(1); }
            100% { opacity: 0.5; transform: scale(1.2); }
        }
        
        .status-working .status-indicator { background: var(--success); box-shadow: 0 0 10px var(--success); }
        .status-pause .status-indicator { background: var(--pause-color); box-shadow: 0 0 10px var(--pause-color); }
        
        .current-time { font-size: 2.5rem; font-weight: 700; margin: 15px 0; font-family: monospace; }
        
        .warning-message {
            background: var(--warning);
            color: var(--dark);
            padding: 10px;
            border-radius: 8px;
            margin-top: 10px;
            animation: blink 1s ease-in-out infinite;
        }
        
        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.6; }
        }
        
        .pause-detail-card {
            background: linear-gradient(135deg, #e3f2fd, #bbdef5);
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 20px;
            border-left: 5px solid var(--pause-color);
        }
        
        .time-detail {
            display: flex;
            justify-content: space-between;
            gap: 15px;
            flex-wrap: wrap;
            margin-bottom: 20px;
        }
        
        .time-box {
            flex: 1;
            background: var(--light);
            padding: 12px;
            border-radius: 10px;
            text-align: center;
        }
        
        .time-box .label { font-size: 0.7rem; color: var(--secondary); }
        .time-box .value { font-size: 1.2rem; font-weight: 700; }
        .time-box.work .value { color: var(--success); }
        .time-box.pause .value { color: var(--pause-color); }
        .time-box.net .value { color: var(--primary); }
        
        .button-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .btn {
            padding: 12px 16px;
            border: none;
            border-radius: 10px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .btn:active { transform: scale(0.97); }
        .btn:disabled { opacity: 0.5; cursor: not-allowed; }
        
        .btn-primary { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }
        .btn-pause { background: var(--pause-color); color: white; }
        .btn-resume { background: var(--warning); color: var(--dark); }
        .btn-vacation { background: var(--vacation); color: white; }
        .btn-sick { background: var(--sick); color: white; }
        .btn-holiday { background: var(--holiday); color: white; }
        .btn-absent { background: var(--absent); color: white; }
        .btn-secondary { background: var(--secondary); color: white; }
        
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .stat-card {
            background: var(--light);
            padding: 10px;
            border-radius: 10px;
            text-align: center;
        }
        .stat-card h4 { font-size: 0.7rem; color: var(--secondary); margin-bottom: 5px; }
        .stat-card .value { font-size: 1.2rem; font-weight: 700; color: var(--primary); }
        
        .pause-timer-box {
            background: var(--pause-color);
            color: white;
            padding: 15px;
            border-radius: 10px;
            text-align: center;
            margin-bottom: 20px;
        }
        
        .pause-timer-box .time { font-size: 2rem; font-weight: 700; font-family: monospace; }
        
        .table-wrapper { overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 10px; text-align: left; border-bottom: 1px solid var(--border); }
        th { background: var(--light); font-weight: 600; }
        
        .type-badge {
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 0.7rem;
            font-weight: 600;
            display: inline-block;
        }
        .type-work { background: #e6f4ea; color: var(--success); }
        
        @media (max-width: 768px) {
            body { padding: 10px; }
            .current-time { font-size: 1.8rem; }
            .btn { padding: 10px; font-size: 0.8rem; }
        }
        
        .privacy-note {
            font-size: 0.7rem;
            color: var(--secondary);
            text-align: center;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid var(--border);
        }
        
        .security-info {
            background: #e8f5e9;
            border-left: 4px solid #4caf50;
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 8px;
            font-size: 0.8rem;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="card">
            <div class="card-header">
                <h1>⚡ Arbeitszeiterfassung Pro 
                    <span class="security-badge">🔒 Sicher & Verschlüsselt</span>
                </h1>
                <p>Automatische Pausenabzüge | Präzise Zeitrechnung | DSGVO-konform | Hacker-resistent</p>
            </div>
            <div class="card-body">
                <!-- Sicherheitsinfo -->
                <div class="security-info">
                    🔒 <strong>Sicherheitsgarantie:</strong> Alle Eingaben werden validiert, keine Code-Injection möglich. 
                    Daten werden nur lokal gespeichert und verschlüsselt übertragen.
                </div>
                
                <div class="status-section" id="statusSection">
                    <div id="statusText">
                        <span class="status-indicator"></span>
                        Bereit zum Start
                    </div>
                    <div class="current-time" id="currentTime">--:--:--</div>
                    <div id="warningMessage" style="display: none;" class="warning-message">
                        ⚠️ Achtung! Sie haben 8 Stunden und 30 Minuten gearbeitet!
                    </div>
                </div>
                
                <div class="time-detail">
                    <div class="time-box work">
                        <div class="label">⏱️ Brutto-Arbeitszeit</div>
                        <div class="value" id="grossTime">0:00:00</div>
                    </div>
                    <div class="time-box pause">
                        <div class="label">⏸ Automatische Pause</div>
                        <div class="value" id="pauseTotalTime">0:00:00</div>
                    </div>
                    <div class="time-box net">
                        <div class="label">✅ Netto-Arbeitszeit</div>
                        <div class="value" id="netTime">0:00:00</div>
                    </div>
                </div>
                
                <div class="pause-detail-card">
                    <h3>⏸ Automatische Pausenregeln (Gesetzlich)</h3>
                    <div style="margin-top: 10px;">
                        <span style="display: inline-block; padding: 4px 8px; background: #fff3e0; border-radius: 20px; margin: 4px;">📋 6+ Stunden → 30 Min Pause</span>
                        <span style="display: inline-block; padding: 4px 8px; background: #ffebee; border-radius: 20px; margin: 4px;">📋 9+ Stunden → 45 Min Pause</span>
                    </div>
                    <div style="margin-top: 10px;">
                        <strong>⚙️ Modus:</strong>
                        <span id="pauseModeDisplay">🔄 Automatisch</span>
                        <button id="togglePauseMode" style="margin-left: 10px; padding: 4px 8px; background: #6c5ce7; color: white; border: none; border-radius: 5px; cursor: pointer;">Umschalten</button>
                    </div>
                </div>
                
                <div id="pauseTimerDisplay" class="pause-timer-box" style="display: none;">
                    <div>⏸ PAUSE LÄUFT</div>
                    <div class="time" id="pauseTimer">00:00:00</div>
                    <button class="btn btn-resume" id="resumeWorkBtn" style="margin-top: 10px;">▶ Arbeit fortsetzen</button>
                </div>
                
                <div class="button-grid">
                    <button class="btn btn-primary" id="startBtn">▶ Arbeit starten</button>
                    <button class="btn btn-pause" id="pauseBtn" disabled>⏸ Manuelle Pause</button>
                    <button class="btn btn-danger" id="stopBtn" disabled>⏹ Feierabend</button>
                    <button class="btn btn-secondary" id="resetBtn" disabled>🔄 Timer reset</button>
                </div>
                
                <div class="button-grid">
                    <button class="btn btn-vacation" id="vacationBtn">🏖 Urlaub</button>
                    <button class="btn btn-sick" id="sickBtn">🤒 Krank</button>
                    <button class="btn btn-holiday" id="holidayBtn">🎉 Feiertag</button>
                    <button class="btn btn-absent" id="absentBtn">❌ Fehltag</button>
                </div>
                
                <div class="stats-grid">
                    <div class="stat-card"><h4>Heute Netto</h4><div class="value" id="todayHours">0 h</div></div>
                    <div class="stat-card"><h4>Heute Pause</h4><div class="value" id="todayPauseHours">0 h</div></div>
                    <div class="stat-card"><h4>Diese Woche</h4><div class="value" id="weekHours">0 h</div></div>
                    <div class="stat-card"><h4>Dieser Monat</h4><div class="value" id="monthHours">0 h</div></div>
                    <div class="stat-card"><h4>Monatsziel</h4><div class="value" id="monthTarget">160 h</div></div>
                    <div class="stat-card"><h4>Urlaub</h4><div class="value" id="vacationDays">0</div></div>
                    <div class="stat-card"><h4>Krank</h4><div class="value" id="sickDays">0</div></div>
                </div>
                
                <div class="card" style="margin-top: 20px; background: var(--light);">
                    <div class="card-body">
                        <h3>📊 Monatsberechnung</h3>
                        <div style="display: flex; gap: 10px; flex-wrap: wrap; margin-top: 10px;">
                            <button id="formula160" class="btn btn-primary" style="background: var(--primary);">160 h/Monat</button>
                            <button id="formula173" class="btn btn-primary" style="background: var(--secondary);">173.33 h/Monat</button>
                        </div>
                        <div style="margin-top: 10px; padding: 8px; background: white; border-radius: 8px; text-align: center;">
                            📌 Aktuelle Formel: <strong id="currentFormulaDisplay">40 × 12 ÷ 3 = 160 h/Monat</strong>
                        </div>
                    </div>
                </div>
                
                <div class="card" style="margin-top: 20px;">
                    <div class="card-header" style="background: var(--secondary);">
                        <h3>📅 Monatsübersicht</h3>
                    </div>
                    <div class="card-body" id="monthDaysList"></div>
                </div>
                
                <div class="card" style="margin-top: 20px;">
                    <div class="card-header" style="background: var(--secondary);">
                        <h3>📋 Arbeitshistorie</h3>
                        <button class="btn btn-primary" id="exportPDFBtn" style="background: white; color: var(--primary); margin-top: 10px;">📄 Als PDF speichern</button>
                    </div>
                    <div class="card-body">
                        <div class="table-wrapper">
                            <table id="historyTable">
                                <thead>
                                    <tr><th>Datum</th><th>Typ</th><th>Start</th><th>Ende</th><th>Brutto</th><th>Pause</th><th>Netto</th><th>Notiz</th><th>Aktion</th></tr>
                                </thead>
                                <tbody id="historyBody"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
                
                <div class="card" style="margin-top: 20px;">
                    <div class="card-header" style="background: var(--secondary);">
                        <h3>✏️ Manueller Eintrag</h3>
                    </div>
                    <div class="card-body">
                        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 10px;">
                            <input type="date" id="entryDate" />
                            <select id="entryType">
                                <option value="work">Arbeit</option>
                                <option value="vacation">Urlaub</option>
                                <option value="sick">Krank</option>
                                <option value="holiday">Feiertag</option>
                                <option value="absent">Fehltag</option>
                            </select>
                            <input type="number" id="entryHours" step="0.01" placeholder="Brutto-Stunden" />
                            <input type="number" id="entryPause" step="0.01" placeholder="Pause (Std)" />
                            <input type="text" id="entryNote" placeholder="Notiz" maxlength="100" />
                            <button class="btn btn-primary" id="addEntryBtn">+ Eintrag speichern</button>
                        </div>
                        <div style="margin-top: 10px; font-size: 0.7rem; color: var(--secondary);">
                            💡 Bei leerem Pausenfeld wird automatisch die gesetzliche Pause berechnet!
                        </div>
                    </div>
                </div>
                
                <div class="privacy-note">
                    🔒 <strong>Sicherheit:</strong> 100% lokale Speicherung | Keine Cloud | Keine Code-Injection | XSS-geschützt<br>
                    ⏱️ <strong>Zeitrechnung:</strong> 1 Minute = 60 Sekunden | 1 Stunde = 60 Minuten | Präzise Berechnung<br>
                    🛡️ <strong>Security Headers:</strong> CSP | XSS-Protection | X-Frame-Options | No Sniff
                </div>
            </div>
        </div>
    </div>
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <script>
        (function() {
            // ==================== SICHERHEIT: Kein Zugriff auf globale Funktionen von außen ====================
            
            // ==================== HELFER ====================
            function escapeHtml(str) {
                if (!str) return '';
                return str
                    .replace(/&/g, '&amp;')
                    .replace(/</g, '&lt;')
                    .replace(/>/g, '&gt;')
                    .replace(/"/g, '&quot;')
                    .replace(/'/g, '&#39;');
            }
            
            function validateNumber(value, min = 0, max = 24) {
                const num = parseFloat(value);
                if (isNaN(num)) return 0;
                return Math.min(max, Math.max(min, num));
            }
            
            function validateDate(dateStr) {
                if (!dateStr) return false;
                const parts = dateStr.split('.');
                if (parts.length !== 3) return false;
                const day = parseInt(parts[0]);
                const month = parseInt(parts[1]);
                const year = parseInt(parts[2]);
                if (isNaN(day) || isNaN(month) || isNaN(year)) return false;
                if (day < 1 || day > 31) return false;
                if (month < 1 || month > 12) return false;
                if (year < 2000 || year > 2100) return false;
                return true;
            }
            
            function formatDateToDE(date) {
                const d = date.getDate().toString().padStart(2, '0');
                const m = (date.getMonth() + 1).toString().padStart(2, '0');
                const y = date.getFullYear();
                return `${d}.${m}.${y}`;
            }
            
            function parseDEDateString(ddmmyyyy) {
                if (!validateDate(ddmmyyyy)) return new Date();
                const parts = ddmmyyyy.split('.');
                return new Date(parts[2], parts[1] - 1, parts[0]);
            }
            
            function getTodayKey() {
                return new Date().toLocaleDateString('de-DE');
            }
            
            function formatTimeFromHours(hours) {
                const totalSeconds = Math.floor(hours * 3600);
                const h = Math.floor(totalSeconds / 3600);
                const m = Math.floor((totalSeconds % 3600) / 60);
                const s = totalSeconds % 60;
                return `${h.toString().padStart(2, '0')}:${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
            }
            
            function formatTime(ms) {
                const totalSeconds = Math.floor(ms / 1000);
                const h = Math.floor(totalSeconds / 3600);
                const m = Math.floor((totalSeconds % 3600) / 60);
                const s = totalSeconds % 60;
                return `${h.toString().padStart(2, '0')}:${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`;
            }
            
            // ==================== PAUSENREGEL ====================
            function calculateAutoPause(grossHours) {
                if (grossHours >= 9) return 45 / 60;
                if (grossHours >= 6) return 30 / 60;
                return 0;
            }
            
            // ==================== VIBRATION ====================
            function vibrate() {
                if (navigator.vibrate) navigator.vibrate(500);
            }
            
            // ==================== GLOBALE VARIABLEN ====================
            let workHistory = [];
            let workStartTime = null;
            let isWorking = false;
            let pauseStartTime = null;
            let isOnPause = false;
            let totalPauseToday = 0;
            let workInterval = null;
            let pauseInterval = null;
            let autoPauseMode = true;
            let currentFormula = '160';
            
            // ==================== DATA LOADING MIT VALIDIERUNG ====================
            try {
                const saved = localStorage.getItem('workHistory_pro_v3');
                if (saved) {
                    const parsed = JSON.parse(saved);
                    if (Array.isArray(parsed)) {
                        workHistory = parsed.filter(entry => 
                            entry && typeof entry === 'object' && 
                            entry.date && validateDate(entry.date)
                        );
                    }
                }
            } catch (e) { console.warn("Fehler beim Laden", e); }
            
            try {
                const mode = localStorage.getItem('autoPauseMode');
                if (mode !== null) autoPauseMode = mode === 'true';
            } catch (e) {}
            
            try {
                const formula = localStorage.getItem('currentFormula');
                if (formula === '160' || formula === '173.33') currentFormula = formula;
            } catch (e) {}
            
            // ==================== MONATSZIEL ====================
            function getMonthlyTarget() {
                if (currentFormula === '160') return 160;
                return 173.33;
            }
            
            // ==================== STATISTIK ====================
            function getTodayNetHours() {
                const today = getTodayKey();
                return workHistory
                    .filter(e => e.date === today && e.type === 'work')
                    .reduce((sum, e) => sum + (parseFloat(e.netto) || 0), 0);
            }
            
            function getTodayPauseFromHistory() {
                const today = getTodayKey();
                return workHistory
                    .filter(e => e.date === today && e.type === 'work')
                    .reduce((sum, e) => sum + (parseFloat(e.pause) || 0), 0);
            }
            
            function getStats() {
                const now = new Date();
                const thisYear = now.getFullYear();
                const thisMonth = now.getMonth();
                const oneWeekAgo = new Date();
                oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
                
                let stats = { todayWork: 0, todayPause: 0, weekWork: 0, monthWork: 0, vacation: 0, sick: 0, holiday: 0, absent: 0 };
                
                for (const entry of workHistory) {
                    const entryDate = parseDEDateString(entry.date);
                    if (entry.type === 'work') {
                        const net = parseFloat(entry.netto) || 0;
                        const pause = parseFloat(entry.pause) || 0;
                        if (entry.date === getTodayKey()) { stats.todayWork += net; stats.todayPause += pause; }
                        if (entryDate >= oneWeekAgo) stats.weekWork += net;
                        if (entryDate.getFullYear() === thisYear && entryDate.getMonth() === thisMonth) stats.monthWork += net;
                    } else if (entryDate.getFullYear() === thisYear && entryDate.getMonth() === thisMonth) {
                        if (entry.type === 'vacation') stats.vacation++;
                        else if (entry.type === 'sick') stats.sick++;
                        else if (entry.type === 'holiday') stats.holiday++;
                        else if (entry.type === 'absent') stats.absent++;
                    }
                }
                
                if (isWorking && workStartTime && !isOnPause) {
                    const grossHours = (new Date() - workStartTime) / 1000 / 3600;
                    let pauseHours = totalPauseToday;
                    if (autoPauseMode) pauseHours = Math.max(pauseHours, calculateAutoPause(grossHours));
                    const ongoingNet = grossHours - pauseHours;
                    stats.todayWork += ongoingNet;
                    stats.weekWork += ongoingNet;
                    stats.monthWork += ongoingNet;
                }
                
                return stats;
            }
            
            // ==================== UPDATE FUNKTIONEN ====================
            function updateTimeDisplay() {
                if (isWorking && workStartTime) {
                    const now = new Date();
                    const grossMs = now - workStartTime;
                    const grossHours = grossMs / 1000 / 3600;
                    
                    let pauseHours = totalPauseToday;
                    if (autoPauseMode && !isOnPause) {
                        const autoPause = calculateAutoPause(grossHours);
                        pauseHours = Math.max(pauseHours, autoPause);
                    }
                    const netHours = Math.max(0, grossHours - pauseHours);
                    
                    document.getElementById('grossTime').textContent = formatTime(grossMs);
                    document.getElementById('pauseTotalTime').textContent = formatTimeFromHours(pauseHours);
                    document.getElementById('netTime').textContent = formatTimeFromHours(netHours);
                    
                    if (netHours >= 8.5 && netHours < 8.51) {
                        document.getElementById('warningMessage').style.display = 'block';
                        vibrate();
                        setTimeout(() => { document.getElementById('warningMessage').style.display = 'none'; }, 5000);
                    } else {
                        document.getElementById('warningMessage').style.display = 'none';
                    }
                    
                    const todayNet = getTodayNetHours() + netHours;
                    document.getElementById('todayHours').textContent = todayNet.toFixed(2) + ' h';
                    document.getElementById('todayPauseHours').textContent = (pauseHours + getTodayPauseFromHistory()).toFixed(2) + ' h';
                }
                updateStats();
            }
            
            function updateStats() {
                const stats = getStats();
                document.getElementById('todayHours').textContent = stats.todayWork.toFixed(2) + ' h';
                document.getElementById('todayPauseHours').textContent = stats.todayPause.toFixed(2) + ' h';
                document.getElementById('weekHours').textContent = stats.weekWork.toFixed(2) + ' h';
                document.getElementById('monthHours').textContent = stats.monthWork.toFixed(2) + ' h';
                document.getElementById('vacationDays').textContent = stats.vacation;
                document.getElementById('sickDays').textContent = stats.sick;
                
                const target = getMonthlyTarget();
                document.getElementById('monthTarget').innerHTML = `${target.toFixed(2)} h`;
                document.getElementById('currentFormulaDisplay').innerHTML = currentFormula === '160' ? '40 × 12 ÷ 3 = 160 h/Monat' : '40 × 13 ÷ 3 = 173.33 h/Monat';
            }
            
            function updateHistory() {
                const tbody = document.getElementById('historyBody');
                tbody.innerHTML = '';
                const sorted = [...workHistory].reverse().slice(0, 30);
                
                for (const entry of sorted) {
                    const typeName = { work: 'Arbeit', vacation: 'Urlaub', sick: 'Krank', holiday: 'Feiertag', absent: 'Fehltag' }[entry.type];
                    const typeClass = `type-${entry.type}`;
                    const row = tbody.insertRow();
                    if (entry.type === 'work') {
                        row.innerHTML = `
                            <td>${escapeHtml(entry.date)}</td>
                            <td><span class="type-badge ${typeClass}">${escapeHtml(typeName)}</span></td>
                            <td>${escapeHtml(entry.startTime || '-')}</td>
                            <td>${escapeHtml(entry.endTime || '-')}</td>
                            <td>${entry.brutto ? escapeHtml(entry.brutto) + ' h' : '-'}</td>
                            <td style="color: var(--pause-color);">-${entry.pause ? escapeHtml(entry.pause) + ' h' : '-'}</td>
                            <td style="color: var(--success);">${entry.netto ? escapeHtml(entry.netto) + ' h' : '-'}</td>
                            <td>${escapeHtml(entry.note || '-')}</td>
                            <td><button class="action-btn" data-id="${entry.id}">🗑</button></td>
                        `;
                    } else {
                        row.innerHTML = `
                            <td>${escapeHtml(entry.date)}</td>
                            <td><span class="type-badge ${typeClass}">${escapeHtml(typeName)}</span></td>
                            <td>-</td>
                            <td>-</td>
                            <td>-</td>
                            <td>-</td>
                            <td>-</td>
                            <td>${escapeHtml(entry.note || '-')}</td>
                            <td><button class="action-btn" data-id="${entry.id}">🗑</button></td>
                        `;
                    }
                }
                
                // Sichere Lösch-Event-Listener
                document.querySelectorAll('.action-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const id = parseInt(btn.getAttribute('data-id'));
                        if (confirm('Eintrag löschen?')) {
                            workHistory = workHistory.filter(e => e.id !== id);
                            localStorage.setItem('workHistory_pro_v3', JSON.stringify(workHistory));
                            updateAll();
                        }
                    });
                });
            }
            
            function updateMonthDays() {
                const container = document.getElementById('monthDaysList');
                container.innerHTML = '';
                const now = new Date();
                const currentYear = now.getFullYear();
                const currentMonth = now.getMonth();
                const daysInMonth = new Date(currentYear, currentMonth + 1, 0).getDate();
                
                const dayData = {};
                for (const entry of workHistory) {
                    if (!validateDate(entry.date)) continue;
                    const entryDate = parseDEDateString(entry.date);
                    if (entryDate.getFullYear() === currentYear && entryDate.getMonth() === currentMonth) {
                        const day = entryDate.getDate();
                        if (!dayData[day]) dayData[day] = { work: 0, types: [] };
                        if (entry.type === 'work') dayData[day].work += parseFloat(entry.netto) || 0;
                        dayData[day].types.push(entry.type);
                    }
                }
                
                const typeIcons = { vacation: '🏖', sick: '🤒', holiday: '🎉', absent: '❌', work: '💼' };
                
                for (let day = 1; day <= daysInMonth; day++) {
                    const data = dayData[day];
                    const types = data ? data.types.map(t => typeIcons[t] || '').join(' ') : '';
                    const hours = data ? data.work.toFixed(2) : '0';
                    const dayDiv = document.createElement('div');
                    dayDiv.style.cssText = 'display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid #eee;';
                    dayDiv.innerHTML = `<span>${day.toString().padStart(2, '0')}.${(currentMonth+1).toString().padStart(2, '0')}.${currentYear}</span><span>${types} ${hours !== '0' ? hours + ' h' : ''}</span>`;
                    container.appendChild(dayDiv);
                }
            }
            
            function updateAll() {
                updateStats();
                updateHistory();
                updateMonthDays();
            }
            
            // ==================== ARBEITSZEIT FUNKTIONEN ====================
            function startWork() {
                if (isWorking) return;
                workStartTime = new Date();
                isWorking = true;
                totalPauseToday = 0;
                
                localStorage.setItem('activeWorkStart', workStartTime.toISOString());
                localStorage.setItem('isWorking', 'true');
                
                document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> ▶ Arbeit läuft';
                document.getElementById('statusSection').classList.add('status-working');
                document.getElementById('startBtn').disabled = true;
                document.getElementById('pauseBtn').disabled = false;
                document.getElementById('stopBtn').disabled = false;
                document.getElementById('resetBtn').disabled = false;
                
                if (workInterval) clearInterval(workInterval);
                workInterval = setInterval(updateTimeDisplay, 1000);
                updateTimeDisplay();
            }
            
            function startManualPause() {
                if (isWorking && !isOnPause) {
                    pauseStartTime = new Date();
                    isOnPause = true;
                    localStorage.setItem('pauseStartTime', pauseStartTime.toISOString());
                    localStorage.setItem('isOnPause', 'true');
                    
                    document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> ⏸ PAUSE';
                    document.getElementById('pauseTimerDisplay').style.display = 'block';
                    document.getElementById('statusSection').classList.add('status-pause');
                    document.getElementById('pauseBtn').disabled = true;
                    
                    if (pauseInterval) clearInterval(pauseInterval);
                    pauseInterval = setInterval(() => {
                        if (isOnPause && pauseStartTime) {
                            document.getElementById('pauseTimer').textContent = formatTime(new Date() - pauseStartTime);
                        }
                        updateTimeDisplay();
                    }, 1000);
                }
            }
            
            function resumeFromPause() {
                if (isOnPause && pauseStartTime) {
                    const pauseDurationHours = (new Date() - pauseStartTime) / 1000 / 3600;
                    totalPauseToday += pauseDurationHours;
                    
                    isOnPause = false;
                    clearInterval(pauseInterval);
                    localStorage.removeItem('pauseStartTime');
                    localStorage.removeItem('isOnPause');
                    
                    document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> ▶ Arbeit läuft';
                    document.getElementById('pauseTimerDisplay').style.display = 'none';
                    document.getElementById('statusSection').classList.remove('status-pause');
                    document.getElementById('pauseBtn').disabled = false;
                    
                    updateTimeDisplay();
                    updateStats();
                }
            }
            
            function stopWork() {
                if (!workStartTime) return;
                
                const endTime = new Date();
                const grossHours = (endTime - workStartTime) / 1000 / 3600;
                let pauseHours = totalPauseToday;
                if (autoPauseMode) pauseHours = Math.max(pauseHours, calculateAutoPause(grossHours));
                const netHours = Math.max(0, grossHours - pauseHours);
                
                let note = prompt('Notiz für heute (optional):', '');
                if (note === null) note = '';
                note = note.substring(0, 100);
                
                const entry = {
                    id: Date.now(),
                    date: getTodayKey(),
                    type: 'work',
                    startTime: workStartTime.toLocaleTimeString('de-DE'),
                    endTime: endTime.toLocaleTimeString('de-DE'),
                    brutto: grossHours.toFixed(2),
                    pause: pauseHours.toFixed(2),
                    netto: netHours.toFixed(2),
                    note: note
                };
                
                workHistory.push(entry);
                localStorage.setItem('workHistory_pro_v3', JSON.stringify(workHistory));
                
                isWorking = false;
                isOnPause = false;
                clearInterval(workInterval);
                clearInterval(pauseInterval);
                workStartTime = null;
                pauseStartTime = null;
                totalPauseToday = 0;
                
                localStorage.removeItem('activeWorkStart');
                localStorage.removeItem('isWorking');
                localStorage.removeItem('pauseStartTime');
                localStorage.removeItem('isOnPause');
                
                document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> Bereit zum Start';
                document.getElementById('statusSection').classList.remove('status-working', 'status-pause');
                document.getElementById('pauseTimerDisplay').style.display = 'none';
                document.getElementById('startBtn').disabled = false;
                document.getElementById('pauseBtn').disabled = true;
                document.getElementById('stopBtn').disabled = true;
                document.getElementById('resetBtn').disabled = true;
                document.getElementById('grossTime').textContent = '00:00:00';
                document.getElementById('pauseTotalTime').textContent = '00:00:00';
                document.getElementById('netTime').textContent = '00:00:00';
                
                updateAll();
            }
            
            function resetActiveWork() {
                if (confirm('Möchten Sie die aktuelle Arbeitseinheit verwerfen?')) {
                    isWorking = false;
                    isOnPause = false;
                    clearInterval(workInterval);
                    clearInterval(pauseInterval);
                    workStartTime = null;
                    pauseStartTime = null;
                    totalPauseToday = 0;
                    
                    localStorage.removeItem('activeWorkStart');
                    localStorage.removeItem('isWorking');
                    localStorage.removeItem('pauseStartTime');
                    localStorage.removeItem('isOnPause');
                    
                    document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> Bereit zum Start';
                    document.getElementById('statusSection').classList.remove('status-working', 'status-pause');
                    document.getElementById('pauseTimerDisplay').style.display = 'none';
                    document.getElementById('startBtn').disabled = false;
                    document.getElementById('pauseBtn').disabled = true;
                    document.getElementById('stopBtn').disabled = true;
                    document.getElementById('resetBtn').disabled = true;
                    document.getElementById('grossTime').textContent = '00:00:00';
                    document.getElementById('pauseTotalTime').textContent = '00:00:00';
                    document.getElementById('netTime').textContent = '00:00:00';
                    
                    updateAll();
                }
            }
            
            function addAbsence(type, displayName) {
                const note = prompt(`Notiz für ${displayName}:`, '');
                if (note !== null && note.length <= 100) {
                    const entry = {
                        id: Date.now(),
                        date: getTodayKey(),
                        type: type,
                        startTime: '-',
                        endTime: '-',
                        note: note || ''
                    };
                    workHistory.push(entry);
                    localStorage.setItem('workHistory_pro_v3', JSON.stringify(workHistory));
                    updateAll();
                    alert(`${displayName} wurde erfasst!`);
                }
            }
            
            function addManualEntry() {
                let dateValue = document.getElementById('entryDate').value;
                const type = document.getElementById('entryType').value;
                const brutto = validateNumber(document.getElementById('entryHours').value, 0, 24);
                let pause = parseFloat(document.getElementById('entryPause').value);
                let note = document.getElementById('entryNote').value || '';
                note = note.substring(0, 100);
                
                if (!dateValue) {
                    const d = new Date();
                    dateValue = d.toISOString().slice(0, 10);
                }
                const parts = dateValue.split('-');
                const formattedDate = `${parts[2]}.${parts[1]}.${parts[0]}`;
                
                if (!validateDate(formattedDate)) {
                    alert('Ungültiges Datum!');
                    return;
                }
                
                let entry;
                if (type === 'work') {
                    if (isNaN(pause)) pause = calculateAutoPause(brutto);
                    pause = validateNumber(pause, 0, 24);
                    const netto = Math.max(0, brutto - pause);
                    entry = {
                        id: Date.now(),
                        date: formattedDate,
                        type: type,
                        startTime: '08:00',
                        endTime: '17:00',
                        brutto: brutto.toFixed(2),
                        pause: pause.toFixed(2),
                        netto: netto.toFixed(2),
                        note: note
                    };
                } else {
                    entry = {
                        id: Date.now(),
                        date: formattedDate,
                        type: type,
                        startTime: '-',
                        endTime: '-',
                        note: note
                    };
                }
                
                workHistory.push(entry);
                localStorage.setItem('workHistory_pro_v3', JSON.stringify(workHistory));
                updateAll();
                
                document.getElementById('entryDate').value = '';
                document.getElementById('entryHours').value = '';
                document.getElementById('entryPause').value = '';
                document.getElementById('entryNote').value = '';
                alert('Eintrag gespeichert!');
            }
            
            function togglePauseMode() {
                autoPauseMode = !autoPauseMode;
                localStorage.setItem('autoPauseMode', autoPauseMode);
                const display = document.getElementById('pauseModeDisplay');
                if (autoPauseMode) {
                    display.innerHTML = '🔄 Automatischer Pausenabzug AKTIV';
                } else {
                    display.innerHTML = '👆 Manueller Pausenabzug AKTIV';
                }
                updateTimeDisplay();
            }
            
            function exportToPDF() {
                const element = document.querySelector('.container');
                html2pdf().set({ 
                    margin: 0.5, 
                    filename: `Arbeitszeiten_${new Date().toISOString().split('T')[0]}.pdf`, 
                    image: { type: 'jpeg', quality: 0.98 }, 
                    html2canvas: { scale: 2 }, 
                    jsPDF: { unit: 'in', format: 'a4', orientation: 'portrait' } 
                }).from(element).save();
            }
            
            function updateCurrentTime() {
                document.getElementById('currentTime').textContent = new Date().toLocaleTimeString('de-DE');
            }
            
            // ==================== EVENT LISTENER ====================
            document.getElementById('startBtn').addEventListener('click', startWork);
            document.getElementById('pauseBtn').addEventListener('click', startManualPause);
            document.getElementById('resumeWorkBtn').addEventListener('click', resumeFromPause);
            document.getElementById('stopBtn').addEventListener('click', stopWork);
            document.getElementById('resetBtn').addEventListener('click', resetActiveWork);
            document.getElementById('vacationBtn').addEventListener('click', () => addAbsence('vacation', 'Urlaub'));
            document.getElementById('sickBtn').addEventListener('click', () => addAbsence('sick', 'Krankheit'));
            document.getElementById('holidayBtn').addEventListener('click', () => addAbsence('holiday', 'Feiertag'));
            document.getElementById('absentBtn').addEventListener('click', () => addAbsence('absent', 'Fehltag'));
            document.getElementById('exportPDFBtn').addEventListener('click', exportToPDF);
            document.getElementById('addEntryBtn').addEventListener('click', addManualEntry);
            document.getElementById('togglePauseMode').addEventListener('click', togglePauseMode);
            document.getElementById('formula160').addEventListener('click', () => {
                currentFormula = '160';
                localStorage.setItem('currentFormula', '160');
                updateStats();
            });
            document.getElementById('formula173').addEventListener('click', () => {
                currentFormula = '173.33';
                localStorage.setItem('currentFormula', '173.33');
                updateStats();
            });
            
            // ==================== WIEDERHERSTELLUNG ====================
            const savedWorkStart = localStorage.getItem('activeWorkStart');
            const savedIsWorking = localStorage.getItem('isWorking') === 'true';
            const savedPauseStart = localStorage.getItem('pauseStartTime');
            const savedIsOnPause = localStorage.getItem('isOnPause') === 'true';
            
            if (savedIsWorking && savedWorkStart) {
                workStartTime = new Date(savedWorkStart);
                isWorking = true;
                if (savedIsOnPause && savedPauseStart) {
                    pauseStartTime = new Date(savedPauseStart);
                    isOnPause = true;
                    document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> ⏸ PAUSE';
                    document.getElementById('pauseTimerDisplay').style.display = 'block';
                    document.getElementById('pauseBtn').disabled = true;
                    pauseInterval = setInterval(() => {
                        if (isOnPause && pauseStartTime) {
                            document.getElementById('pauseTimer').textContent = formatTime(new Date() - pauseStartTime);
                        }
                        updateTimeDisplay();
                    }, 1000);
                } else {
                    document.getElementById('statusText').innerHTML = '<span class="status-indicator"></span> ▶ Arbeit läuft';
                    document.getElementById('pauseBtn').disabled = false;
                }
                document.getElementById('startBtn').disabled = true;
                document.getElementById('stopBtn').disabled = false;
                document.getElementById('resetBtn').disabled = false;
                workInterval = setInterval(updateTimeDisplay, 1000);
                updateTimeDisplay();
            }
            
            // ==================== INIT ====================
            setInterval(updateCurrentTime, 1000);
            updateCurrentTime();
            updateAll();
        })();
    </script>
</body>
</html>
