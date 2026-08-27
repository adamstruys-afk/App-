<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alpha Tracker - 4 Mois de Transformation</title>
    <style>
        :root {
            --bg-color: #0f1115;
            --card-bg: #1a1d24;
            --accent: #e50914;
            --text-main: #ffffff;
            --text-muted: #8c939d;
            --border: #2c323d;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        body { background-color: var(--bg-color); color: var(--text-main); padding: 20px; max-width: 600px; margin: 0 auto; }
        h1 { text-align: center; font-size: 1.5rem; margin-bottom: 5px; color: var(--accent); text-transform: uppercase; letter-spacing: 1px; }
        .subtitle { text-align: center; color: var(--text-muted); font-size: 0.85rem; margin-bottom: 25px; }
        .card { background-color: var(--card-bg); border: 1px solid var(--border); border-radius: 12px; padding: 20px; margin-bottom: 15px; }
        .card h2 { font-size: 1.1rem; margin-bottom: 15px; display: flex; justify-content: space-between; align-items: center; }
        .form-group { margin-bottom: 12px; }
        label { display: block; font-size: 0.8rem; color: var(--text-muted); margin-bottom: 5px; text-transform: uppercase; }
        input, select { width: 100%; padding: 10px; background: var(--bg-color); border: 1px solid var(--border); border-radius: 6px; color: white; font-size: 1rem; }
        button { width: 100%; background-color: var(--accent); color: white; border: none; padding: 12px; border-radius: 6px; font-weight: bold; cursor: pointer; text-transform: uppercase; font-size: 0.9rem; margin-top: 10px; transition: opacity 0.2s; }
        button:hover { opacity: 0.9; }
        .stats-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; }
        .stat-box { background: var(--bg-color); padding: 12px; border-radius: 8px; text-align: center; border: 1px solid var(--border); }
        .stat-box span { display: block; font-size: 1.2rem; font-weight: bold; color: var(--accent); margin-top: 5px; }
        .history-list { font-size: 0.85rem; color: var(--text-muted); max-height: 150px; overflow-y: auto; margin-top: 10px; }
        .history-item { padding: 6px 0; border-bottom: 1px solid var(--border); display: flex; justify-content: space-between; }
    </style>
</head>
<body>

    <h1>Alpha Protocol</h1>
    <div class="subtitle">Jour 1 / 120 — Ne négocie pas avec ta discipline.</div>

    <!-- SECTION : ENREGISTRER LA JOURNÉE -->
    <div class="card">
        <h2>Validation Quotidienne</h2>
        <div class="form-group">
            <label>Date</label>
            <input type="date" id="logDate">
        </div>
        <div class="form-group">
            <label>Calories (Cible déficit)</label>
            <input type="number" id="calories" placeholder="ex: 2100">
        </div>
        <div class="form-group">
            <label>Sommeil (Heures)</label>
            <input type="number" step="0.5" id="sleep" placeholder="ex: 8">
        </div>
        <div class="form-group">
            <label>Séance de Sport Faite ?</label>
            <select id="workout">
                <option value="Non">Non (Jour off / Repos)</option>
                <option value="Musculation Lourde">Musculation Lourde</option>
                <option value="Cardio / HIIT">Cardio / HIIT</option>
            </select>
        </div>
        <div class="form-group">
            <label>Bain Froid / Douche Froide ?</label>
            <select id="coldBath">
                <option value="Non">Non</option>
                <option value="Oui">Oui</option>
            </select>
        </div>
        <button onclick="saveDay()">Enregistrer la journée</button>
    </div>

    <!-- SECTION : TABLEAU DE BORD RAPIDE -->
    <div class="card">
        <h2>Indicateurs Clés</h2>
        <div class="stats-grid">
            <div class="stat-box">
                Jours validés <span><strong id="totalDays">0</strong></span>
            </div>
            <div class="stat-box">
                Bains froids <span><strong id="totalCold">0</strong></span>
            </div>
        </div>
    </div>

    <!-- SECTION : HISTORIQUE -->
    <div class="card">
        <h2>Historique Récent</h2>
        <div id="historyList" class="history-list">
            <!-- Injecté en JS -->
        </div>
    </div>

    <script>
        // Initialiser la date du jour par défaut dans l'input
        document.getElementById('logDate').valueAsDate = new Date();

        function loadData() {
            let data = JSON.parse(localStorage.getItem('alpha_logs')) || [];
            document.getElementById('totalDays').innerText = data.length;
            
            let coldCount = data.filter(d => d.coldBath === 'Oui').length;
            document.getElementById('totalCold').innerText = coldCount;

            let historyHTML = '';
            data.slice(-5).reverse().forEach(item => {
                historyHTML += `<div class="history-item"><span>${item.date} (${item.workout})</span> <span>🔥 ${item.calories} kcal | 💤 ${item.sleep}h</span></div>`;
            });
            document.getElementById('historyList').innerHTML = historyHTML || 'Aucune donnée enregistrée.';
        }

        function saveDay() {
            let date = document.getElementById('logDate').value;
            let calories = document.getElementById('calories').value;
            let sleep = document.getElementById('sleep').value;
            let workout = document.getElementById('workout').value;
            let coldBath = document.getElementById('coldBath').value;

            if(!calories || !sleep) {
                alert("Remplis au moins les calories et le sommeil !");
                return;
            }

            let entry = { date, calories, sleep, workout, coldBath };
            let data = JSON.parse(localStorage.getItem('alpha_logs')) || [];
            
            // Éviter les doublons sur la même date
            data = data.filter(d => d.date !== date);
            data.push(entry);

            localStorage.setItem('alpha_logs', JSON.stringify(data));
            loadData();
            alert("Journée enregistrée. Continue d'avancer.");
        }

        loadData();
    </script>
</body>
</html>
