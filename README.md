<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>Dashboard de Devengados Perú | Análisis por Distrito</title>
    <!-- Chart.js y plugin de etiquetas -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-datalabels@2.0.0/dist/chartjs-plugin-datalabels.min.js"></script>
    <!-- html2pdf para exportación profesional -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js" integrity="sha512-GsLlZN/3F2ErC5ifS5QtgpiJtWd43JWSuIgh7mbzZ8zBps+dvLusV+eNQATqgA/HdeKFVgA5v3S/cIrLF7QnIg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        body {
            background: #f4f7fc;
            font-family: 'Segoe UI', 'Roboto', 'Poppins', sans-serif;
            padding: 30px 24px;
            color: #1a2c3e;
        }
        .dashboard-wrapper {
            max-width: 1600px;
            margin: 0 auto;
        }
        /* Header */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            margin-bottom: 28px;
            gap: 15px;
        }
        h1 {
            font-size: 1.9rem;
            font-weight: 700;
            background: linear-gradient(135deg, #1E4A76, #2A7F6E);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            letter-spacing: -0.5px;
        }
        .btn-export {
            background: #1e6f5c;
            color: white;
            border: none;
            border-radius: 60px;
            padding: 12px 28px;
            font-weight: 600;
            font-size: 0.9rem;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 8px rgba(0,0,0,0.05);
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }
        .btn-export:hover {
            background: #0e5645;
            transform: translateY(-2px);
        }
        /* Filtros */
        .filters-panel {
            background: white;
            border-radius: 32px;
            padding: 20px 28px;
            margin-bottom: 32px;
            box-shadow: 0 12px 24px rgba(0,0,0,0.04);
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            align-items: flex-end;
        }
        .filter-item {
            flex: 1;
            min-width: 170px;
        }
        .filter-item label {
            display: block;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            color: #4a627a;
            margin-bottom: 8px;
        }
        select, .btn-search {
            width: 100%;
            padding: 12px 16px;
            border-radius: 20px;
            border: 1px solid #ccdbe9;
            background: #fff;
            font-size: 0.9rem;
            cursor: pointer;
            transition: 0.2s;
            font-weight: 500;
        }
        select:focus, .btn-search:focus {
            outline: none;
            border-color: #2a7f6e;
            box-shadow: 0 0 0 3px rgba(42,127,110,0.2);
        }
        .btn-search {
            background: #2a7f6e;
            color: white;
            border: none;
            font-weight: bold;
        }
        .btn-search:hover {
            background: #1e5f52;
        }
        /* Grid gráficos */
        .grid-charts {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(560px, 1fr));
            gap: 28px;
            margin-bottom: 45px;
        }
        .card {
            background: white;
            border-radius: 32px;
            padding: 18px 20px 22px;
            box-shadow: 0 12px 28px rgba(0,0,0,0.05);
            transition: all 0.2s;
        }
        .card h3 {
            font-size: 1.25rem;
            font-weight: 600;
            margin-bottom: 16px;
            padding-left: 14px;
            border-left: 5px solid #2a7f6e;
            color: #1f3b4c;
        }
        canvas {
            max-height: 340px;
            width: 100%;
        }
        .subnote {
            font-size: 0.7rem;
            color: #6c86a3;
            text-align: center;
            margin-top: 12px;
        }
        /* Tabla */
        .table-container {
            background: white;
            border-radius: 32px;
            padding: 20px 24px;
            overflow-x: auto;
            margin-top: 10px;
            box-shadow: 0 12px 28px rgba(0,0,0,0.05);
        }
        .table-container h3 {
            font-size: 1.3rem;
            margin-bottom: 18px;
            color: #1f3b4c;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 0.8rem;
            min-width: 800px;
        }
        th {
            background: #eef2f9;
            padding: 14px 8px;
            text-align: center;
            font-weight: 700;
            color: #1f4970;
            border-radius: 16px 16px 0 0;
        }
        td {
            padding: 10px 6px;
            text-align: right;
            border-bottom: 1px solid #e2edf7;
        }
        td:first-child, th:first-child {
            text-align: left;
            font-weight: 500;
        }
        .district-name {
            font-weight: 700;
            color: #135b4b;
        }
        .badge-pct {
            background: #e0f2e9;
            color: #176b51;
            padding: 4px 10px;
            border-radius: 30px;
            font-weight: 700;
            font-size: 0.7rem;
            display: inline-block;
        }
        .badge-negative {
            background: #ffe6e5;
            color: #bc4e2c;
        }
        footer {
            text-align: center;
            margin-top: 35px;
            font-size: 0.7rem;
            color: #5d7184;
        }
        @media (max-width: 1200px) {
            .grid-charts { grid-template-columns: 1fr; }
            body { padding: 20px 16px; }
        }
        @media print {
            .btn-export, .btn-search, .filters-panel { display: none; }
            body { background: white; padding: 0; }
            .card { break-inside: avoid; }
        }
    </style>
