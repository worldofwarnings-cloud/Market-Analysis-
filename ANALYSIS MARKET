<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Live Market Monitor</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --bg: #0a0e14;
    --panel: #10151d;
    --panel-2: #141b26;
    --border: #1f2733;
    --text: #e8edf3;
    --muted: #7c8a9e;
    --up: #34d1a4;
    --down: #ef5b5b;
    --resistance: #f2a93b;
    --support: #4fb0ff;
    --breakout: #c792ea;
    --demo: #f2a93b;
    --live: #34d1a4;
    --accent: #6ea8fe;
  }
  * { box-sizing: border-box; }
  html, body {
    margin: 0; padding: 0;
    background: var(--bg);
    color: var(--text);
    font-family: 'Inter', sans-serif;
    height: 100%;
    overflow-x: hidden;
  }
  .mono { font-family: 'IBM Plex Mono', monospace; }

  #app {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  /* ---------- Top bar ---------- */
  .topbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    padding: 14px 20px;
    border-bottom: 1px solid var(--border);
    background: linear-gradient(180deg, #0d1219, #0a0e14);
    position: relative;
  }
  .ticker {
    display: flex;
    align-items: baseline;
    gap: 14px;
    padding: 4px 8px;
    border-radius: 8px;
    transition: box-shadow 0.3s ease, background 0.3s ease;
  }
  .ticker.flash-up { box-shadow: 0 0 0 1px var(--up), 0 0 22px rgba(52,209,164,0.45); background: rgba(52,209,164,0.06); }
  .ticker.flash-down { box-shadow: 0 0 0 1px var(--down), 0 0 22px rgba(239,91,91,0.45); background: rgba(239,91,91,0.06); }
  .ticker .sym {
    font-size: 12px;
    letter-spacing: 0.06em;
    color: var(--muted);
    font-weight: 600;
  }
  .ticker .price {
    font-size: 28px;
    font-weight: 600;
    font-family: 'IBM Plex Mono', monospace;
  }
  .ticker .delta {
    font-size: 14px;
    font-weight: 500;
    font-family: 'IBM Plex Mono', monospace;
  }
  .delta.pos { color: var(--up); }
  .delta.neg { color: var(--down); }

  .status-cluster {
    display: flex;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
  }
  .status-pill {
    display: flex;
    align-items: center;
    gap: 7px;
    padding: 5px 11px;
    border-radius: 20px;
    background: var(--panel-2);
    border: 1px solid var(--border);
    font-size: 12px;
    color: var(--muted);
    white-space: nowrap;
  }
  .dot {
    width: 7px; height: 7px; border-radius: 50%;
    background: var(--demo);
  }
  .status-pill.live .dot { background: var(--live); box-shadow: 0 0 8px var(--live); animation: pulse 1.8s infinite; }
  .status-pill.live { color: var(--live); }
  .status-pill.td .dot { background: var(--accent); box-shadow: 0 0 8px var(--accent); animation: pulse 1.8s infinite; }
  .status-pill.td { color: var(--accent); }
  .status-pill.closed .dot { background: var(--muted); }
  .status-pill.closed { color: var(--muted); }
  .status-pill.notlive .dot { background: var(--resistance); }
  .status-pill.notlive { color: var(--resistance); }
  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.35; }
  }

  /* ---------- Signal pill ---------- */
  .signal-pill {
    display: flex;
    align-items: center;
    gap: 7px;
    padding: 5px 12px;
    border-radius: 20px;
    font-size: 12px;
    font-weight: 700;
    font-family: 'IBM Plex Mono', monospace;
    white-space: nowrap;
    border: 1px solid var(--border);
    background: var(--panel-2);
    color: var(--muted);
    transition: background 0.3s ease, border-color 0.3s ease, color 0.3s ease;
  }
  .signal-pill.buy { color: var(--up); border-color: var(--up); background: rgba(52,209,164,0.10); }
  .signal-pill.sell { color: var(--down); border-color: var(--down); background: rgba(239,91,91,0.10); }
  .signal-pill .sig-conf { opacity: 0.85; }

  /* ---------- Level strength badge ---------- */
  .level-row { position: relative; }
  .level-row .lmeta { display: flex; flex-direction: column; align-items: flex-end; gap: 3px; }
  .strength-badge {
    font-size: 10px;
    font-weight: 700;
    padding: 2px 7px;
    border-radius: 10px;
    font-family: 'IBM Plex Mono', monospace;
    white-space: nowrap;
  }
  .strength-badge.strong { background: rgba(52,209,164,0.16); color: var(--up); }
  .strength-badge.moderate { background: rgba(242,169,59,0.16); color: var(--resistance); }
  .strength-badge.weak { background: rgba(124,138,158,0.16); color: var(--muted); }

  .designer-credit {
    position: fixed;
    left: 10px;
    bottom: 8px;
    font-size: 10.5px;
    color: var(--muted);
    opacity: 0.55;
    z-index: 2;
    font-family: 'Inter', sans-serif;
    letter-spacing: 0.02em;
    pointer-events: none;
  }

  .tf-group, .sym-group {
    display: flex;
    gap: 4px;
    background: var(--panel-2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 3px;
    flex-wrap: wrap;
  }
  .tf-btn, .sym-btn {
    border: none;
    background: transparent;
    color: var(--muted);
    font-family: 'IBM Plex Mono', monospace;
    font-size: 12px;
    padding: 6px 10px;
    border-radius: 6px;
    cursor: pointer;
    white-space: nowrap;
  }
  .sym-btn { font-family: 'Inter', sans-serif; font-weight: 600; font-size: 12.5px; }
  .tf-btn.active, .sym-btn.active {
    background: #1c2531;
    color: var(--text);
  }
  .sym-btn.active { color: var(--accent); box-shadow: inset 0 0 0 1px rgba(110,168,254,0.4); }

  .refresh-btn, .gear-btn {
    border: 1px solid var(--border);
    background: var(--panel-2);
    color: var(--muted);
    border-radius: 8px;
    padding: 7px 10px;
    cursor: pointer;
    font-size: 12px;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .refresh-btn:hover, .gear-btn:hover { color: var(--text); border-color: #33404f; }
  .gear-btn { font-size: 15px; padding: 6px 9px; }

  .subbar {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    padding: 10px 20px;
    border-bottom: 1px solid var(--border);
    background: #0b0f16;
  }

  /* ---------- Settings panel ---------- */
  .settings-panel {
    position: absolute;
    top: 62px;
    right: 20px;
    width: 300px;
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 16px;
    box-shadow: 0 12px 32px rgba(0,0,0,0.5);
    z-index: 30;
    display: none;
    flex-direction: column;
    gap: 10px;
  }
  .settings-panel.open { display: flex; }
  .settings-panel h3 {
    margin: 0;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 0.07em;
    color: var(--muted);
  }
  .settings-panel p {
    margin: 0;
    font-size: 11.5px;
    color: var(--muted);
    line-height: 1.5;
  }
  .settings-panel a { color: var(--accent); }
  .settings-panel input {
    width: 100%;
    padding: 8px 10px;
    background: var(--panel-2);
    border: 1px solid var(--border);
    border-radius: 6px;
    color: var(--text);
    font-family: 'IBM Plex Mono', monospace;
    font-size: 12.5px;
  }
  .settings-actions { display: flex; gap: 8px; }
  .settings-actions button {
    flex: 1;
    padding: 7px 0;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: var(--panel-2);
    color: var(--text);
    cursor: pointer;
    font-size: 12px;
  }
  .settings-actions button.primary { background: #1d4b8f; border-color: #1d4b8f; }
  .settings-actions button:hover { border-color: #33404f; }
  .settings-status { font-size: 11.5px; color: var(--muted); min-height: 14px; }

  /* ---------- Main layout ---------- */
  main {
    flex: 1;
    display: flex;
    gap: 1px;
    background: var(--border);
  }
  .chart-wrap {
    flex: 1;
    position: relative;
    background: var(--bg);
    min-height: 420px;
    min-width: 0;
  }
  #chart { display: block; width: 100%; height: 100%; }

  .banner {
    position: absolute;
    top: 14px; left: 50%;
    transform: translateX(-50%) translateY(-14px);
    padding: 9px 18px;
    border-radius: 8px;
    font-size: 13px;
    font-weight: 600;
    letter-spacing: 0.01em;
    box-shadow: 0 8px 24px rgba(0,0,0,0.4);
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.35s ease, transform 0.35s ease;
    white-space: nowrap;
    z-index: 5;
  }
  .banner.show { opacity: 1; transform: translateX(-50%) translateY(0); }
  .banner.up { background: rgba(52,209,164,0.14); border: 1px solid var(--up); color: var(--up); }
  .banner.down { background: rgba(239,91,91,0.14); border: 1px solid var(--down); color: var(--down); }

  .loading-overlay {
    position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    color: var(--muted);
    font-size: 13px;
    background: rgba(10,14,20,0.6);
    z-index: 4;
  }
  .loading-overlay.hidden { display: none; }

  .demo-warning { display: none !important; }

  aside.panel {
    width: 300px;
    flex-shrink: 0;
    background: var(--panel);
    padding: 18px;
    display: flex;
    flex-direction: column;
    gap: 22px;
    overflow-y: auto;
  }
  @media (max-width: 820px) {
    main { flex-direction: column; }
    aside.panel { width: 100%; flex-direction: row; flex-wrap: wrap; }
    .levels-block { flex: 1; min-width: 220px; }
    .settings-panel { right: 10px; left: 10px; width: auto; }
  }

  .panel h3 {
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.08em;
    color: var(--muted);
    margin: 0 0 10px 0;
    font-weight: 600;
  }
  .level-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 10px;
    border-radius: 6px;
    margin-bottom: 6px;
    background: var(--panel-2);
    border-left: 3px solid transparent;
    transition: box-shadow 0.3s ease;
  }
  .level-row.resistance { border-left-color: var(--resistance); }
  .level-row.support { border-left-color: var(--support); }
  .level-row.hit { box-shadow: 0 0 0 1px currentColor inset; }
  .level-row .lp { font-family: 'IBM Plex Mono', monospace; font-size: 14px; font-weight: 600; }
  .level-row .lt { font-size: 11px; color: var(--muted); }
  .empty-hint { color: var(--muted); font-size: 12px; padding: 6px 2px; }

  .forecast-window {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 11px;
    color: var(--muted);
    margin-bottom: 8px;
  }
  .forecast-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 7px 10px;
    border-radius: 6px;
    margin-bottom: 6px;
    background: var(--panel-2);
  }
  .forecast-row .fl { font-size: 11.5px; color: var(--muted); flex: 1; }
  .forecast-row .fv { font-family: 'IBM Plex Mono', monospace; font-weight: 600; font-size: 13.5px; margin-right: 8px; }
  .forecast-row .fp { font-family: 'IBM Plex Mono', monospace; font-size: 11px; }
  .forecast-row .fc { font-size: 10px; color: var(--muted); margin-left: 6px; }
  .forecast-row.high { border-left: 3px solid var(--up); }
  .forecast-row.high .fp { color: var(--up); }
  .forecast-row.low { border-left: 3px solid var(--down); }
  .forecast-row.low .fp { color: var(--down); }
  .forecast-row.neutral { border-left: 3px solid var(--muted); }
  .forecast-row.neutral .fp { color: var(--muted); }

  #alertLog {
    list-style: none;
    margin: 0; padding: 0;
    display: flex;
    flex-direction: column;
    gap: 8px;
    max-height: 260px;
    overflow-y: auto;
  }
  #alertLog li {
    font-size: 12px;
    padding: 8px 10px;
    border-radius: 6px;
    background: var(--panel-2);
    line-height: 1.5;
  }
  #alertLog li .t { color: var(--muted); font-family: 'IBM Plex Mono', monospace; font-size: 11px; margin-right: 6px; }
  #alertLog li.up { border-left: 3px solid var(--up); }
  #alertLog li.down { border-left: 3px solid var(--down); }

  footer {
    padding: 10px 20px;
    font-size: 11px;
    color: var(--muted);
    border-top: 1px solid var(--border);
    background: var(--bg);
    text-align: center;
  }
  footer a { color: var(--muted); }

  ::-webkit-scrollbar { width: 8px; height: 8px; }
  ::-webkit-scrollbar-thumb { background: #26303e; border-radius: 4px; }
</style>
</head>
<body>
<div id="app">

  <div class="topbar">
    <div class="ticker" id="tickerBox">
      <span class="sym" id="symLabel">ICE U.S. DOLLAR INDEX &middot; DXY</span>
      <span class="price mono" id="priceEl">--.--</span>
      <span class="delta mono" id="deltaEl">--</span>
    </div>
    <div class="status-cluster">
      <div class="tf-group" id="tfGroup">
        <button class="tf-btn" data-tf="1">1m</button>
        <button class="tf-btn" data-tf="2">2m</button>
        <button class="tf-btn" data-tf="3">3m</button>
        <button class="tf-btn" data-tf="4">4m</button>
        <button class="tf-btn active" data-tf="5">5m</button>
        <button class="tf-btn" data-tf="15">15m</button>
      </div>
      <button class="refresh-btn" id="refreshBtn">&#8635; <span id="countdownEl">20s</span></button>
      <div class="status-pill" id="statusPill"><span class="dot"></span><span id="statusText">Connecting…</span></div>
      <div class="signal-pill" id="signalPill" style="display:none;"><span id="signalLabel">--</span><span class="sig-conf" id="signalConf"></span></div>
      <button class="gear-btn" id="gearBtn" title="API key settings">&#9881;</button>
    </div>

    <div class="settings-panel" id="settingsPanel">
      <h3>Live Data API Key (optional)</h3>
      <p>
        Without a key, this app pulls data through free public proxies (Yahoo Finance), which can be slow or blocked.
        Add a free <a href="https://twelvedata.com/pricing" target="_blank" rel="noopener">Twelve Data</a> API key for
        faster, more reliable live prices. The key is kept only in this browser tab's memory — it is never saved to
        disk and will be cleared on reload.
      </p>
      <input type="password" id="apiKeyInput" placeholder="Paste Twelve Data API key">
      <div class="settings-actions">
        <button class="primary" id="saveKeyBtn">Save &amp; use</button>
        <button id="clearKeyBtn">Clear</button>
      </div>
      <div class="settings-status" id="keyStatus"></div>
    </div>
  </div>

  <div class="subbar">
    <div class="sym-group" id="symGroup">
      <button class="sym-btn active" data-sym="dxy">DXY</button>
      <button class="sym-btn" data-sym="xau">GOLD / USD</button>
      <button class="sym-btn" data-sym="eur">EUR / USD</button>
      <button class="sym-btn" data-sym="btc">BTC / USD</button>
    </div>
  </div>

  <main>
    <div class="chart-wrap">
      <canvas id="chart"></canvas>
      <div class="banner" id="banner"></div>
      <div class="loading-overlay" id="loadingOverlay">Fetching market data…</div>
      <div class="demo-warning" id="demoWarning">⚠ Live feed unavailable right now — showing simulated demo prices, NOT the real market price.</div>
    </div>
    <aside class="panel">
      <div class="levels-block">
        <h3>Next 24h Outlook</h3>
        <div class="forecast-window" id="forecastWindow">--</div>
        <div class="forecast-row high"><span class="fl">Expected High</span><span class="fv" id="forecastHigh">--</span><span class="fp" id="forecastHighPct">--</span><span class="fc" id="forecastHighConf"></span></div>
        <div class="forecast-row neutral"><span class="fl">Neutral (current)</span><span class="fv" id="forecastNeutral">--</span><span class="fp">0.00%</span><span class="fc" id="forecastNeutralConf"></span></div>
        <div class="forecast-row low"><span class="fl">Expected Low</span><span class="fv" id="forecastLow">--</span><span class="fp" id="forecastLowPct">--</span><span class="fc" id="forecastLowConf"></span></div>
        <div class="empty-hint" id="forecastNext">Recalculates at 3:00 AM &amp; 11:00 PM daily.</div>
        <div class="empty-hint">Statistical estimate from recent daily volatility — not a guaranteed prediction.</div>
      </div>
      <div class="levels-block">
        <h3>Resistance</h3>
        <div id="resList"><div class="empty-hint">Calculating…</div></div>
      </div>
      <div class="levels-block">
        <h3>Support</h3>
        <div id="supList"><div class="empty-hint">Calculating…</div></div>
      </div>
      <div class="levels-block">
        <h3>Breakout Log</h3>
        <ul id="alertLog"><li class="empty-hint" style="border:none;background:none;">No breakouts yet.</li></ul>
      </div>
    </aside>
  </main>

  <footer>
    BTC/USD via Binance (direct, live) &middot; DXY / GOLD / EUR-USD via Yahoo Finance (best-effort through public CORS proxies)
    or Twelve Data (if you add a key) &middot; shows "Market Closed" outside FX trading hours and "Not Live" if a feed can't be
    reached while the market is open &middot; the BUY/SELL signal and level-strength percentages are a heuristic read of trend,
    momentum, breakouts and candle patterns — not a guarantee of future price movement &middot; Educational tool only, not financial advice.
  </footer>

  <div class="designer-credit">Design by Haq Nawaz</div>
</div>

<script>
(function(){
  // ---------------- Symbol config ----------------
  const SYMBOLS = {
    dxy: { label: 'ICE U.S. DOLLAR INDEX · DXY', yahoo: ['DX-Y.NYB', 'DX=F'], td: 'DXY', decimals: 2, demoBase: 105.00, demoVol: 0.05, fallbackAtrPct: 0.5 },
    xau: { label: 'GOLD · XAU/USD', yahoo: ['XAUUSD=X', 'GC=F'], td: 'XAU/USD', decimals: 2, demoBase: 2385.00, demoVol: 2.2, fallbackAtrPct: 1.3 },
    eur: { label: 'EURO · EUR/USD', yahoo: ['EURUSD=X'], td: 'EUR/USD', decimals: 4, demoBase: 1.0850, demoVol: 0.0011, fallbackAtrPct: 0.6 },
    btc: { label: 'BITCOIN · BTC/USD', yahoo: ['BTC-USD'], td: 'BTC/USD', binance: 'BTCUSDT', decimals: 1, demoBase: 77000, demoVol: 220, fallbackAtrPct: 3.8 },
  };

  const TF_REFRESH = { 1: 5, 2: 5, 3: 5, 4: 5, 5: 5, 15: 5 };

  // ---------------- Market hours ----------------
  // Crypto (BTC) trades 24/7. DXY / GOLD / EUR follow the global FX/futures
  // session, which is closed roughly Fri 22:00 UTC -> Sun 22:00 UTC.
  function isMarketClosed(symKey) {
    if (symKey === 'btc') return false;
    const now = new Date();
    const day = now.getUTCDay(); // 0 = Sun ... 6 = Sat
    const hour = now.getUTCHours();
    if (day === 6) return true;
    if (day === 0 && hour < 22) return true;
    if (day === 5 && hour >= 22) return true;
    return false;
  }

  const state = {
    symKey: 'dxy',
    tf: 5,               // minutes
    apiKey: null,         // kept in memory only, never persisted
    candles1m: [],        // base 1-minute candles for the current symbol
    candles: [],          // aggregated candles for the active timeframe
    isLive: false,
    marketClosed: false,
    source: 'demo',       // 'yahoo' | 'twelvedata' | 'binance' | 'demo'
    activeYahooSymbol: null,
    resistance: [],
    support: [],
    lastAlertedTime: null,
    breakout: null,       // { type, level, expiresAt }
    countdown: 20,
    forecast: null,       // { high, low, neutral, pct, anchorTime, symKey }
    tickCounter: 0,
  };

  const priceEl = document.getElementById('priceEl');
  const deltaEl = document.getElementById('deltaEl');
  const symLabel = document.getElementById('symLabel');
  const tickerBox = document.getElementById('tickerBox');
  const statusPill = document.getElementById('statusPill');
  const statusText = document.getElementById('statusText');
  const countdownEl = document.getElementById('countdownEl');
  const loadingOverlay = document.getElementById('loadingOverlay');
  const demoWarning = document.getElementById('demoWarning');
  const resList = document.getElementById('resList');
  const supList = document.getElementById('supList');
  const alertLog = document.getElementById('alertLog');
  const banner = document.getElementById('banner');
  const canvas = document.getElementById('chart');
  const ctx = canvas.getContext('2d');
  const gearBtn = document.getElementById('gearBtn');
  const settingsPanel = document.getElementById('settingsPanel');
  const apiKeyInput = document.getElementById('apiKeyInput');
  const keyStatus = document.getElementById('keyStatus');
  const forecastWindow = document.getElementById('forecastWindow');
  const forecastHigh = document.getElementById('forecastHigh');
  const forecastHighPct = document.getElementById('forecastHighPct');
  const forecastLow = document.getElementById('forecastLow');
  const forecastLowPct = document.getElementById('forecastLowPct');
  const forecastNeutral = document.getElementById('forecastNeutral');
  const forecastNext = document.getElementById('forecastNext');
  const forecastHighConf = document.getElementById('forecastHighConf');
  const forecastNeutralConf = document.getElementById('forecastNeutralConf');
  const forecastLowConf = document.getElementById('forecastLowConf');
  const signalPill = document.getElementById('signalPill');
  const signalLabel = document.getElementById('signalLabel');
  const signalConf = document.getElementById('signalConf');

  function cfg() { return SYMBOLS[state.symKey]; }
  function fmt(price) { return price.toFixed(cfg().decimals); }

  // ---------------- Data fetching ----------------
  function withTimeout(promise, ms) {
    return Promise.race([
      promise,
      new Promise((_, rej) => setTimeout(() => rej(new Error('timeout')), ms))
    ]);
  }

  async function fetchViaProxies(targetUrl) {
    const proxies = [
      'https://api.allorigins.win/raw?url=' + encodeURIComponent(targetUrl),
      'https://corsproxy.io/?url=' + encodeURIComponent(targetUrl),
      'https://api.codetabs.com/v1/proxy?quest=' + encodeURIComponent(targetUrl),
      'https://thingproxy.freeboard.io/fetch/' + targetUrl,
    ];
    for (const p of proxies) {
      try {
        const res = await withTimeout(fetch(p, { cache: 'no-store' }), 8000);
        if (!res.ok) continue;
        const json = await res.json();
        if (json) return json;
      } catch (e) { /* try next */ }
    }
    return null;
  }

  async function fetchYahoo1m(symbol) {
    const target = `https://query1.finance.yahoo.com/v8/finance/chart/${symbol}?interval=1m&range=5d`;
    const json = await fetchViaProxies(target);
    const result = json && json.chart && json.chart.result && json.chart.result[0];
    if (!result || !result.timestamp) return null;
    const ts = result.timestamp;
    const q = result.indicators.quote[0];
    const candles = [];
    for (let i = 0; i < ts.length; i++) {
      const o = q.open[i], h = q.high[i], l = q.low[i], c = q.close[i];
      if (o == null || h == null || l == null || c == null) continue;
      candles.push({ time: ts[i] * 1000, open: o, high: h, low: l, close: c });
    }
    return candles.length ? candles : null;
  }

  async function fetchBinance1m(binanceSymbol) {
    const url = `https://api.binance.com/api/v3/klines?symbol=${binanceSymbol}&interval=1m&limit=500`;
    const res = await withTimeout(fetch(url, { cache: 'no-store' }), 8000);
    if (!res.ok) return null;
    const rows = await res.json();
    if (!Array.isArray(rows) || !rows.length) return null;
    const candles = rows.map(r => ({
      time: r[0],
      open: parseFloat(r[1]), high: parseFloat(r[2]),
      low: parseFloat(r[3]), close: parseFloat(r[4]),
    }));
    return candles.length ? candles : null;
  }

  async function fetchTwelveData1m(symbol, apiKey) {
    const url = `https://api.twelvedata.com/time_series?symbol=${encodeURIComponent(symbol)}&interval=1min&outputsize=500&apikey=${encodeURIComponent(apiKey)}`;
    const res = await withTimeout(fetch(url, { cache: 'no-store' }), 8000);
    const json = await res.json();
    if (!json || json.status === 'error' || !Array.isArray(json.values)) return null;
    const candles = json.values
      .map(v => ({
        time: new Date(v.datetime.replace(' ', 'T')).getTime(),
        open: parseFloat(v.open), high: parseFloat(v.high),
        low: parseFloat(v.low), close: parseFloat(v.close),
      }))
      .filter(c => !isNaN(c.close))
      .sort((a, b) => a.time - b.time);
    return candles.length ? candles : null;
  }

  // ---- Lightweight single-price fetchers used for the fast "live tick" between full resyncs ----
  async function fetchBinanceQuickPrice(binanceSymbol) {
    const res = await withTimeout(fetch(`https://api.binance.com/api/v3/ticker/price?symbol=${binanceSymbol}`, { cache: 'no-store' }), 4000);
    if (!res.ok) return null;
    const json = await res.json();
    const p = parseFloat(json && json.price);
    return isNaN(p) ? null : p;
  }

  async function fetchTwelveDataQuickPrice(symbol, apiKey) {
    const res = await withTimeout(fetch(`https://api.twelvedata.com/price?symbol=${encodeURIComponent(symbol)}&apikey=${encodeURIComponent(apiKey)}`, { cache: 'no-store' }), 4000);
    const json = await res.json();
    const p = parseFloat(json && json.price);
    return isNaN(p) ? null : p;
  }

  async function fetchYahooQuickPrice(symbol) {
    const target = `https://query1.finance.yahoo.com/v7/finance/quote?symbols=${symbol}`;
    try {
      const res = await withTimeout(fetch('https://api.allorigins.win/raw?url=' + encodeURIComponent(target), { cache: 'no-store' }), 4500);
      if (!res.ok) return null;
      const json = await res.json();
      const r = json && json.quoteResponse && json.quoteResponse.result && json.quoteResponse.result[0];
      const p = r && parseFloat(r.regularMarketPrice);
      return isNaN(p) ? null : p;
    } catch (e) { return null; }
  }

  // ---- Daily candles, used only to size the 24h outlook (recomputed once/day) ----
  async function fetchBinanceDaily(binanceSymbol) {
    const res = await withTimeout(fetch(`https://api.binance.com/api/v3/klines?symbol=${binanceSymbol}&interval=1d&limit=30`, { cache: 'no-store' }), 8000);
    if (!res.ok) return null;
    const rows = await res.json();
    if (!Array.isArray(rows) || !rows.length) return null;
    return rows.map(r => ({ time: r[0], open: +r[1], high: +r[2], low: +r[3], close: +r[4] }));
  }

  async function fetchTwelveDataDaily(symbol, apiKey) {
    const url = `https://api.twelvedata.com/time_series?symbol=${encodeURIComponent(symbol)}&interval=1day&outputsize=30&apikey=${encodeURIComponent(apiKey)}`;
    const res = await withTimeout(fetch(url, { cache: 'no-store' }), 8000);
    const json = await res.json();
    if (!json || json.status === 'error' || !Array.isArray(json.values)) return null;
    return json.values.map(v => ({
      time: new Date(v.datetime.replace(' ', 'T')).getTime(),
      open: parseFloat(v.open), high: parseFloat(v.high), low: parseFloat(v.low), close: parseFloat(v.close),
    })).filter(c => !isNaN(c.close));
  }

  async function fetchYahooDaily(symbol) {
    const target = `https://query1.finance.yahoo.com/v8/finance/chart/${symbol}?interval=1d&range=3mo`;
    const json = await fetchViaProxies(target);
    const result = json && json.chart && json.chart.result && json.chart.result[0];
    if (!result || !result.timestamp) return null;
    const ts = result.timestamp;
    const q = result.indicators.quote[0];
    const candles = [];
    for (let i = 0; i < ts.length; i++) {
      const o = q.open[i], h = q.high[i], l = q.low[i], c = q.close[i];
      if (o == null || h == null || l == null || c == null) continue;
      candles.push({ time: ts[i] * 1000, open: o, high: h, low: l, close: c });
    }
    return candles.length ? candles : null;
  }

  function computeAtrPctFromDaily(dailyCandles) {
    if (!dailyCandles || dailyCandles.length < 5) return null;
    const recent = dailyCandles.slice(-20);
    const pct = recent.reduce((s, c) => s + (c.high - c.low) / c.close, 0) / recent.length * 100;
    return isFinite(pct) && pct > 0 ? pct : null;
  }

  async function fetchDailyAtrPct() {
    const c = cfg();
    if (state.apiKey) {
      try { const p = computeAtrPctFromDaily(await fetchTwelveDataDaily(c.td, state.apiKey)); if (p) return p; } catch (e) {}
    }
    if (c.binance) {
      try { const p = computeAtrPctFromDaily(await fetchBinanceDaily(c.binance)); if (p) return p; } catch (e) {}
    }
    for (const sym of c.yahoo) {
      try { const p = computeAtrPctFromDaily(await fetchYahooDaily(sym)); if (p) return p; } catch (e) {}
    }
    return null;
  }

  async function fetchLiveCandles1m() {
    const c = cfg();
    if (state.apiKey) {
      try {
        const candles = await fetchTwelveData1m(c.td, state.apiKey);
        if (candles && candles.length > 20) return { candles, source: 'twelvedata' };
      } catch (e) { /* fall through */ }
    }
    if (c.binance) {
      try {
        const candles = await fetchBinance1m(c.binance);
        if (candles && candles.length > 20) return { candles, source: 'binance' };
      } catch (e) { /* fall through */ }
    }
    for (const sym of c.yahoo) {
      try {
        const candles = await fetchYahoo1m(sym);
        if (candles && candles.length > 20) { state.activeYahooSymbol = sym; return { candles, source: 'yahoo' }; }
      } catch (e) { /* try next */ }
    }
    return null;
  }

  // ---------------- Demo data (1-minute base) ----------------
  function generateDemo1m(existing) {
    const c = cfg();
    const stepMs = 60000;
    const count = 400;
    let candles = existing && existing.length ? existing.slice() : [];
    if (!candles.length) {
      let price = c.demoBase;
      let t = Date.now() - stepMs * count;
      for (let i = 0; i < count; i++) {
        const vol = c.demoVol * (0.6 + Math.random() * 0.8);
        const open = price;
        const close = open + (Math.random() - 0.5) * vol;
        const high = Math.max(open, close) + Math.random() * vol * 0.5;
        const low = Math.min(open, close) - Math.random() * vol * 0.5;
        candles.push({ time: t, open, high, low, close });
        price = close;
        t += stepMs;
      }
    } else {
      const last = candles[candles.length - 1];
      const vol = c.demoVol * (0.6 + Math.random() * 0.8);
      const open = last.close;
      const close = open + (Math.random() - 0.5) * vol;
      const high = Math.max(open, close) + Math.random() * vol * 0.5;
      const low = Math.min(open, close) - Math.random() * vol * 0.5;
      candles.push({ time: last.time + stepMs, open, high, low, close });
      if (candles.length > count) candles.shift();
    }
    return candles;
  }

  // ---------------- Aggregation ----------------
  function aggregate(candles1m, tfMinutes) {
    if (tfMinutes <= 1 || !candles1m.length) return candles1m.slice();
    const bucketMs = tfMinutes * 60000;
    const out = [];
    let bucketKey = null, cur = null;
    for (const c of candles1m) {
      const key = Math.floor(c.time / bucketMs);
      if (key !== bucketKey) {
        if (cur) out.push(cur);
        bucketKey = key;
        cur = { time: key * bucketMs, open: c.open, high: c.high, low: c.low, close: c.close };
      } else {
        cur.high = Math.max(cur.high, c.high);
        cur.low = Math.min(cur.low, c.low);
        cur.close = c.close;
      }
    }
    if (cur) out.push(cur);
    return out;
  }

  // ---------------- Support / Resistance ----------------
  function findPivots(candles, lookback) {
    const highs = [], lows = [];
    for (let i = lookback; i < candles.length - lookback; i++) {
      let isHigh = true, isLow = true;
      for (let j = i - lookback; j <= i + lookback; j++) {
        if (j === i) continue;
        if (candles[j].high > candles[i].high) isHigh = false;
        if (candles[j].low < candles[i].low) isLow = false;
      }
      if (isHigh) highs.push({ idx: i, price: candles[i].high });
      if (isLow) lows.push({ idx: i, price: candles[i].low });
    }
    return { highs, lows };
  }

  function clusterLevels(pivots, tolerancePct, totalCandles) {
    if (!pivots.length) return [];
    const sorted = pivots.slice().sort((a, b) => a.price - b.price);
    const clusters = [];
    let current = [sorted[0]];
    for (let i = 1; i < sorted.length; i++) {
      const prevAvg = current.reduce((s, p) => s + p.price, 0) / current.length;
      if (Math.abs(sorted[i].price - prevAvg) / prevAvg <= tolerancePct) {
        current.push(sorted[i]);
      } else {
        clusters.push(current);
        current = [sorted[i]];
      }
    }
    clusters.push(current);
    return clusters.map(c => {
      const touches = c.length;
      const lastIdx = Math.max(...c.map(p => p.idx));
      return {
        price: c.reduce((s, p) => s + p.price, 0) / c.length,
        touches,
        lastIdx,
        confidence: levelConfidence(touches, lastIdx, totalCandles),
      };
    }).sort((a, b) => {
      if (b.touches !== a.touches) return b.touches - a.touches;
      return b.lastIdx - a.lastIdx;
    });
  }

  function levelConfidence(touches, lastIdx, totalCandles) {
    const touchScore = Math.min(touches / 8, 1) * 55;
    const recency = totalCandles > 0 ? Math.min(lastIdx / totalCandles, 1) : 0;
    const recencyScore = recency * 25;
    const score = 20 + touchScore + recencyScore;
    return Math.max(35, Math.min(97, Math.round(score)));
  }

  function levelStrengthLabel(confidence) {
    if (confidence >= 70) return 'strong';
    if (confidence >= 50) return 'moderate';
    return 'weak';
  }

  function computeLevels(candles) {
    if (candles.length < 15) return { resistance: [], support: [] };
    const lookback = candles.length > 100 ? 4 : 3;
    const pivots = findPivots(candles, lookback);
    const tolerancePct = 0.0012;
    const resClusters = clusterLevels(pivots.highs, tolerancePct, candles.length).slice(0, 4);
    const supClusters = clusterLevels(pivots.lows, tolerancePct, candles.length).slice(0, 4);
    return { resistance: resClusters, support: supClusters };
  }

  // ---------------- Breakout detection ----------------
  function checkBreakout(candles, resistance, support) {
    if (candles.length < 3) return null;
    const last = candles[candles.length - 1];
    const prev = candles[candles.length - 2];
    const buffer = 0.0006;

    for (const r of resistance) {
      const level = r.price;
      if (prev.close <= level * (1 + buffer) && last.close > level * (1 + buffer)) {
        return { type: 'up', level, time: last.time };
      }
    }
    for (const s of support) {
      const level = s.price;
      if (prev.close >= level * (1 - buffer) && last.close < level * (1 - buffer)) {
        return { type: 'down', level, time: last.time };
      }
    }
    return null;
  }

  function showBanner(type, level) {
    banner.className = 'banner ' + type;
    banner.textContent = type === 'up'
      ? `Live breakout above resistance ${fmt(level)}`
      : `Live breakdown below support ${fmt(level)}`;
    requestAnimationFrame(() => banner.classList.add('show'));
    setTimeout(() => banner.classList.remove('show'), 6000);
  }

  function flashTicker(type) {
    tickerBox.classList.remove('flash-up', 'flash-down');
    void tickerBox.offsetWidth;
    tickerBox.classList.add(type === 'up' ? 'flash-up' : 'flash-down');
    setTimeout(() => tickerBox.classList.remove('flash-up', 'flash-down'), 8000);
  }

  function logAlert(type, level, time) {
    const empty = alertLog.querySelector('.empty-hint');
    if (empty) empty.remove();
    const li = document.createElement('li');
    li.className = type;
    const t = new Date(time);
    const timeStr = t.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' });
    li.innerHTML = `<span class="t">${timeStr}</span>` +
      (type === 'up'
        ? `Broke <b>above</b> resistance ${fmt(level)}`
        : `Broke <b>below</b> support ${fmt(level)}`);
    alertLog.prepend(li);
    while (alertLog.children.length > 12) alertLog.removeChild(alertLog.lastChild);
  }

  // ---------------- Technical signal engine ----------------
  const SIGNAL_SHOW_THRESHOLD = 65;

  function emaSeries(values, period) {
    if (!values.length) return [];
    const k = 2 / (period + 1);
    let prev = values[0];
    const out = [prev];
    for (let i = 1; i < values.length; i++) {
      prev = values[i] * k + prev * (1 - k);
      out.push(prev);
    }
    return out;
  }

  function computeRSI(candles, period) {
    if (candles.length < period + 1) return null;
    let gains = 0, losses = 0;
    for (let i = candles.length - period; i < candles.length; i++) {
      const diff = candles[i].close - candles[i - 1].close;
      if (diff >= 0) gains += diff; else losses -= diff;
    }
    const avgGain = gains / period, avgLoss = losses / period;
    if (avgLoss === 0) return 100;
    const rs = avgGain / avgLoss;
    return 100 - 100 / (1 + rs);
  }

  function candlePattern(candles) {
    if (candles.length < 2) return null;
    const last = candles[candles.length - 1];
    const prev = candles[candles.length - 2];
    const bodyLast = Math.abs(last.close - last.open);
    const bodyPrev = Math.abs(prev.close - prev.open);
    const range = (last.high - last.low) || 1e-9;
    if (last.close > last.open && prev.close < prev.open &&
        last.close >= prev.open && last.open <= prev.close && bodyLast > bodyPrev * 0.9) return 'bullish_engulfing';
    if (last.close < last.open && prev.close > prev.open &&
        last.open >= prev.close && last.close <= prev.open && bodyLast > bodyPrev * 0.9) return 'bearish_engulfing';
    if (last.close > last.open && bodyLast / range > 0.6) return 'strong_bull';
    if (last.close < last.open && bodyLast / range > 0.6) return 'strong_bear';
    return null;
  }

  function computeSignal() {
    const candles = state.candles;
    if (candles.length < 25 || state.marketClosed) return null;
    const closes = candles.map(c => c.close).slice(-60);
    const ema9 = emaSeries(closes, 9);
    const ema21 = emaSeries(closes, 21);
    const lastEma9 = ema9[ema9.length - 1];
    const lastEma21 = ema21[ema21.length - 1];
    const rsi = computeRSI(candles, 14);
    const pattern = candlePattern(candles);
    const activeBreak = state.breakout && state.breakout.expiresAt > Date.now() ? state.breakout : null;

    let bull = 0, bear = 0;
    if (lastEma9 > lastEma21) bull += 30; else bear += 30;

    if (rsi != null) {
      if (rsi > 55) bull += 25;
      else if (rsi < 45) bear += 25;
      else { bull += 8; bear += 8; }
    }

    if (activeBreak) {
      if (activeBreak.type === 'up') bull += 25; else bear += 25;
    }

    if (pattern === 'bullish_engulfing') bull += 20;
    else if (pattern === 'strong_bull') bull += 12;
    else if (pattern === 'bearish_engulfing') bear += 20;
    else if (pattern === 'strong_bear') bear += 12;

    const side = bull >= bear ? 'buy' : 'sell';
    const rawScore = Math.max(bull, bear);
    const confidence = Math.min(96, Math.round(rawScore));
    return { side, confidence };
  }

  function renderSignal() {
    const sig = computeSignal();
    if (!sig || sig.confidence < SIGNAL_SHOW_THRESHOLD) {
      signalPill.style.display = 'none';
      signalPill.classList.remove('buy', 'sell');
      return;
    }
    signalPill.style.display = 'flex';
    signalPill.classList.toggle('buy', sig.side === 'buy');
    signalPill.classList.toggle('sell', sig.side === 'sell');
    signalLabel.textContent = sig.side === 'buy' ? '▲ BUY' : '▼ SELL';
    signalConf.textContent = sig.confidence + '%';
  }

  function handleBreakoutIfAny() {
    const breakout = checkBreakout(state.candles, state.resistance, state.support);
    if (breakout && breakout.time !== state.lastAlertedTime) {
      state.lastAlertedTime = breakout.time;
      state.breakout = { type: breakout.type, level: breakout.level, expiresAt: Date.now() + 8000 };
      showBanner(breakout.type, breakout.level);
      flashTicker(breakout.type);
      logAlert(breakout.type, breakout.level, breakout.time);
      startAnimLoop();
    }
  }

  // ---------------- Fast live tick ----------------
  function mergePriceIntoCandles1m(price) {
    const bucketMs = 60000;
    const bucketStart = Math.floor(Date.now() / bucketMs) * bucketMs;
    let candles = state.candles1m;
    if (!candles.length) {
      candles.push({ time: bucketStart, open: price, high: price, low: price, close: price });
      return;
    }
    const last = candles[candles.length - 1];
    if (last.time === bucketStart) {
      last.high = Math.max(last.high, price);
      last.low = Math.min(last.low, price);
      last.close = price;
    } else if (bucketStart > last.time) {
      candles.push({ time: bucketStart, open: last.close, high: Math.max(last.close, price), low: Math.min(last.close, price), close: price });
      if (candles.length > 600) candles.shift();
    }
  }

  function afterLiveUpdate() {
    state.candles = aggregate(state.candles1m, state.tf);
    handleBreakoutIfAny();
    updateTicker();
    renderLevels();
    renderSignal();
    drawChart();
  }

  function applyLivePrice(price) {
    if (price == null || isNaN(price)) return;
    mergePriceIntoCandles1m(price);
    afterLiveUpdate();
  }

  function demoTick() {
    const c = cfg();
    if (!state.candles1m.length) { state.candles1m = generateDemo1m([]); }
    const last = state.candles1m[state.candles1m.length - 1];
    const microVol = c.demoVol * 0.18;
    const price = last.close + (Math.random() - 0.5) * microVol;
    applyLivePrice(price);
  }

  async function tickLive() {
    try {
      const c = cfg();
      if (state.source === 'binance' && c.binance) {
        const p = await fetchBinanceQuickPrice(c.binance);
        if (p != null) applyLivePrice(p);
      } else if (state.source === 'twelvedata' && state.apiKey && state.tickCounter % 3 === 0) {
        const p = await fetchTwelveDataQuickPrice(c.td, state.apiKey);
        if (p != null) applyLivePrice(p);
      } else if (state.source === 'yahoo' && state.activeYahooSymbol && state.tickCounter % 3 === 0) {
        const p = await fetchYahooQuickPrice(state.activeYahooSymbol);
        if (p != null) applyLivePrice(p);
      } else if (state.source === 'demo') {
        demoTick();
      }
    } catch (e) { /* ignore - next tick will retry */ }
    state.tickCounter++;
  }

  // ---------------- Rendering ----------------
  function resizeCanvas() {
    const wrap = canvas.parentElement;
    const dpr = window.devicePixelRatio || 1;
    canvas.width = wrap.clientWidth * dpr;
    canvas.height = wrap.clientHeight * dpr;
    canvas.style.width = wrap.clientWidth + 'px';
    canvas.style.height = wrap.clientHeight + 'px';
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  }

  function drawChart() {
    const wrap = canvas.parentElement;
    const W = wrap.clientWidth, H = wrap.clientHeight;
    ctx.clearRect(0, 0, W, H);

    const candles = state.candles;
    if (!candles.length) return;

    const padL = 14, padR = 74, padT = 20, padB = 28;
    const chartW = W - padL - padR;
    const chartH = H - padT - padB;

    const visibleCount = Math.min(candles.length, 140);
    const visible = candles.slice(candles.length - visibleCount);

    let minP = Infinity, maxP = -Infinity;
    visible.forEach(c => { minP = Math.min(minP, c.low); maxP = Math.max(maxP, c.high); });
    [...state.resistance, ...state.support].forEach(l => {
      minP = Math.min(minP, l.price);
      maxP = Math.max(maxP, l.price);
    });
    const pad = (maxP - minP) * 0.08 || Math.abs(maxP) * 0.01 || 0.5;
    minP -= pad; maxP += pad;

    const yFor = p => padT + (1 - (p - minP) / (maxP - minP)) * chartH;
    const slot = chartW / visibleCount;
    const xFor = i => padL + i * slot + slot / 2;

    // grid
    ctx.strokeStyle = '#161d28';
    ctx.lineWidth = 1;
    const gridLines = 5;
    ctx.fillStyle = '#5c6b7e';
    ctx.font = '11px "IBM Plex Mono", monospace';
    ctx.textAlign = 'left';
    for (let i = 0; i <= gridLines; i++) {
      const p = minP + (maxP - minP) * (i / gridLines);
      const y = yFor(p);
      ctx.beginPath();
      ctx.moveTo(padL, y);
      ctx.lineTo(W - padR, y);
      ctx.stroke();
      ctx.fillText(fmt(p), W - padR + 8, y + 4);
    }

    const now = Date.now();
    const activeBreak = state.breakout && state.breakout.expiresAt > now ? state.breakout : null;

    function drawLevel(level, color, kind) {
      const isHit = activeBreak &&
        ((kind === 'resistance' && activeBreak.type === 'up') || (kind === 'support' && activeBreak.type === 'down')) &&
        Math.abs(level.price - activeBreak.level) / activeBreak.level < 0.0001;
      const y = yFor(level.price);
      ctx.save();
      if (isHit) {
        const pulse = 0.55 + 0.45 * Math.sin(now / 140);
        ctx.shadowColor = color;
        ctx.shadowBlur = 16;
        ctx.strokeStyle = color;
        ctx.globalAlpha = pulse;
        ctx.setLineDash([]);
        ctx.lineWidth = 2.6;
      } else {
        ctx.strokeStyle = color;
        ctx.globalAlpha = 0.85;
        ctx.setLineDash([5, 4]);
        ctx.lineWidth = 1.3;
      }
      ctx.beginPath();
      ctx.moveTo(padL, y);
      ctx.lineTo(W - padR, y);
      ctx.stroke();
      ctx.setLineDash([]);
      ctx.shadowBlur = 0;
      ctx.globalAlpha = 1;
      ctx.fillStyle = color;
      ctx.font = (isHit ? 'bold 11px' : '10.5px') + ' "IBM Plex Mono", monospace';
      ctx.fillText(fmt(level.price) + '  (' + level.touches + 'x)' + (isHit ? '  ← BREAK' : ''), padL + 4, y - 5);
      ctx.restore();
    }
    state.resistance.forEach(l => drawLevel(l, '#f2a93b', 'resistance'));
    state.support.forEach(l => drawLevel(l, '#4fb0ff', 'support'));

    // candles
    const bodyW = Math.max(2, slot * 0.6);
    visible.forEach((c, i) => {
      const x = xFor(i);
      const up = c.close >= c.open;
      const color = up ? '#34d1a4' : '#ef5b5b';
      ctx.strokeStyle = color;
      ctx.fillStyle = color;
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(x, yFor(c.high));
      ctx.lineTo(x, yFor(c.low));
      ctx.stroke();
      const yO = yFor(c.open), yC = yFor(c.close);
      const top = Math.min(yO, yC);
      const h = Math.max(1, Math.abs(yC - yO));
      ctx.fillRect(x - bodyW / 2, top, bodyW, h);
    });

    // price line + tag
    const lastC = visible[visible.length - 1];
    if (lastC) {
      const up = lastC.close >= lastC.open;
      const lineColor = activeBreak ? (activeBreak.type === 'up' ? '#34d1a4' : '#ef5b5b') : (up ? '#34d1a4' : '#ef5b5b');
      const y = yFor(lastC.close);
      ctx.save();
      if (activeBreak) {
        const pulse = 0.5 + 0.5 * Math.sin(now / 120);
        ctx.shadowColor = lineColor;
        ctx.shadowBlur = 14 * pulse + 4;
        ctx.lineWidth = 1.8;
      } else {
        ctx.lineWidth = 1;
      }
      ctx.strokeStyle = lineColor;
      ctx.setLineDash([2, 3]);
      ctx.beginPath();
      ctx.moveTo(padL, y);
      ctx.lineTo(W - padR, y);
      ctx.stroke();
      ctx.setLineDash([]);
      ctx.shadowBlur = 0;

      ctx.fillStyle = lineColor;
      ctx.fillRect(W - padR, y - 9, padR - 2, 18);
      ctx.fillStyle = '#0a0e14';
      ctx.font = 'bold 11px "IBM Plex Mono", monospace';
      ctx.textAlign = 'left';
      ctx.fillText(fmt(lastC.close), W - padR + 5, y + 4);
      ctx.restore();
    }

    // time labels
    ctx.fillStyle = '#5c6b7e';
    ctx.font = '10px "IBM Plex Mono", monospace';
    ctx.textAlign = 'center';
    const labelEvery = Math.max(1, Math.floor(visibleCount / 6));
    visible.forEach((c, i) => {
      if (i % labelEvery !== 0) return;
      const d = new Date(c.time);
      const label = `${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`;
      ctx.fillText(label, xFor(i), H - 8);
    });
  }

  let animHandle = null;
  function startAnimLoop() {
    if (animHandle) return;
    function loop() {
      const active = state.breakout && state.breakout.expiresAt > Date.now();
      drawChart();
      if (active) {
        animHandle = requestAnimationFrame(loop);
      } else {
        animHandle = null;
      }
    }
    animHandle = requestAnimationFrame(loop);
  }

  // ---------------- UI updates ----------------
  function updateTicker() {
    const candles = state.candles;
    if (!candles.length) return;
    const last = candles[candles.length - 1];
    const prev = candles.length > 1 ? candles[candles.length - 2] : last;
    priceEl.textContent = fmt(last.close);
    const diff = last.close - prev.close;
    const pct = (diff / prev.close) * 100;
    deltaEl.textContent = (diff >= 0 ? '+' : '') + diff.toFixed(cfg().decimals) + ' (' + (pct >= 0 ? '+' : '') + pct.toFixed(2) + '%)';
    deltaEl.className = 'delta mono ' + (diff >= 0 ? 'pos' : 'neg');
  }

  function renderLevels() {
    const now = Date.now();
    const activeBreak = state.breakout && state.breakout.expiresAt > now ? state.breakout : null;
    function renderList(container, levels, cls) {
      if (!levels.length) {
        container.innerHTML = '<div class="empty-hint">No clear level yet.</div>';
        return;
      }
      container.innerHTML = levels
        .slice().sort((a,b) => cls === 'resistance' ? a.price - b.price : b.price - a.price)
        .map(l => {
          const isHit = activeBreak &&
            ((cls === 'resistance' && activeBreak.type === 'up') || (cls === 'support' && activeBreak.type === 'down')) &&
            Math.abs(l.price - activeBreak.level) / activeBreak.level < 0.0001;
          const conf = l.confidence != null ? l.confidence : 50;
          const strength = levelStrengthLabel(conf);
          return `<div class="level-row ${cls}${isHit ? ' hit' : ''}" style="${isHit ? 'color:' + (cls==='resistance'?'#f2a93b':'#4fb0ff') : ''}">` +
            `<span class="lp">${fmt(l.price)}</span>` +
            `<span class="lmeta"><span class="strength-badge ${strength}">${conf}% ${strength.toUpperCase()}</span>` +
            `<span class="lt">${l.touches} touch${l.touches>1?'es':''}${isHit ? ' · LIVE BREAK' : ''}</span></span></div>`;
        })
        .join('');
    }
    renderList(resList, state.resistance, 'resistance');
    renderList(supList, state.support, 'support');
  }

  function setConnecting() {
    statusPill.classList.remove('live', 'td', 'closed', 'notlive');
    statusText.textContent = 'Connecting…';
  }

  function setStatus(source) {
    state.source = source;
    const closed = isMarketClosed(state.symKey);
    state.marketClosed = closed;
    state.isLive = !closed && source !== 'demo';
    statusPill.classList.remove('live', 'td', 'closed', 'notlive');
    if (closed) {
      statusPill.classList.add('closed');
      statusText.textContent = 'Market Closed';
    } else if (source === 'yahoo') {
      statusPill.classList.add('live');
      statusText.textContent = 'LIVE (Yahoo)';
    } else if (source === 'binance') {
      statusPill.classList.add('live');
      statusText.textContent = 'LIVE (Binance)';
    } else if (source === 'twelvedata') {
      statusPill.classList.add('td');
      statusText.textContent = 'LIVE (Twelve Data)';
    } else {
      statusPill.classList.add('notlive');
      statusText.textContent = 'Not Live';
    }
  }

  // ---------------- 24h Outlook ----------------
  const OUTLOOK_ANCHOR_HOURS = [3, 23];

  function getWindowAnchor(date) {
    let best = null;
    for (const dayOffset of [-1, 0]) {
      for (const h of OUTLOOK_ANCHOR_HOURS) {
        const a = new Date(date);
        a.setDate(a.getDate() + dayOffset);
        a.setHours(h, 0, 0, 0);
        if (a.getTime() <= date.getTime() && (!best || a.getTime() > best.getTime())) best = a;
      }
    }
    return best;
  }

  function getNextAnchor(anchorTime) {
    const a = new Date(anchorTime);
    if (a.getHours() === OUTLOOK_ANCHOR_HOURS[0]) {
      a.setHours(OUTLOOK_ANCHOR_HOURS[1], 0, 0, 0);
    } else {
      a.setDate(a.getDate() + 1);
      a.setHours(OUTLOOK_ANCHOR_HOURS[0], 0, 0, 0);
    }
    return a;
  }

  function renderForecast() {
    const f = state.forecast;
    if (!f) return;
    const start = new Date(f.anchorTime);
    const end = new Date(f.anchorTime + 24 * 3600 * 1000);
    const opts = { weekday: 'short', hour: '2-digit', minute: '2-digit' };
    forecastWindow.textContent = `${start.toLocaleString([], opts)} → ${end.toLocaleString([], opts)}`;
    forecastHigh.textContent = fmt(f.high);
    forecastHighPct.textContent = '+' + f.pct.toFixed(2) + '%';
    forecastHighConf.textContent = '· 95% confidence';
    forecastLow.textContent = fmt(f.low);
    forecastLowPct.textContent = '-' + f.pct.toFixed(2) + '%';
    forecastLowConf.textContent = '· 95% confidence';
    forecastNeutral.textContent = fmt(f.neutral);
    forecastNeutralConf.textContent = state.isLive ? '99% confidence (live price)' : 'demo price';
    const next = getNextAnchor(f.anchorTime);
    forecastNext.textContent = 'Next update: ' + next.toLocaleString([], opts);
  }

  async function computeForecast(force) {
    const anchor = getWindowAnchor(new Date());
    if (!force && state.forecast && state.forecast.anchorTime === anchor.getTime() && state.forecast.symKey === state.symKey) return;
    const basePrice = state.candles.length ? state.candles[state.candles.length - 1].close : cfg().demoBase;
    let atrPct = null;
    try { atrPct = await fetchDailyAtrPct(); } catch (e) { atrPct = null; }
    if (!atrPct) atrPct = cfg().fallbackAtrPct;
    state.forecast = {
      high: basePrice * (1 + atrPct / 100),
      low: basePrice * (1 - atrPct / 100),
      neutral: basePrice,
      pct: atrPct,
      anchorTime: anchor.getTime(),
      symKey: state.symKey,
    };
    renderForecast();
  }

  // ---------------- Main cycle ----------------
  async function refreshData(isInitial) {
    if (isInitial) loadingOverlay.classList.remove('hidden');
    if (!isMarketClosed(state.symKey)) setConnecting();
    let result = null;
    try {
      result = await fetchLiveCandles1m();
    } catch (e) { result = null; }

    if (result && result.candles.length > 20) {
      state.candles1m = result.candles;
      setStatus(result.source);
    } else {
      state.candles1m = generateDemo1m(state.candles1m);
      setStatus('demo');
    }

    state.candles = aggregate(state.candles1m, state.tf);

    const levels = computeLevels(state.candles);
    state.resistance = levels.resistance;
    state.support = levels.support;

    handleBreakoutIfAny();

    updateTicker();
    renderLevels();
    renderSignal();
    resizeCanvas();
    drawChart();
    loadingOverlay.classList.add('hidden');
    state.countdown = TF_REFRESH[state.tf];
    countdownEl.textContent = state.countdown + 's';

    computeForecast(false);
  }

  function tickCountdown() {
    state.countdown -= 1;
    if (state.countdown <= 0) {
      refreshData(false);
    } else {
      countdownEl.textContent = state.countdown + 's';
    }
  }

  function resetForNewSelection() {
    state.candles1m = [];
    state.candles = [];
    state.lastAlertedTime = null;
    state.breakout = null;
    state.activeYahooSymbol = null;
    alertLog.innerHTML = '<li class="empty-hint" style="border:none;background:none;">No breakouts yet.</li>';
    symLabel.textContent = cfg().label;
    signalPill.style.display = 'none';
    signalPill.classList.remove('buy', 'sell');
    refreshData(true).then(() => computeForecast(true));
  }

  // ---------------- Events ----------------
  document.getElementById('tfGroup').addEventListener('click', (e) => {
    const btn = e.target.closest('.tf-btn');
    if (!btn) return;
    document.querySelectorAll('.tf-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    state.tf = parseInt(btn.dataset.tf, 10);
    state.candles = aggregate(state.candles1m, state.tf);
    state.lastAlertedTime = null;
    state.breakout = null;
    const levels = computeLevels(state.candles);
    state.resistance = levels.resistance;
    state.support = levels.support;
    updateTicker();
    renderLevels();
    renderSignal();
    resizeCanvas();
    drawChart();
    state.countdown = TF_REFRESH[state.tf];
    countdownEl.textContent = state.countdown + 's';
  });

  document.getElementById('symGroup').addEventListener('click', (e) => {
    const btn = e.target.closest('.sym-btn');
    if (!btn) return;
    document.querySelectorAll('.sym-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    state.symKey = btn.dataset.sym;
    resetForNewSelection();
  });

  document.getElementById('refreshBtn').addEventListener('click', () => refreshData(false));
  window.addEventListener('resize', () => { resizeCanvas(); drawChart(); });

  gearBtn.addEventListener('click', () => settingsPanel.classList.toggle('open'));
  document.addEventListener('click', (e) => {
    if (!settingsPanel.contains(e.target) && e.target !== gearBtn && settingsPanel.classList.contains('open')) {
      settingsPanel.classList.remove('open');
    }
  });
  document.getElementById('saveKeyBtn').addEventListener('click', () => {
    const val = apiKeyInput.value.trim();
    if (!val) { keyStatus.textContent = 'Enter a key first.'; return; }
    state.apiKey = val;
    keyStatus.textContent = 'Saved for this session — refreshing…';
    refreshData(true);
  });
  document.getElementById('clearKeyBtn').addEventListener('click', () => {
    state.apiKey = null;
    apiKeyInput.value = '';
    keyStatus.textContent = 'Cleared — using free public sources.';
    refreshData(true);
  });

  // ---------------- Init ----------------
  symLabel.textContent = cfg().label;
  resizeCanvas();
  refreshData(true).then(() => computeForecast(false));
  setInterval(tickCountdown, 1000);
  setInterval(tickLive, 1500);
  setInterval(() => computeForecast(false), 60000);
})();
</script>
</body>
</html>
