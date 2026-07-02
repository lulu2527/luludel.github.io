<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Temps Écoulé</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;600;700&family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #0D0D14;
    --surface: #16162A;
    --surface2: #1E1E38;
    --accent: #7C6AF7;
    --accent-dim: rgba(124, 106, 247, 0.15);
    --accent-glow: rgba(124, 106, 247, 0.35);
    --text: #F0EEF8;
    --text-muted: #8A87A8;
    --text-dim: #4A4870;
    --success: #4ECDC4;
    --border: rgba(124, 106, 247, 0.2);
  }

  html, body {
    height: 100%;
    background: var(--bg);
    color: var(--text);
    font-family: 'Inter', sans-serif;
    overflow: hidden;
  }

  .app {
    max-width: 430px;
    height: 100dvh;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    position: relative;
    overflow: hidden;
  }

  /* Ambient background glow */
  .app::before {
    content: '';
    position: absolute;
    top: -80px;
    left: 50%;
    transform: translateX(-50%);
    width: 300px;
    height: 300px;
    background: radial-gradient(circle, rgba(124,106,247,0.12) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }

  /* ── HEADER ── */
  .header {
    position: relative;
    z-index: 1;
    padding: 52px 24px 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .header-title {
    font-family: 'Inter', sans-serif;
    font-size: 13px;
    font-weight: 500;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .status-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--accent);
    box-shadow: 0 0 8px var(--accent);
    animation: blink 2s ease-in-out infinite;
  }

  .status-dot.inactive {
    background: var(--text-dim);
    box-shadow: none;
    animation: none;
  }

  @keyframes blink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.3; }
  }

  /* ── SCROLL CONTENT ── */
  .content {
    position: relative;
    z-index: 1;
    flex: 1;
    overflow-y: auto;
    padding: 0 20px 32px;
    scrollbar-width: none;
  }
  .content::-webkit-scrollbar { display: none; }

  /* ── DATE/TIME PICKER ── */
  .picker-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 20px;
    margin-bottom: 16px;
  }

  .picker-label {
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-muted);
    margin-bottom: 14px;
  }

  .picker-row {
    display: flex;
    gap: 10px;
  }

  .picker-field {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 6px;
  }

  .picker-field label {
    font-size: 11px;
    color: var(--text-dim);
    font-weight: 500;
  }

  .picker-field input {
    width: 100%;
    background: var(--surface2);
    border: 1px solid rgba(255,255,255,0.06);
    border-radius: 10px;
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    font-size: 14px;
    padding: 10px 12px;
    outline: none;
    transition: border-color 0.2s;
    -webkit-appearance: none;
    appearance: none;
  }

  .picker-field input:focus {
    border-color: var(--accent);
    box-shadow: 0 0 0 3px var(--accent-dim);
  }

  /* Color scheme for native date/time pickers */
  input[type="date"]::-webkit-calendar-picker-indicator,
  input[type="time"]::-webkit-calendar-picker-indicator {
    filter: invert(0.6) sepia(1) hue-rotate(220deg) saturate(3);
    cursor: pointer;
  }

  /* ── CTA BUTTON ── */
  .btn-start {
    width: 100%;
    padding: 15px;
    background: var(--accent);
    border: none;
    border-radius: 14px;
    color: #fff;
    font-family: 'Inter', sans-serif;
    font-size: 15px;
    font-weight: 600;
    letter-spacing: 0.02em;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s, background 0.15s;
    margin-top: 4px;
    box-shadow: 0 4px 20px rgba(124, 106, 247, 0.4);
  }

  .btn-start:active {
    transform: scale(0.97);
    box-shadow: 0 2px 10px rgba(124, 106, 247, 0.3);
  }

  .btn-start:hover {
    background: #8E7EF9;
  }

  /* ── COUNTER DISPLAY ── */
  .counter-section {
    margin-top: 4px;
  }

  .since-label {
    font-size: 11px;
    font-weight: 500;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-muted);
    text-align: center;
    margin-bottom: 6px;
  }

  .since-date {
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--accent);
    text-align: center;
    margin-bottom: 20px;
    opacity: 0.9;
  }

  /* Grid of time blocks */
  .time-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-bottom: 10px;
  }

  .time-block {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 16px 12px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.3s;
  }

  .time-block::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--accent-dim);
    opacity: 0;
    transition: opacity 0.3s;
  }

  .time-block.pulse::before {
    animation: flash 0.4s ease-out;
  }

  @keyframes flash {
    0% { opacity: 0.6; }
    100% { opacity: 0; }
  }

  .time-value {
    font-family: 'JetBrains Mono', monospace;
    font-size: 32px;
    font-weight: 700;
    color: var(--text);
    line-height: 1;
    letter-spacing: -0.02em;
    transition: color 0.2s;
  }

  .time-block.highlight .time-value {
    color: var(--accent);
    text-shadow: 0 0 20px var(--accent-glow);
  }

  .time-unit {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-dim);
  }

  /* Seconds full width */
  .time-block.wide {
    grid-column: 1 / -1;
    flex-direction: row;
    justify-content: center;
    gap: 16px;
    padding: 14px 20px;
  }

  .time-block.wide .time-value {
    font-size: 28px;
  }

  /* ── TOTAL LINE ── */
  .total-row {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 14px 18px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 10px;
  }

  .total-label {
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .total-value {
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--text-muted);
  }

  /* ── PLACEHOLDER ── */
  .placeholder {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    gap: 12px;
    color: var(--text-dim);
  }

  .placeholder-icon {
    font-size: 40px;
    opacity: 0.4;
  }

  .placeholder-text {
    font-size: 14px;
    text-align: center;
    line-height: 1.6;
    max-width: 220px;
  }

  /* ── RESET BUTTON ── */
  .btn-reset {
    width: 100%;
    padding: 12px;
    background: transparent;
    border: 1px solid var(--border);
    border-radius: 12px;
    color: var(--text-muted);
    font-family: 'Inter', sans-serif;
    font-size: 13px;
    font-weight: 500;
    cursor: pointer;
    margin-top: 14px;
    transition: background 0.2s, color 0.2s;
  }

  .btn-reset:active {
    background: var(--surface2);
  }

  /* ── HIDDEN ── */
  .hidden { display: none !important; }