</head>
<body>
<div class="dashboard-wrapper" id="dashboardMain">
    <div class="header">
        <h1>📈 Panel de Devengado del PVL · Gobiernos Locales</h1>
        <button class="btn-export" id="exportPDFBtn">📑 Exportar Dashboard a PDF</button>
    </div>

    <div class="filters-panel">
        <div class="filter-item">
            <label>🏛 DEPARTAMENTO</label>
            <select id="filterDepto"><option value="">Todos</option></select>
        </div>
        <div class="filter-item">
            <label>📍 PROVINCIA</label>
            <select id="filterProv"><option value="">Todos</option></select>
        </div>
        <div class="filter-item">
            <label>🏘️ DISTRITO</label>
            <select id="filterDist"><option value="">Todos</option></select>
        </div>
        <div class="filter-item">
            <label>&nbsp;</label>
            <button class="btn-search" id="applyFiltersBtn">🔍 BUSCAR / ACTUALIZAR</button>
        </div>
    </div>

    <div class="grid-charts">
        <div class="card"><h3>📆 1. Evolución del Devengado Total (S/)</h3><canvas id="chartEvolution"></canvas><div class="subnote">Montos acumulados por período - etiquetas fijas en cada barra</div></div>
        <div class="card"><h3>📊 2. Comparación últimos 4 cortes (% por distrito)</h3><canvas id="chartLast4Comparison"></canvas><div class="subnote">Top 12 distritos · Porcentaje respecto al total de sus 4 períodos</div></div>
        <div class="card"><h3>🏅 3. Mayor Devengado: Último vs Penúltimo corte</h3><canvas id="chartLastVsPrev"></canvas><div class="subnote">Top 10 distritos: 26.05.2026 vs 18.05.2026</div></div>
        <div class="card"><h3>📈 4. Incremento porcentual (26.05 vs 18.05)</h3><canvas id="chartPctIncrease"></canvas><div class="subnote">Variación positiva/negativa con etiquetas de %</div></div>
    </div>

    <div class="table-container">
        <h3>🗺️ Listado nacional de distritos · Últimos 5 devengados + incremento</h3>
        <div style="overflow-x: auto;">
            <table id="detailTable">
                <thead id="tableHeader"></thead>
                <tbody id="tableBody"><tr><td colspan="10">Cargando datos desde Google Sheets...</td></tr></tbody>
            </table>
        </div>
    </div>
    <footer>Fuente: Hoja pública de devengados | Filtros dinámicos | Los montos en S/ con separación de miles</footer>
</div>

