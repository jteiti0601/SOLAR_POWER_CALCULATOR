[solar-global-advanced.html](https://github.com/user-attachments/files/23688892/solar-global-advanced.html)
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Global Solar Energy Calculator</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    :root {
      /* Default (light) theme variables */
      --text: #0f172a;
      --muted: #334155;
      --card-bg: rgba(255,255,255,0.85);
      --border: rgba(0,0,0,0.1);
      --btn-text: #052e16;
      --accent: #22c55e;
      --accent2: #06b6d4;
      --danger: #ef4444;
      --bg1: #f0f9ff; --bg2: #fff7ed; --bg3: #fef9c3; --bg4: #ecfeff;
    }
    body.dark {
      --text: #e5e7eb;
      --muted: #cbd5e1;
      --card-bg: rgba(17,24,39,0.7);
      --border: rgba(255,255,255,0.2);
      --btn-text: #022c22;
      --accent: #22c55e;
      --accent2: #06b6d4;
      --danger: #ef4444;
      --bg1: #0ea5e9; --bg2: #22c55e; --bg3: #f59e0b; --bg4: #3b82f6;
    }
    html, body {
      margin: 0; padding: 0; height: 100%;
      font-family: Inter, system-ui, -apple-system, Segoe UI, Roboto, Arial;
      color: var(--text);
      background: linear-gradient(-45deg, var(--bg1), var(--bg2), var(--bg3), var(--bg4));
      background-size: 400% 400%;
      animation: gradientBG 18s ease infinite;
    }
    @keyframes gradientBG {
      0% { background-position: 0% 50%; }
      50% { background-position: 100% 50%; }
      100% { background-position: 0% 50%; }
    }
    header { text-align: center; padding: 24px 16px; }
    header h1 { margin: 0; font-size: 1.8rem; }
    header p { margin: 8px 0 0; color: var(--muted); }
    .actions { display: flex; gap: 8px; justify-content: center; flex-wrap: wrap; margin-top: 12px; }

    .container {
      max-width: 1200px; margin: 0 auto; padding: 16px;
      display: grid; grid-template-columns: 1fr; gap: 16px;
    }
    @media (min-width: 980px) { .grid-2 { grid-template-columns: 1fr 1fr; } }

    .card {
      backdrop-filter: blur(8px);
      background: var(--card-bg);
      border: 1px solid var(--border);
      border-radius: 16px;
      padding: 16px;
      box-shadow: 0 12px 30px rgba(0,0,0,0.15);
    }
    .card h2 { margin: 0 0 10px; font-size: 1.2rem; }
    .row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
    @media (max-width: 720px) { .row { grid-template-columns: 1fr; } }
    label { font-size: 0.95rem; color: var(--muted); }
    input, select {
      margin-top: 6px; padding: 10px 12px; font-size: 1rem;
      border-radius: 12px; border: 1px solid var(--border);
      background: rgba(255,255,255,0.6); color: var(--text);
      outline: none;
    }
    body.dark input, body.dark select {
      background: rgba(10,15,25,0.6);
    }
    input::placeholder { color: #64748b; }
    .inline { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
    .btn {
      background: var(--accent); color: var(--btn-text); font-weight: 700; border: none;
      padding: 10px 14px; border-radius: 12px; cursor: pointer;
    }
    .btn.secondary { background: var(--accent2); }
    .btn.danger { background: var(--danger); }
    .btn.ghost {
      background: transparent; border: 1px solid var(--border); color: var(--text);
    }
    .metrics {
      display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin-top: 12px;
    }
    @media (max-width: 980px) { .metrics { grid-template-columns: 1fr 1fr; } }
    @media (max-width: 640px) { .metrics { grid-template-columns: 1fr; } }
    .metric {
      background: rgba(255,255,255,0.5);
      border: 1px solid var(--border);
      border-radius: 12px; padding: 12px;
    }
    body.dark .metric { background: rgba(0,0,0,0.25); }
    .metric .label { color: var(--muted); font-size: 0.9rem; }
    .metric .value { font-size: 1.3rem; font-weight: 800; margin-top: 4px; }
    .hint { color: var(--muted); font-size: 0.85rem; margin-top: 8px; }
    footer { text-align: center; color: var(--muted); padding: 20px 10px; font-size: 0.85rem; }
    .toast {
      position: fixed; bottom: 16px; right: 16px; background: var(--card-bg);
      border: 1px solid var(--border); border-radius: 10px; padding: 10px 12px;
      box-shadow: 0 8px 20px rgba(0,0,0,0.2); display: none;
    }
  </style>
</head>
<body class="light">
  <header>
    <h1>☀️ Global Solar Energy Calculator</h1>
    <p>Estimate solar production, savings, battery coverage, CO₂ impact, and long‑term ROI. Use presets or manual inputs—works anywhere.</p>
    <div class="actions">
      <button id="themeToggle" class="btn ghost" aria-label="Toggle dark mode">Toggle Dark Mode</button>
      <button id="exportCsv" class="btn secondary" aria-label="Export results as CSV">Export CSV</button>
      <button id="shareLink" class="btn" aria-label="Copy shareable link">Share</button>
    </div>
  </header>

  <div class="container grid-2">
    <section class="card" aria-label="Input form">
      <h2>Inputs</h2>

      <div class="row">
        <div>
          <label for="locationPreset">Location preset</label>
          <select id="locationPreset" onchange="onLocationPresetChange()">
            <option value="manual">Manual entry</option>
            <option value="Suva">Suva</option>
            <option value="Nadi">Nadi</option>
            <option value="Labasa">Labasa</option>
            <option value="Sydney">Sydney</option>
            <option value="SanFrancisco">San Francisco</option>
            <option value="Stockholm">Stockholm</option>
          </select>
        </div>
        <div>
          <label for="currency">Currency</label>
          <select id="currency">
            <option value="FJD">FJD</option>
            <option value="USD">USD</option>
            <option value="EUR">EUR</option>
            <option value="AUD">AUD</option>
            <option value="INR">INR</option>
          </select>
        </div>
      </div>

      <div class="row">
        <div>
          <label for="irradiance">Average solar irradiance (kWh/m²/day)</label>
          <input id="irradiance" type="number" step="0.1" value="5.0" />
        </div>
        <div>
          <label for="co2Intensity">Grid carbon intensity (kg CO₂/kWh)</label>
          <input id="co2Intensity" type="number" step="0.01" value="0.70" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="panelPower">Panel power (kW per panel)</label>
          <input id="panelPower" type="number" step="0.01" value="0.35" />
        </div>
        <div>
          <label for="numPanels">Number of panels</label>
          <input id="numPanels" type="number" value="10" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="efficiency">System efficiency (0–1)</label>
          <input id="efficiency" type="number" step="0.01" value="0.80" />
        </div>
        <div>
          <label for="lossesCombined">Inverter & other losses (0–1)</label>
          <input id="lossesCombined" type="number" step="0.01" value="0.90" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="dailyUsage">Daily household usage (kWh)</label>
          <input id="dailyUsage" type="number" step="0.1" value="20" />
        </div>
        <div>
          <label for="costPerKwh">Electricity cost (per kWh)</label>
          <input id="costPerKwh" type="number" step="0.01" value="0.50" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="systemCost">Installed system cost</label>
          <input id="systemCost" type="number" step="0.01" value="8000" />
        </div>
        <div>
          <label for="batteryKwh">Battery capacity (kWh)</label>
          <input id="batteryKwh" type="number" step="0.1" value="5" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="degradation">Panel degradation (% per year)</label>
          <input id="degradation" type="number" step="0.1" value="0.5" />
        </div>
        <div>
          <label for="seasonalVar">Seasonal variance (±%)</label>
          <input id="seasonalVar" type="number" step="1" value="10" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="inflationRate">Inflation rate (% per year)</label>
          <input id="inflationRate" type="number" step="0.1" value="2.0" />
        </div>
        <div>
          <label for="priceEscalation">Electricity price escalation (% per year)</label>
          <input id="priceEscalation" type="number" step="0.1" value="3.0" />
        </div>
      </div>

      <div class="row">
        <div>
          <label for="discountRate">Discount rate (% per year)</label>
          <input id="discountRate" type="number" step="0.1" value="5.0" />
        </div>
        <div class="inline" style="align-items:flex-end;">
          <button class="btn" onclick="calculate()">Calculate</button>
          <button class="btn secondary" onclick="resetDefaults()">Reset</button>
          <button class="btn danger" onclick="clearAll()">Clear</button>
        </div>
      </div>

      <p class="hint">If using a preset, irradiance and grid CO₂ auto‑fill. Efficiency × losses should reflect real‑world conditions (orientation, wiring, heat, inverter).</p>
    </section>

    <section class="card" aria-label="Key results">
      <h2>Key results</h2>
      <div class="metrics">
        <div class="metric">
          <div class="label">Daily production</div>
          <div class="value" id="dailyProd">—</div>
        </div>
        <div class="metric">
          <div class="label">Monthly production</div>
          <div class="value" id="monthlyProd">—</div>
        </div>
        <div class="metric">
          <div class="label">Estimated monthly savings</div>
          <div class="value" id="monthlySavings">—</div>
        </div>
      </div>
      <div class="metrics">
        <div class="metric">
          <div class="label">Battery backup hours</div>
          <div class="value" id="batteryHours">—</div>
        </div>
        <div class="metric">
          <div class="label">Annual CO₂ avoided</div>
          <div class="value" id="annualCo2">—</div>
        </div>
        <div class="metric">
          <div class="label">Simple payback</div>
          <div class="value" id="paybackYears">—</div>
        </div>
      </div>
      <div class="metrics">
        <div class="metric">
          <div class="label">NPV (20 years)</div>
          <div class="value" id="npv20">—</div>
        </div>
        <div class="metric">
          <div class="label">IRR (approx)</div>
          <div class="value" id="irrApprox">—</div>
        </div>
        <div class="metric">
          <div class="label">Currency</div>
          <div class="value" id="currencyDisplay">—</div>
        </div>
      </div>
    </section>

    <section class="card" aria-label="Daily comparison chart">
      <h2>Production vs. usage</h2>
      <canvas id="dailyChart" height="220" aria-label="Daily production vs usage chart"></canvas>
      <p class="hint">Compare estimated daily solar production with your daily household usage.</p>
    </section>

    <section class="card" aria-label="Monthly seasonal chart">
      <h2>12‑month seasonal curve</h2>
      <canvas id="monthlyChart" height="220" aria-label="Monthly production chart with seasonality"></canvas>
      <p class="hint">Monthly production with sinusoidal seasonal variation and first‑year degradation applied.</p>
    </section>

    <section class="card" aria-label="Long-term savings chart">
      <h2>20‑year savings projection</h2>
      <canvas id="longTermChart" height="220" aria-label="20-year savings vs system cost chart"></canvas>
      <p class="hint">Cumulative savings vs. system cost, with annual degradation and price escalation applied to savings.</p>
    </section>
  </div>

  <div id="toast" class="toast" role="status" aria-live="polite"></div>

  <footer>Estimates only. For site‑specific design, consult local installers and use precise irradiance data.</footer>

  <script>
    // Presets: irradiance (kWh/m²/day) and grid CO₂ intensity (kg CO2/kWh)
    const LOCATION_PRESETS = {
      Suva:          { irradiance: 5.0, co2: 0.70 },
      Nadi:          { irradiance: 5.2, co2: 0.70 },
      Labasa:        { irradiance: 4.8, co2: 0.70 },
      Sydney:        { irradiance: 4.5, co2: 0.70 },
      SanFrancisco:  { irradiance: 4.6, co2: 0.20 },
      Stockholm:     { irradiance: 2.8, co2: 0.02 }
    };
    const CURRENCY_SYMBOLS = { USD: "$", EUR: "€", AUD: "A$", INR: "₹", FJD: "FJ$" };
    const MONTHS = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];

    let dailyChart, monthlyChart, longTermChart;

    function parse(id) {
      const el = document.getElementById(id);
      const val = parseFloat(el.value);
      return Number.isNaN(val) ? 0 : val;
    }
    function clamp(val, min, max) { return Math.min(Math.max(val, min), max); }
    function toast(msg) {
      const t = document.getElementById("toast");
      t.textContent = msg;
      t.style.display = "block";
      setTimeout(() => t.style.display = "none", 2000);
    }

    function onLocationPresetChange() {
      const preset = document.getElementById("locationPreset").value;
      const irrEl = document.getElementById("irradiance");
      const co2El = document.getElementById("co2Intensity");
      if (preset === "manual") return;
      const p = LOCATION_PRESETS[preset];
      irrEl.value = p.irradiance;
      co2El.value = p.co2;
      toast(`Preset applied: ${preset}`);
    }

    function effectiveEfficiency(sysEff, lossesCombined) {
      return clamp(sysEff, 0, 1) * clamp(lossesCombined, 0, 1);
    }
    function calcDailyProduction(irradiance, capacityKw, effCombined) {
      return irradiance * capacityKw * effCombined;
    }

    function fmtCurrency(val, symbol) {
      return `${symbol} ${Number.isFinite(val) ? val.toFixed(2) : "—"}`;
    }

    // Advanced financials
    function calcYearlySavings(baseMonthlySavings, yearIndex, priceEscalationPct, degradationPct) {
      const degFactor = Math.pow(1 - degradationPct/100, yearIndex);
      const escalFactor = Math.pow(1 + priceEscalationPct/100, yearIndex);
      return baseMonthlySavings * 12 * degFactor * escalFactor;
    }
    function calcNPV(baseMonthlySavings, years, priceEscalationPct, degradationPct, discountRatePct) {
      let npv = 0;
      for (let y = 1; y <= years; y++) {
        const cash = calcYearlySavings(baseMonthlySavings, y, priceEscalationPct, degradationPct);
        npv += cash / Math.pow(1 + discountRatePct/100, y);
      }
      return npv;
    }
    // Simple IRR approximation via bisection on 0-50%
    function approxIRR(baseMonthlySavings, years, priceEscalationPct, degradationPct, initialCost) {
      let low = 0, high = 0.5; // 0%..50%
      for (let i = 0; i < 32; i++) {
        const mid = (low + high)/2;
        const npv = calcNPV(baseMonthlySavings, years, priceEscalationPct, degradationPct, mid*100) - initialCost;
        if (npv > 0) low = mid; else high = mid;
      }
      return ((low + high)/2) * 100;
    }

    function calculate() {
      const currency = document.getElementById("currency").value;
      const symbol = CURRENCY_SYMBOLS[currency] || "";
      document.getElementById("currencyDisplay").innerText = currency;

      const irradiance = parse("irradiance");
      const co2Intensity = parse("co2Intensity");
      const panelPower = parse("panelPower");
      const numPanels = parseInt(document.getElementById("numPanels").value || "0", 10);
      const sysEff = parse("efficiency");
      const lossesCombined = parse("lossesCombined");
      const dailyUsage = parse("dailyUsage");
      const costPerKwh = parse("costPerKwh");
      const systemCost = parse("systemCost");
      const batteryKwh = parse("batteryKwh");
      const degradationPct = parse("degradation"); // %/year
      const seasonalVarPct = parse("seasonalVar");
      const inflationRatePct = parse("inflationRate"); // not used directly, displayed purpose
      const priceEscalationPct = parse("priceEscalation");
      const discountRatePct = parse("discountRate");

      const totalCapacityKw = panelPower * (Number.isNaN(numPanels) ? 0 : numPanels);
      const effCombined = effectiveEfficiency(sysEff, lossesCombined);

      const dailyProd = calcDailyProduction(irradiance, totalCapacityKw, effCombined);
      const monthlyProd = dailyProd * 30;

      const monthlySavings = Math.min(monthlyProd, dailyUsage * 30) * costPerKwh;
      const batteryHours = dailyUsage > 0 ? (batteryKwh / dailyUsage) * 24 : 0;
      const annualCo2 = monthlyProd * 12 * co2Intensity;

      const annualSavings = monthlySavings * 12;
      const paybackYears = annualSavings > 0 ? (systemCost / annualSavings) : Infinity;

      // Advanced NPV and IRR
      const npv20 = calcNPV(monthlySavings, 20, priceEscalationPct, degradationPct, discountRatePct);
      const irrApprox = approxIRR(monthlySavings, 20, priceEscalationPct, degradationPct, systemCost);

      // Update metrics
      document.getElementById("dailyProd").innerText = `${dailyProd.toFixed(2)} kWh`;
      document.getElementById("monthlyProd").innerText = `${monthlyProd.toFixed(0)} kWh`;
      document.getElementById("monthlySavings").innerText = fmtCurrency(monthlySavings, symbol);
      document.getElementById("batteryHours").innerText = Number.isFinite(batteryHours) ? `${batteryHours.toFixed(1)} hrs` : "—";
      document.getElementById("annualCo2").innerText = `${annualCo2.toFixed(0)} kg`;
      document.getElementById("paybackYears").innerText = (paybackYears === Infinity) ? "—" : `${paybackYears.toFixed(1)} yrs`;
      document.getElementById("npv20").innerText = fmtCurrency(npv20, symbol);
      document.getElementById("irrApprox").innerText = Number.isFinite(irrApprox) ? `${irrApprox.toFixed(1)}%` : "—";

      updateDailyChart(dailyProd, dailyUsage);
      updateMonthlyChart(monthlyProd, seasonalVarPct, degradationPct);
      updateLongTermChart(monthlySavings, systemCost, degradationPct, priceEscalationPct, symbol);
    }

    function updateDailyChart(dailyProd, dailyUsage) {
      const ctx = document.getElementById("dailyChart").getContext("2d");
      if (dailyChart) dailyChart.destroy();
      dailyChart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: ["Daily Solar Production", "Daily Usage"],
          datasets: [{
            label: "kWh/day",
            data: [dailyProd, dailyUsage],
            backgroundColor: ["#22c55e", "#06b6d4"]
          }]
        },
        options: {
          responsive: true,
          plugins: { legend: { display: false } },
          scales: {
            y: { beginAtZero: true, ticks: { color: getTickColor() }, grid: { color: getGridColor() } },
            x: { ticks: { color: getTickColor() } }
          }
        }
      });
    }

    function updateMonthlyChart(monthlyProdBase, seasonalVarPct, degradationPct) {
      const ctx = document.getElementById("monthlyChart").getContext("2d");
      if (monthlyChart) monthlyChart.destroy();
      const seasonal = clamp(seasonalVarPct, 0, 50) / 100;
      const degradationFactor = 1 - (degradationPct / 100); // first-year approximation

      const monthsProd = MONTHS.map((_, i) => {
        const angle = (i / 12) * 2 * Math.PI;
        const seasonalFactor = 1 + seasonal * Math.sin(angle - Math.PI/2);
        return monthlyProdBase * seasonalFactor * degradationFactor;
      });

      monthlyChart = new Chart(ctx, {
        type: "line",
        data: { labels: MONTHS,
          datasets: [{ label: "Monthly production (kWh)", data: monthsProd, borderColor: "#f59e0b", backgroundColor: "rgba(245,158,11,0.25)", tension: 0.25 }]
        },
        options: {
          responsive: true,
          plugins: { legend: { labels: { color: getTickColor() } } },
          scales: {
            y: { beginAtZero: true, ticks: { color: getTickColor() }, grid: { color: getGridColor() } },
            x: { ticks: { color: getTickColor() } }
          }
        }
      });
    }

    function updateLongTermChart(monthlySavingsBase, systemCost, degradationPct, priceEscalationPct, symbol) {
      const ctx = document.getElementById("longTermChart").getContext("2d");
      if (longTermChart) longTermChart.destroy();

      const years = Array.from({length: 20}, (_, i) => i + 1);
      const yearlySavings = years.map(y => calcYearlySavings(monthlySavingsBase, y, priceEscalationPct, degradationPct));
      const cumulativeSavings = yearlySavings.map((_, i) => yearlySavings.slice(0, i + 1).reduce((a, b) => a + b, 0));

      longTermChart = new Chart(ctx, {
        type: "line",
        data: {
          labels: years.map(y => `${y} yr`),
          datasets: [
            { label: `Cumulative savings (${symbol})`, data: cumulativeSavings, borderColor: "#06b6d4", backgroundColor: "rgba(6,182,212,0.25)", tension: 0.25 },
            { label: `System cost (${symbol})`, data: years.map(() => systemCost), borderColor: "#ef4444", backgroundColor: "rgba(239,68,68,0.15)" }
          ]
        },
        options: {
          responsive: true,
          plugins: { legend: { labels: { color: getTickColor() } } },
          scales: {
            y: { beginAtZero: true, ticks: { color: getTickColor() }, grid: { color: getGridColor() } },
            x: { ticks: { color: getTickColor() } }
          },
          interaction: { mode: "index", intersect: false },
        }
      });
    }

    function getTickColor() { return document.body.classList.contains("dark") ? "#e5e7eb" : "#0f172a"; }
    function getGridColor() { return document.body.classList.contains("dark") ? "rgba(255,255,255,0.2)" : "rgba(0,0,0,0.1)"; }

    // Theme toggle
    document.getElementById("themeToggle").addEventListener("click", () => {
      document.body.classList.toggle("dark");
      document.body.classList.toggle("light");
      calculate(); // refresh charts colors
      toast(document.body.classList.contains("dark") ? "Dark mode on" : "Light mode on");
      localStorage.setItem("solar-theme", document.body.classList.contains("dark") ? "dark" : "light");
    });

    // Export CSV
    document.getElementById("exportCsv").addEventListener("click", () => {
      const data = collectData();
      const rows = [
        ["Metric","Value"],
        ["Location preset", document.getElementById("locationPreset").value],
        ["Currency", document.getElementById("currency").value],
        ["Irradiance (kWh/m²/day)", data.irradiance],
        ["Grid CO₂ (kg/kWh)", data.co2Intensity],
        ["Panel power (kW)", data.panelPower],
        ["Number of panels", data.numPanels],
        ["System efficiency", data.efficiency],
        ["Losses combined", data.lossesCombined],
        ["Daily usage (kWh)", data.dailyUsage],
        ["Cost per kWh", data.costPerKwh],
        ["System cost", data.systemCost],
        ["Battery (kWh)", data.batteryKwh],
        ["Degradation (%/yr)", data.degradation],
        ["Seasonal variance (%)", data.seasonalVar],
        ["Inflation (%/yr)", data.inflationRate],
        ["Price escalation (%/yr)", data.priceEscalation],
        ["Discount rate (%/yr)", data.discountRate],
        ["Daily production (kWh)", data.dailyProd.toFixed(2)],
        ["Monthly production (kWh)", data.monthlyProd.toFixed(0)],
        ["Monthly savings", data.monthlySavings.toFixed(2)],
        ["Battery hours", data.batteryHours.toFixed(1)],
        ["Annual CO₂ (kg)", data.annualCo2.toFixed(0)],
        ["Payback (yrs)", Number.isFinite(data.paybackYears) ? data.paybackYears.toFixed(1) : "—"],
        ["NPV 20y", data.npv20.toFixed(2)],
        ["IRR approx (%)", data.irrApprox.toFixed(1)]
      ];
      const csv = rows.map(r => r.join(",")).join("\n");
      const blob = new Blob([csv], { type: "text/csv;charset=utf-8;" });
      const url = URL.createObjectURL(blob);
      const a = document.createElement("a");
      a.href = url; a.download = "solar-calculator-results.csv";
      document.body.appendChild(a); a.click(); document.body.removeChild(a);
      URL.revokeObjectURL(url);
      toast("CSV downloaded");
    });

    // Share link with query params
    document.getElementById("shareLink").addEventListener("click", () => {
      const params = new URLSearchParams();
      ["locationPreset","currency","irradiance","co2Intensity","panelPower","numPanels","efficiency","lossesCombined","dailyUsage","costPerKwh","systemCost","batteryKwh","degradation","seasonalVar","inflationRate","priceEscalation","discountRate"]
        .forEach(id => params.set(id, document.getElementById(id).value));
      const shareUrl = `${location.origin}${location.pathname}?${params.toString()}`;
      navigator.clipboard.writeText(shareUrl).then(() => toast("Link copied to clipboard"));
    });

    function collectData() {
      const currency = document.getElementById("currency").value;
      const symbol = CURRENCY_SYMBOLS[currency] || "";
      const irradiance = parse("irradiance");
      const co2Intensity = parse("co2Intensity");
      const panelPower = parse("panelPower");
      const numPanels = parseInt(document.getElementById("numPanels").value || "0", 10);
      const sysEff = parse("efficiency");
      const lossesCombined = parse("lossesCombined");
      const dailyUsage = parse("dailyUsage");
      const costPerKwh = parse("costPerKwh");
      const systemCost = parse("systemCost");
      const batteryKwh = parse("batteryKwh");
      const degradation = parse("degradation");
      const seasonalVar = parse("seasonalVar");
      const inflationRate = parse("inflationRate");
      const priceEscalation = parse("priceEscalation");
      const discountRate = parse("discountRate");

      const totalCapacityKw = panelPower * (Number.isNaN(numPanels) ? 0 : numPanels);
      const effCombined = effectiveEfficiency(sysEff, lossesCombined);
      const dailyProd = calcDailyProduction(irradiance, totalCapacityKw, effCombined);
      const monthlyProd = dailyProd * 30;
      const monthlySavings = Math.min(monthlyProd, dailyUsage * 30) * costPerKwh;
      const batteryHours = dailyUsage > 0 ? (batteryKwh / dailyUsage) * 24 : 0;
      const annualCo2 = monthlyProd * 12 * co2Intensity;
      const annualSavings = monthlySavings * 12;
      const paybackYears = annualSavings > 0 ? (systemCost / annualSavings) : Infinity;
      const npv20 = calcNPV(monthlySavings, 20, priceEscalation, degradation, discountRate);
      const irrApprox = approxIRR(monthlySavings, 20, priceEscalation, degradation, systemCost);

      return { currency, symbol, irradiance, co2Intensity, panelPower, numPanels, efficiency: sysEff, lossesCombined, dailyUsage, costPerKwh, systemCost, batteryKwh, degradation, seasonalVar, inflationRate, priceEscalation, discountRate, dailyProd, monthlyProd, monthlySavings, batteryHours, annualCo2, paybackYears, npv20, irrApprox };
    }

    // Prefill from query params
    function applyQueryParams() {
      const params = new URLSearchParams(location.search);
      ["locationPreset","currency","irradiance","co2Intensity","panelPower","numPanels","efficiency","lossesCombined","dailyUsage","costPerKwh","systemCost","batteryKwh","degradation","seasonalVar","inflationRate","priceEscalation","discountRate"]
        .forEach(id => {
          const val = params.get(id);
          if (val !== null) document.getElementById(id).value = val;
        });
      const preset = params.get("locationPreset");
      if (preset && preset !== "manual" && LOCATION_PRESETS[preset]) {
        onLocationPresetChange();
      }
    }

    function resetDefaults() {
      document.getElementById("locationPreset").value = "manual";
      document.getElementById("currency").value = "FJD";
      document.getElementById("irradiance").value = 5.0;
      document.getElementById("co2Intensity").value = 0.70;
      document.getElementById("panelPower").value = 0.35;
      document.getElementById("numPanels").value = 10;
      document.getElementById("efficiency").value = 0.80;
      document.getElementById("lossesCombined").value = 0.90;
      document.getElementById("dailyUsage").value = 20;
      document.getElementById("costPerKwh").value = 0.50;
      document.getElementById("systemCost").value = 8000;
      document.getElementById("batteryKwh").value = 5;
      document.getElementById("degradation").value = 0.5;
      document.getElementById("seasonalVar").value = 10;
      document.getElementById("inflationRate").value = 2.0;
      document.getElementById("priceEscalation").value = 3.0;
      document.getElementById("discountRate").value = 5.0;
      calculate();
    }

    function clearAll() {
      ["locationPreset","currency","irradiance","co2Intensity","panelPower","numPanels","efficiency","lossesCombined","dailyUsage","costPerKwh","systemCost","batteryKwh","degradation","seasonalVar","inflationRate","priceEscalation","discountRate"]
        .forEach(id => {
          const el = document.getElementById(id);
          if (el.tagName.toLowerCase() === "select") el.selectedIndex = 0; else el.value = "";
        });
      if (dailyChart) dailyChart.destroy();
      if (monthlyChart) monthlyChart.destroy();
      if (longTermChart) longTermChart.destroy();
      ["dailyProd","monthlyProd","monthlySavings","batteryHours","annualCo2","paybackYears","npv20","irrApprox","currencyDisplay"]
        .forEach(id => document.getElementById(id).innerText = "—");
    }

    // Theme persistence
    (function initTheme() {
      const saved = localStorage.getItem("solar-theme");
      if (saved === "dark") { document.body.classList.add("dark"); document.body.classList.remove("light"); }
      else { document.body.classList.add("light"); document.body.classList.remove("dark"); }
    })();

    // Initialize
    applyQueryParams();
    calculate();
  </script>
</body>
</html>