</style>
</head>
<body>
<div class="app">

  <div class="header">
    <span class="header-title">Temps Écoulé</span>
    <div class="status-dot inactive" id="statusDot"></div>
  </div>

  <div class="content">

    <!-- Counter -->
    <div class="counter-section" id="counterSection">

      <div class="since-label">Depuis le</div>
      <div class="since-date" id="sinceDate">—</div>

      <div class="time-grid">
        <div class="time-block" id="block-years">
          <div class="time-value" id="val-years">0</div>
          <div class="time-unit">Années</div>
        </div>
        <div class="time-block" id="block-months">
          <div class="time-value" id="val-months">0</div>
          <div class="time-unit">Mois</div>
        </div>
        <div class="time-block" id="block-weeks">
          <div class="time-value" id="val-weeks">0</div>
          <div class="time-unit">Semaines</div>
        </div>
        <div class="time-block" id="block-days">
          <div class="time-value" id="val-days">0</div>
          <div class="time-unit">Jours</div>
        </div>
        <div class="time-block" id="block-hours">
          <div class="time-value" id="val-hours">0</div>
          <div class="time-unit">Heures</div>
        </div>
        <div class="time-block" id="block-minutes">
          <div class="time-value" id="val-minutes">0</div>
          <div class="time-unit">Minutes</div>
        </div>
        <div class="time-block wide highlight" id="block-seconds">
          <div class="time-value" id="val-seconds">0</div>
          <div class="time-unit">Secondes</div>
        </div>
      </div>

      <!-- Total secondes -->
      <div class="total-row">
        <span class="total-label">Total secondes</span>
        <span class="total-value" id="val-total">0</span>
      </div>

    </div>

  </div>
</div>