<script>
    // Configuración global de chartjs datalabels
    Chart.register(ChartDataLabels);
    Chart.defaults.set('plugins.datalabels', {
        color: '#1f2d3a',
        anchor: 'end',
        align: 'top',
        offset: 4,
        font: { weight: 'bold', size: 10 },
        formatter: (value, ctx) => {
            if (value === undefined || value === null) return '';
            // Para gráfico de porcentajes
            if (ctx.dataset.label && ctx.dataset.label.includes('%') && typeof value === 'number') return value.toFixed(1)+'%';
            if (typeof value === 'number') {
                if (Math.abs(value) >= 1000) return 'S/ ' + value.toLocaleString('es-PE');
                return 'S/ ' + value.toFixed(0);
            }
            return value;
        }
    });

    // URL pública del CSV (nuevo enlace)
    const CSV_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vQlTYari809MR6QiPkHF7PRivLgJelkKGLSFyqyvdIv46Pwj0CyDccWuSmB5ctCX3Jxqw8JPHx7hvhB/pub?gid=0&single=true&output=csv";

    // Estructura de columnas esperadas
    const EXPECTED_COLS = [
        "Departamento", "Provincia", "Distrito",
        "Devengado al 16.03.2026",
        "Devengado al 06.04.2026",
        "Devengado al 13.04.2026",
        "Devengado al 20.04.2026",
        "Devengado al 27.04.2026",
        "Devengado al 18.05.2026",
        "Devengado al 26.05.2026"
    ];

    let rawDataset = [];        // array de objetos con datos completos
    let filteredData = [];      // después de aplicar filtros
    // Referencias gráficos
    let chartEvo, chartComp, chartVsPrev, chartPct;
    // Mapeo de fechas abreviadas
    let shortLabels = [];
    let fullDateColumns = [];

    // Cargar datos
    async function fetchData() {
        try {
            const response = await fetch(CSV_URL);
            if (!response.ok) throw new Error(`Error HTTP: ${response.status}`);
            const csvText = await response.text();
            parseCSVAndBuild(csvText);
        } catch (err) {
            console.error(err);
            document.getElementById('tableBody').innerHTML = `<tr><td colspan="10">❌ Error al cargar datos: ${err.message}. Verifique el link público.</td></tr>`;
        }
    }

    function parseCSVAndBuild(csv) {
        const lines = csv.split(/\r?\n/).filter(l => l.trim().length > 0);
        if (lines.length < 2) return;
        // parsear primera fila como headers
        let headers = lines[0].split(',').map(h => h.trim().replace(/^"|"$/g, ''));
        // encontrar índices de columnas requeridas
        let colIndex = {};
        EXPECTED_COLS.forEach(col => {
            let idx = headers.findIndex(h => h === col);
            if (idx === -1) idx = headers.findIndex(h => h.toLowerCase() === col.toLowerCase());
            colIndex[col] = idx;
        });
        if (colIndex["Departamento"] === -1 || colIndex["Distrito"] === -1) {
            throw new Error("No se encontraron columnas: Departamento, Provincia, Distrito");
        }
        // Guardar las fechas (columnas de devengado)
        const dateColumns = EXPECTED_COLS.slice(3); // desde 16.03 hasta 26.05
        fullDateColumns = dateColumns;
        shortLabels = dateColumns.map(d => {
            let match = d.match(/(\d{2})\.(\d{2})\.(\d{4})/);
            return match ? `${match[1]}/${match[2]}` : d.slice(-8);
        });

        rawDataset = [];
        for (let i = 1; i < lines.length; i++) {
            const row = parseCSVRow(lines[i]);
            if (row.length < headers.length) continue;
            const departamento = row[colIndex["Departamento"]]?.trim() || "";
            const provincia = row[colIndex["Provincia"]]?.trim() || "";
            const distrito = row[colIndex["Distrito"]]?.trim() || "";
            if (!departamento && !provincia && !distrito) continue;
            // leer valores de devengados
            let devengados = [];
            let allValid = true;
            for (let idx = 0; idx < dateColumns.length; idx++) {
                const colName = dateColumns[idx];
                const colPos = colIndex[colName];
                let val = (colPos !== undefined && colPos !== -1 && row[colPos]) ? row[colPos] : "0";
                val = val.toString().replace(/,/g, '').trim();
                let num = parseFloat(val);
                if (isNaN(num)) num = 0;
                devengados.push(num);
            }
            rawDataset.push({
                departamento,
                provincia,
                distrito,
                devengados: devengados  // orden igual a dateColumns
            });
        }
        if (rawDataset.length === 0) throw new Error("No se encontraron datos en la hoja.");
        // poblar filtros
        populateFilters();
        applyFiltersAndRender();
    }

    function parseCSVRow(line) {
        const result = [];
        let inQuote = false;
        let current = "";
        for (let i = 0; i < line.length; i++) {
            let ch = line[i];
            if (ch === '"') {
                inQuote = !inQuote;
            } else if (ch === ',' && !inQuote) {
                result.push(current);
                current = "";
            } else {
                current += ch;
            }
        }
        result.push(current);
        return result.map(v => v.trim().replace(/^"|"$/g, ''));
    }

    function populateFilters() {
        const deptos = [...new Set(rawDataset.map(d => d.departamento))].sort();
        const deptoSelect = document.getElementById('filterDepto');
        deptoSelect.innerHTML = '<option value="">Todos</option>' + deptos.map(d => `<option value="${escapeHtml(d)}">${d}</option>`).join('');
        deptoSelect.addEventListener('change', () => updateProvinceOptions());
        document.getElementById('filterProv').addEventListener('change', () => updateDistrictOptions());
        updateProvinceOptions();
    }

    function updateProvinceOptions() {
        const selectedDepto = document.getElementById('filterDepto').value;
        let provincias = rawDataset.filter(r => !selectedDepto || r.departamento === selectedDepto).map(r => r.provincia);
        provincias = [...new Set(provincias)].sort();
        const provSelect = document.getElementById('filterProv');
        provSelect.innerHTML = '<option value="">Todos</option>' + provincias.map(p => `<option value="${escapeHtml(p)}">${p}</option>`).join('');
        updateDistrictOptions();
    }

    function updateDistrictOptions() {
        const selectedDepto = document.getElementById('filterDepto').value;
        const selectedProv = document.getElementById('filterProv').value;
        let distritos = rawDataset.filter(r => 
            (!selectedDepto || r.departamento === selectedDepto) &&
            (!selectedProv || r.provincia === selectedProv)
        ).map(r => r.distrito);
        distritos = [...new Set(distritos)].sort();
        const distSelect = document.getElementById('filterDist');
        distSelect.innerHTML = '<option value="">Todos</option>' + distritos.map(d => `<option value="${escapeHtml(d)}">${d}</option>`).join('');
    }

    function escapeHtml(str) { return str.replace(/[&<>]/g, function(m){if(m==='&') return '&amp;'; if(m==='<') return '&lt;'; if(m==='>') return '&gt;'; return m;}); }

    function applyFiltersAndRender() {
        const depto = document.getElementById('filterDepto').value;
        const prov = document.getElementById('filterProv').value;
        const dist = document.getElementById('filterDist').value;
        filteredData = rawDataset.filter(item => 
            (!depto || item.departamento === depto) &&
            (!prov || item.provincia === prov) &&
            (!dist || item.distrito === dist)
        );
        if (filteredData.length === 0) {
            clearChartsAndTableEmpty();
            return;
        }
        updateAllGraphicsAndTable();
    }

    function clearChartsAndTableEmpty() {
        if (chartEvo) chartEvo.destroy();
        if (chartComp) chartComp.destroy();
        if (chartVsPrev) chartVsPrev.destroy();
        if (chartPct) chartPct.destroy();
        document.getElementById('tableBody').innerHTML = '<tr><td colspan="10">⚠️ No hay datos con los filtros seleccionados.</td></tr>';
        document.getElementById('tableHeader').innerHTML = '';
    }

    function updateAllGraphicsAndTable() {
        // Destruir gráficos anteriores
        if (chartEvo) chartEvo.destroy();
        if (chartComp) chartComp.destroy();
        if (chartVsPrev) chartVsPrev.destroy();
        if (chartPct) chartPct.destroy();

        // 1. Gráfico Evolución: suma total por período
        const totals = Array(fullDateColumns.length).fill(0);
        filteredData.forEach(item => {
            for (let i=0; i<item.devengados.length; i++) totals[i] += item.devengados[i];
        });
        const ctxEvo = document.getElementById('chartEvolution').getContext('2d');
        chartEvo = new Chart(ctxEvo, {
            type: 'bar',
            data: { labels: shortLabels, datasets: [{ label: 'Devengado total (S/)', data: totals, backgroundColor: '#2c7da0', borderRadius: 10, barPercentage: 0.7 }] },
            options: { responsive: true, maintainAspectRatio: true, plugins: { datalabels: { anchor: 'end', align: 'top', formatter: (val) => 'S/ '+val.toLocaleString('es-PE') } }, scales: { y: { beginAtZero: true, title: { display: true, text: 'Monto en Soles', font: {weight:'bold'} } } } }
        });

        // 2. Gráfico comparación últimos 4 devengados (índices 3,4,5,6: 20.04,27.04,18.05,26.05)
        const last4Indices = [3,4,5,6];
        const last4Labels = last4Indices.map(i => shortLabels[i]);
        // Agrupar por distrito
        let districtMap = new Map(); // key distrito, value { name, valoresLast4, totalLast4 }
        filteredData.forEach(item => {
            const key = item.distrito;
            if (!districtMap.has(key)) districtMap.set(key, { distrito: item.distrito, last4Vals: [] });
            let entry = districtMap.get(key);
            const vals = last4Indices.map(idx => item.devengados[idx]);
            entry.last4Vals = vals;
            entry.totalLast4 = vals.reduce((a,b)=>a+b,0);
        });
        let districtArray = Array.from(districtMap.values());
        districtArray.sort((a,b)=>b.totalLast4 - a.totalLast4);
        const topDistricts = districtArray.slice(0, 14); // máximo 14 para visual limpia
        // Construir datasets para cada período con porcentaje estático
        const datasetsComp = [];
        for (let p = 0; p < last4Indices.length; p++) {
            const periodData = topDistricts.map(d => d.last4Vals[p]);
            const percentages = topDistricts.map((d, idx) => {
                if (d.totalLast4 === 0) return 0;
                return (d.last4Vals[p] / d.totalLast4) * 100;
            });
            datasetsComp.push({
                label: `${last4Labels[p]} (monto)`,
                data: periodData,
                backgroundColor: `hsl(${30 + p*70}, 65%, 58%)`,
                borderRadius: 6,
                datalabels: {
                    formatter: (value, ctx) => {
                        const pct = percentages[ctx.dataIndex];
                        return `${pct.toFixed(1)}%`;
                    },
                    anchor: 'center',
                    align: 'center',
                    backgroundColor: 'rgba(0,0,0,0.7)',
                    color: '#fff',
                    borderRadius: 12,
                    padding: { left:5, right:5, top:2, bottom:2 },
                    font: { size: 9, weight: 'bold' }
                }
            });
        }
        const ctxComp = document.getElementById('chartLast4Comparison').getContext('2d');
        chartComp = new Chart(ctxComp, {
            type: 'bar',
            data: { labels: topDistricts.map(d => d.distrito.length > 22 ? d.distrito.slice(0,20)+'..' : d.distrito), datasets: datasetsComp },
            options: { responsive: true, maintainAspectRatio: true, plugins: { tooltip: { callbacks: { label: (ctx) => `${ctx.dataset.label}: S/ ${ctx.raw.toLocaleString('es-PE')}` } } }, scales: { y: { title: { display: true, text: 'Monto (S/)' } } } }
        });

        // 3. Gráfico mayor devengado: último (26.05) vs penúltimo (18.05)
        const lastIdx = fullDateColumns.length-1;   // 26.05
        const prevIdx = fullDateColumns.length-2;   // 18.05
        let distCompare = new Map(); // distrito -> {last, prev}
        filteredData.forEach(item => {
            const dName = item.distrito;
            const lastVal = item.devengados[lastIdx];
            const prevVal = item.devengados[prevIdx];
            if (!distCompare.has(dName) || distCompare.get(dName).last < lastVal) {
                distCompare.set(dName, { distrito: dName, last: lastVal, prev: prevVal });
            }
        });
        let compareList = Array.from(distCompare.values());
        compareList.sort((a,b)=>b.last - a.last);
        const top10 = compareList.slice(0,10);
        const ctxVs = document.getElementById('chartLastVsPrev').getContext('2d');
        chartVsPrev = new Chart(ctxVs, {
            type: 'bar',
            data: { labels: top10.map(d => d.distrito.length > 20 ? d.distrito.slice(0,18)+'..' : d.distrito), datasets: [
                { label: '26.05.2026 (Último corte)', data: top10.map(d=>d.last), backgroundColor: '#2b9348', borderRadius: 8 },
                { label: '18.05.2026 (Penúltimo corte)', data: top10.map(d=>d.prev), backgroundColor: '#e9c46a', borderRadius: 8 }
            ] },
            options: { plugins: { datalabels: { anchor: 'end', align: 'top', formatter: (val) => 'S/ '+val.toLocaleString('es-PE') }, tooltip: { callbacks: { label: (ctx) => `${ctx.dataset.label}: S/ ${ctx.raw.toLocaleString('es-PE')}` } } }, scales: { y: { title: { display: true, text: 'Monto Soles' } } } }
        });

        // 4. Gráfico incremento porcentual por distrito (último vs penúltimo)
        let pctArray = [];
        for (let [dist, vals] of distCompare.entries()) {
            let pct = vals.prev === 0 ? (vals.last > 0 ? 100 : 0) : ((vals.last - vals.prev) / vals.prev) * 100;
            pctArray.push({ distrito: dist, pct: pct, montoLast: vals.last });
        }
        pctArray.sort((a,b)=>b.pct - a.pct);
        const topPct = pctArray.slice(0,12);
        const ctxPct = document.getElementById('chartPctIncrease').getContext('2d');
        chartPct = new Chart(ctxPct, {
            type: 'bar',
            data: { labels: topPct.map(d => d.distrito.length > 22 ? d.distrito.slice(0,20)+'..' : d.distrito), datasets: [{ label: 'Incremento % (26.05 vs 18.05)', data: topPct.map(d=> d.pct), backgroundColor: '#f4a261', borderRadius: 8, datalabels: { formatter: (val) => val.toFixed(1)+'%', anchor: 'end', align: 'top', color: '#2d3e50', font: {weight:'bold'} } }] },
            options: { responsive: true, plugins: { tooltip: { callbacks: { label: (ctx) => `Variación: ${ctx.raw.toFixed(2)}%` } } }, scales: { y: { title: { display: true, text: 'Porcentaje (%)' } } } }
        });

        // TABLA con últimos 5 devengados (13.04,20.04,27.04,18.05,26.05) e incremento %
        renderTableWithLast5();
    }

    function renderTableWithLast5() {
        const last5Indices = [2,3,4,5,6]; // desde 13.04 hasta 26.05
        const last5Short = last5Indices.map(i => shortLabels[i]);
        const thead = document.getElementById('tableHeader');
        thead.innerHTML = `<tr><th>Departamento</th><th>Provincia</th><th>Distrito</th>${last5Short.map(l => `<th>${l} (S/)</th>`).join('')}<th>% Inc 26.05 vs 18.05</th></tr>`;
        
        if (filteredData.length === 0) {
            document.getElementById('tableBody').innerHTML = '<tr><td colspan="10">Sin datos con los filtros actuales</td></tr>';
            return;
        }
        // Consolidar por distrito (puede haber duplicados si el CSV tiene filas repetidas, pero sumamos por consistencia)
        let mapDist = new Map();
        filteredData.forEach(item => {
            const key = `${item.departamento}|${item.provincia}|${item.distrito}`;
            if (!mapDist.has(key)) {
                mapDist.set(key, { depto: item.departamento, prov: item.provincia, dist: item.distrito, devs: [...item.devengados] });
            } else {
                let existing = mapDist.get(key);
                for (let i=0; i<existing.devs.length; i++) existing.devs[i] += item.devengados[i];
            }
        });
        let rowsList = Array.from(mapDist.values());
        rowsList.sort((a,b)=> a.dist.localeCompare(b.dist));
        const tbody = document.getElementById('tableBody');
        tbody.innerHTML = '';
        for (let row of rowsList) {
            const last5Vals = last5Indices.map(idx => row.devs[idx]);
            const lastVal = row.devs[6];
            const prevVal = row.devs[5];
            let pct = prevVal === 0 ? (lastVal > 0 ? 100 : 0) : ((lastVal - prevVal) / prevVal) * 100;
            const pctFormatted = pct.toFixed(2) + '%';
            const pctClass = pct >= 0 ? 'badge-pct' : 'badge-pct badge-negative';
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td style="text-align:left">${escapeHtml(row.depto)}</td>
                <td style="text-align:left">${escapeHtml(row.prov)}</td>
                <td style="text-align:left; font-weight:600">${escapeHtml(row.dist)}</td>
                ${last5Vals.map(v => `<td style="text-align:right">S/ ${v.toLocaleString('es-PE')}</td>`).join('')}
                <td style="text-align:center"><span class="${pctClass}">${pctFormatted}</span></td>
            `;
            tbody.appendChild(tr);
        }
    }

    // Exportación a PDF con html2pdf (respetando diseño)
    function exportDashboardPDF() {
        const element = document.getElementById('dashboardMain');
        const opt = {
            margin:        [0.5, 0.5, 0.5, 0.5],
            filename:     `dashboard_devengado_${new Date().toISOString().slice(0,19)}.pdf`,
            image:        { type: 'jpeg', quality: 0.98 },
            html2canvas:  { scale: 2, useCORS: false, letterRendering: true },
            jsPDF:        { unit: 'in', format: 'a3', orientation: 'landscape' }
        };
        html2pdf().set(opt).from(element).save();
    }

    // eventos
    document.getElementById('applyFiltersBtn').addEventListener('click', () => {
        applyFiltersAndRender();
    });
    document.getElementById('exportPDFBtn').addEventListener('click', exportDashboardPDF);

    // Inicializar
    fetchData();
</script>
</body>
</html>
