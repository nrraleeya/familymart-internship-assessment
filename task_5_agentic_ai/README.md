popup.html
<!DOCTYPE html>
<html>
<head>
    <title>ScamSeal Command</title>
    <meta charset="UTF-8">
    <style>
        :root { --primary: #4F46E5; --bg: #F3F4F6; --text: #1F2937; --success: #10B981; --danger: #EF4444; }
        body { width: 340px; font-family: 'Segoe UI', sans-serif; background: var(--bg); color: var(--text); margin: 0; }

        .header { background: white; padding: 15px; border-bottom: 1px solid #E5E7EB; display: flex; align-items: center; justify-content: space-between; }
        .logo { font-weight: 800; font-size: 18px; color: var(--primary); letter-spacing: -0.5px; }
        .status-badge { font-size: 10px; background: #ECFDF5; color: #065F46; padding: 4px 8px; border-radius: 12px; font-weight: 700; }

        .nav { display: flex; background: white; border-bottom: 1px solid #E5E7EB; }
        .nav-item { flex: 1; text-align: center; padding: 12px; font-size: 13px; font-weight: 600; color: #6B7280; cursor: pointer; border-bottom: 3px solid transparent; }
        .nav-item.active { color: var(--primary); border-bottom-color: var(--primary); background: #F9FAFB; }
        
        .content { display: none; padding: 15px; }
        .content.active { display: block; }

        .card { background: white; border-radius: 8px; padding: 15px; box-shadow: 0 1px 2px rgba(0,0,0,0.05); margin-bottom: 10px; }
        h3 { margin: 0 0 10px 0; font-size: 14px; color: #374151; }

        .scan-btn { width: 100%; background: var(--primary); color: white; border: none; padding: 12px; border-radius: 8px; font-weight: 700; font-size: 14px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; transition: 0.2s; }
        .scan-btn:hover { background: #4338CA; }

        .result-box { margin-top: 15px; padding: 15px; border-radius: 8px; display: none; text-align: center; }
        .result-box.SAFE { background: #D1FAE5; color: #065F46; border: 1px solid #34D399; }
        .result-box.SCAM { background: #FEE2E2; color: #991B1B; border: 1px solid #F87171; }
        
        .confidence-container { background: rgba(0,0,0,0.1); border-radius: 10px; height: 8px; width: 100%; margin: 10px 0; overflow: hidden; }
        .confidence-bar { height: 100%; background: currentColor; width: 0%; transition: width 0.5s; }
        
        .keyword-list { text-align: left; font-size: 12px; margin-top: 10px; padding-top: 10px; border-top: 1px solid rgba(0,0,0,0.1); }
        .badge { display: inline-block; background: rgba(255,255,255,0.7); padding: 2px 6px; border-radius: 4px; margin: 3px 3px 3px 0; font-weight: 600; border: 1px solid rgba(0,0,0,0.1); }

        textarea { width: 100%; height: 80px; padding: 10px; border: 1px solid #D1D5DB; border-radius: 6px; box-sizing: border-box; font-family: inherit; margin-bottom: 10px; }

        .toggle-row { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid #F3F4F6; }
        .switch { position: relative; display: inline-block; width: 36px; height: 20px; }
        .switch input { opacity: 0; width: 0; height: 0; }
        .slider { position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0; background-color: #ccc; transition: .4s; border-radius: 20px; }
        .slider:before { position: absolute; content: ""; height: 16px; width: 16px; left: 2px; bottom: 2px; background-color: white; transition: .4s; border-radius: 50%; }
        input:checked + .slider { background-color: var(--primary); }
        input:checked + .slider:before { transform: translateX(16px); }
        
        select { width: 100%; padding: 8px; border-radius: 6px; border: 1px solid #D1D5DB; margin-top: 5px; margin-bottom: 10px; }
        .explanation { background-color: #EFF6FF; padding: 10px; font-size: 11px; color: #1E40AF; border-radius: 4px; margin-top: 5px; line-height: 1.4; border-left: 3px solid #3B82F6; }
        .save-btn { width: 100%; background: #374151; color: white; border: none; padding: 10px; border-radius: 6px; font-weight: 600; cursor: pointer; margin-top: 15px; }
    </style>
</head>
<body>

    <div class="header">
        <div class="logo">ScamSeal</div>
        <div class="status-badge">v2.5 Active</div>
    </div>

    <div class="nav">
        <div id="tab-home" class="nav-item active">Home</div>
        <div id="tab-manual" class="nav-item">Manual Check</div>
        <div id="tab-settings" class="nav-item">Settings</div>
    </div>

    <div id="view-home" class="content active">
        <button id="btn-scan-page" class="scan-btn">
            SCAN THIS PAGE
        </button>
        <p style="text-align: center; font-size: 11px; color: #9CA3AF; margin-top: 8px;">
            Scans all visible content on the current tab.
        </p>

        <div id="pageResult" class="result-box">
            <h2 id="pageStatus" style="margin: 0; font-size: 18px;"></h2>
            <div style="font-size: 12px; margin-top: 5px;">
                Confidence: <strong id="pageConfidenceVal">0%</strong>
            </div>
            <div class="confidence-container">
                <div id="pageConfidenceBar" class="confidence-bar"></div>
            </div>
            <div id="pageKeywords" class="keyword-list"></div>
        </div>

        <div class="card" style="margin-top: 15px;">
            <h3>Active Protection</h3>
            <div class="toggle-row">
                <span>Instagram Detection</span>
                <label class="switch"><input type="checkbox" id="toggle-ig" checked><span class="slider"></span></label>
            </div>
            <div class="toggle-row">
                <span>Facebook Detection</span>
                <label class="switch"><input type="checkbox" id="toggle-fb" checked><span class="slider"></span></label>
            </div>
        </div>
    </div>

    <div id="view-manual" class="content">
        <div class="card">
            <h3>Manual Verifier</h3>
            <textarea id="manualInput" placeholder="Paste suspicious text here..."></textarea>
            <button id="btn-analyze-manual" class="scan-btn" style="background: #374151;">Analyze Text</button>
            <div id="manualResult" class="result-box" style="padding:10px;">
                <strong id="resPrediction"></strong><br>
                Confidence: <span id="resScore"></span>%
            </div>
        </div>
    </div>

    <div id="view-settings" class="content">
        <div class="card">
            <label style="font-size:13px; font-weight:600;">Detection Sensitivity</label>
            <select id="sensitivity">
                <option value="low">Low</option>
                <option value="moderate">Moderate</option>
                <option value="high">High</option>
            </select>
            
            <div class="explanation">
                <strong>Logic:</strong>
                <ul style="margin:5px 0 0 0; padding-left:15px;">
                    <li><strong>High:</strong> Strict. May flag legitimate posts.</li>
                    <li><strong>Moderate:</strong> Balanced detection.</li>
                    <li><strong>Low:</strong> Relaxed. Only flags obvious scams.</li>
                </ul>
            </div>

            <label style="font-size:13px; font-weight:600; display:block; margin-top:15px;">Preferred Language</label>
            <select id="language">
                <option value="auto">Automatic Detection</option>
                <option value="en">English</option>
                <option value="ms">Malay (Bahasa Melayu)</option>
            </select>

            <button id="btn-save" class="save-btn">Save Configuration</button>
            <p id="saveMsg" style="text-align: center; color: var(--success); font-size: 12px; display: none; margin-top: 5px;">Settings Saved!</p>
        </div>
    </div>

    <script src="popup.js"></script>
</body>
</html>