<script>
  let prevValues = {};
  const targetDate = new Date('2026-06-11T06:14:00');

  document.getElementById('sinceDate').textContent = 'mercredi 11 juin 2026 à 06:14';
  document.getElementById('statusDot').classList.remove('inactive');

  function tick() {
    const now = new Date();
    const diffMs = now - targetDate;

    if (diffMs < 0) return;

    const totalSeconds = Math.floor(diffMs / 1000);

    // ── Decompose ──
    let remaining = diffMs;

    const years = Math.floor(remaining / (365.25 * 24 * 3600 * 1000));
    remaining -= years * 365.25 * 24 * 3600 * 1000;

    const months = Math.floor(remaining / (30.4375 * 24 * 3600 * 1000));
    remaining -= months * 30.4375 * 24 * 3600 * 1000;

    const weeks = Math.floor(remaining / (7 * 24 * 3600 * 1000));
    remaining -= weeks * 7 * 24 * 3600 * 1000;

    const days = Math.floor(remaining / (24 * 3600 * 1000));
    remaining -= days * 24 * 3600 * 1000;

    const hours = Math.floor(remaining / (3600 * 1000));
    remaining -= hours * 3600 * 1000;

    const minutes = Math.floor(remaining / (60 * 1000));
    remaining -= minutes * 60 * 1000;

    const seconds = Math.floor(remaining / 1000);

    const values = { years, months, weeks, days, hours, minutes, seconds };

    // Update DOM + pulse on change
    for (const [key, val] of Object.entries(values)) {
      const el = document.getElementById(`val-${key}`);
      const block = document.getElementById(`block-${key}`);
      if (el) {
        const display = String(val).padStart(key === 'seconds' ? 2 : 1, '');
        if (prevValues[key] !== val) {
          el.textContent = display;
          if (block && prevValues[key] !== undefined) {
            block.classList.remove('pulse');
            void block.offsetWidth; // reflow
            block.classList.add('pulse');
          }
        }
      }
    }
    prevValues = { ...values };

    // Total
    document.getElementById('val-total').textContent = totalSeconds.toLocaleString('fr-FR');
  }

  tick();
  setInterval(tick, 1000);
</script>
</body>
</html>
    color: var(--text-dim);
    font-weight: 500;
  }

  .picker-field input {
    width: 100%;
    background: var(--surface2);
    border: 1px solid rgba(255,255,255,0.06);
    border-radius: 10px;
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    font-size: 14px;
    padding: 10px 12px;
    outline: none;
    transition: border-color 0.2s;
    -webkit-appearance: none;
    appearance: none;
  }

  .picker-field input:focus {
    border-color: var(--accent);
    box-shadow: 0 0 0 3px var(--accent-dim);
  }

  /* Color scheme for native date/time pickers */
  input[type="date"]::-webkit-calendar-picker-indicator,
  input[type="time"]::-webkit-calendar-picker-indicator {
    filter: invert(0.6) sepia(1) hue-rotate(220deg) saturate(3);
    cursor: pointer;
  }

  /* ── CTA BUTTON ── */
  .btn-start {
    width: 100%;
    padding: 15px;
    background: var(--accent);
    border: none;
    border-radius: 14px;
    color: #fff;
    font-family: 'Inter', sans-serif;
    font-size: 15px;
    font-weight: 600;
    letter-spacing: 0.02em;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s, background 0.15s;
    margin-top: 4px;
    box-shadow: 0 4px 20px rgba(124, 106, 247, 0.4);
  }

  .btn-start:active {
    transform: scale(0.97);
    box-shadow: 0 2px 10px rgba(124, 106, 247, 0.3);
  }

  .btn-start:hover {
    background: #8E7EF9;
  }

  /* ── COUNTER DISPLAY ── */
  .counter-section {
    margin-top: 4px;
  }

  .since-label {
    font-size: 11px;
    font-weight: 500;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-muted);
    text-align: center;
    margin-bottom: 6px;
  }

  .since-date {
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--accent);
    text-align: center;
    margin-bottom: 20px;
    opacity: 0.9;
  }

  /* Grid of time blocks */
  .time-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
    margin-bottom: 10px;
  }

  .time-block {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 16px 12px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.3s;
  }

  .time-block::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--accent-dim);
    opacity: 0;
    transition: opacity 0.3s;
  }

  .time-block.pulse::before {
    animation: flash 0.4s ease-out;
  }

  @keyframes flash {
    0% { opacity: 0.6; }
    100% { opacity: 0; }
  }

  .time-value {
    font-family: 'JetBrains Mono', monospace;
    font-size: 32px;
    font-weight: 700;
    color: var(--text);
    line-height: 1;
    letter-spacing: -0.02em;
    transition: color 0.2s;
  }

  .time-block.highlight .time-value {
    color: var(--accent);
    text-shadow: 0 0 20px var(--accent-glow);
  }

  .time-unit {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-dim);
  }

  /* Seconds full width */
  .time-block.wide {
    grid-column: 1 / -1;
    flex-direction: row;
    justify-content: center;
    gap: 16px;
    padding: 14px 20px;
  }

  .time-block.wide .time-value {
    font-size: 28px;
  }

  /* ── TOTAL LINE ── */
  .total-row {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 14px 18px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 10px;
  }

  .total-label {
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text-muted);
  }

  .total-value {
    font-family: 'JetBrains Mono', monospace;
    font-size: 13px;
    color: var(--text-muted);
  }

  /* ── PLACEHOLDER ── */
  .placeholder {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 40px 20px;
    gap: 12px;
    color: var(--text-dim);
  }

  .placeholder-icon {
    font-size: 40px;
    opacity: 0.4;
  }

  .placeholder-text {
    font-size: 14px;
    text-align: center;
    line-height: 1.6;
    max-width: 220px;
  }

  /* ── RESET BUTTON ── */
  .btn-reset {
    width: 100%;
    padding: 12px;
    background: transparent;
    border: 1px solid var(--border);
    border-radius: 12px;
    color: var(--text-muted);
    font-family: 'Inter', sans-serif;
    font-size: 13px;
    font-weight: 500;
    cursor: pointer;
    margin-top: 14px;
    transition: background 0.2s, color 0.2s;
  }

  .btn-reset:active {
    background: var(--surface2);
  }

  /* ── HIDDEN ── */
  .hidden { display: none !important; }
</style>
</head>
<body>
<div class="app">

  <div class="header">
    <span class="header-title">Temps Écoulé</span>
    <div class="status-dot inactive" id="statusDot"></div>
  </div>

  <div class="content">

    <!-- Picker -->
    <div class="picker-card">
      <div class="picker-label">Depuis quand ?</div>
      <div class="picker-row">
        <div class="picker-field">
          <label>Date</label>
          <input type="date" id="dateInput">
        </div>
        <div class="picker-field">
          <label>Heure</label>
          <input type="time" id="timeInput" value="00:00">
        </div>
      </div>
    </div>

    <button class="btn-start" id="btnStart" onclick="startCounter()">Calculer le temps écoulé</button>

    <!-- Placeholder (état initial) -->
    <div class="placeholder" id="placeholder">
      <div class="placeholder-icon">⏳</div>
      <div class="placeholder-text">Sélectionne une date et une heure, puis lance le calcul.</div>
    </div>

    <!-- Counter (masqué au départ) -->
    <div class="counter-section hidden" id="counterSection">

      <div class="since-label">Depuis le</div>
      <div class="since-date" id="sinceDate">—</div>

      <div class="time-grid">
        <div class="time-block" id="block-years">
          <div class="time-value" id="val-years">0</div>
          <div class="time-unit">Années</div>
        </div>
        <div class="time-block" id="block-months">
          <div class="time-value" id="val-months">0</div>
          <div class="time-unit">Mois</div>
        </div>
        <div class="time-block" id="block-weeks">
          <div class="time-value" id="val-weeks">0</div>
          <div class="time-unit">Semaines</div>
        </div>
        <div class="time-block" id="block-days">
          <div class="time-value" id="val-days">0</div>
          <div class="time-unit">Jours</div>
        </div>
        <div class="time-block" id="block-hours">
          <div class="time-value" id="val-hours">0</div>
          <div class="time-unit">Heures</div>
        </div>
        <div class="time-block" id="block-minutes">
          <div class="time-value" id="val-minutes">0</div>
          <div class="time-unit">Minutes</div>
        </div>
        <div class="time-block wide highlight" id="block-seconds">
          <div class="time-value" id="val-seconds">0</div>
          <div class="time-unit">Secondes</div>
        </div>
      </div>

      <!-- Total secondes -->
      <div class="total-row">
        <span class="total-label">Total secondes</span>
        <span class="total-value" id="val-total">0</span>
      </div>

      <button class="btn-reset" onclick="resetCounter()">↩ Choisir une autre date</button>
    </div>

  </div>
</div>

<script>
  let intervalId = null;
  let targetDate = null;
  let prevValues = {};

  // Initialise la date à aujourd'hui
  const today = new Date();
  document.getElementById('dateInput').value = today.toISOString().split('T')[0];

  function startCounter() {
    const dateVal = document.getElementById('dateInput').value;
    const timeVal = document.getElementById('timeInput').value || '00:00';

    if (!dateVal) {
      document.getElementById('dateInput').focus();
      return;
    }

    targetDate = new Date(`${dateVal}T${timeVal}:00`);

    if (isNaN(targetDate.getTime())) return;

    if (targetDate > new Date()) {
      alert('La date doit être dans le passé.');
      return;
    }

    // Affiche la date de référence
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit' };
    document.getElementById('sinceDate').textContent = targetDate.toLocaleDateString('fr-FR', options);

    // Switch UI
    document.getElementById('placeholder').classList.add('hidden');
    document.getElementById('counterSection').classList.remove('hidden');
    document.getElementById('statusDot').classList.remove('inactive');

    // Clear previous interval
    if (intervalId) clearInterval(intervalId);
    prevValues = {};

    tick();
    intervalId = setInterval(tick, 1000);
  }

  function tick() {
    const now = new Date();
    const diffMs = now - targetDate;

    if (diffMs < 0) return;

    const totalSeconds = Math.floor(diffMs / 1000);

    // ── Decompose ──
    let remaining = diffMs;

    const years = Math.floor(remaining / (365.25 * 24 * 3600 * 1000));
    remaining -= years * 365.25 * 24 * 3600 * 1000;

    const months = Math.floor(remaining / (30.4375 * 24 * 3600 * 1000));
    remaining -= months * 30.4375 * 24 * 3600 * 1000;

    const weeks = Math.floor(remaining / (7 * 24 * 3600 * 1000));
    remaining -= weeks * 7 * 24 * 3600 * 1000;

    const days = Math.floor(remaining / (24 * 3600 * 1000));
    remaining -= days * 24 * 3600 * 1000;

    const hours = Math.floor(remaining / (3600 * 1000));
    remaining -= hours * 3600 * 1000;

    const minutes = Math.floor(remaining / (60 * 1000));
    remaining -= minutes * 60 * 1000;

    const seconds = Math.floor(remaining / 1000);

    const values = { years, months, weeks, days, hours, minutes, seconds };

    // Update DOM + pulse on change
    for (const [key, val] of Object.entries(values)) {
      const el = document.getElementById(`val-${key}`);
      const block = document.getElementById(`block-${key}`);
      if (el) {
        const display = String(val).padStart(key === 'seconds' ? 2 : 1, '');
        if (prevValues[key] !== val) {
          el.textContent = display;
          if (block && prevValues[key] !== undefined) {
            block.classList.remove('pulse');
            void block.offsetWidth; // reflow
            block.classList.add('pulse');
          }
        }
      }
    }
    prevValues = { ...values };

    // Total
    document.getElementById('val-total').textContent = totalSeconds.toLocaleString('fr-FR');
  }

  function resetCounter() {
    if (intervalId) clearInterval(intervalId);
    intervalId = null;
    targetDate = null;
    prevValues = {};
    document.getElementById('counterSection').classList.add('hidden');
    document.getElementById('placeholder').classList.remove('hidden');
    document.getElementById('statusDot').classList.add('inactive');
  }
</script>
</body>
</html>
