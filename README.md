<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>SYS 469 Deliverable 5 – Hi-Fi Tesla App</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <style>
    :root {
      --red:   #E82127;
      --black: #171A20;
      --gray:  #E0E0E0;
      --r:      12px;
      --font:   -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      --base-size: 16px;
    }
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: var(--font);
    }
    html { font-size: var(--base-size); }
    body {
      background: #fff;
      color: var(--black);
      overscroll-behavior: none;
    }
    /* ===== HEADER ===== */
    #header {
      position: fixed; top: 0; left: 0;
      width: 100%; height: 48px;
      background: #fff; border-bottom: 1px solid #ddd;
      display: flex; align-items: center;
      justify-content: space-between; padding: 0 12px;
      z-index: 1000;
    }
    #header button {
      background: none; border: none; font-size: 1rem; cursor: pointer;
      padding: 6px;
    }
    #header label {
      font-size: 0.9rem; display: flex; align-items: center; gap: 4px;
    }
    /* ===== BANNER ===== */
    #banner {
      position: fixed; top: 48px; left: 0;
      width: 100%; padding: 10px 12px;
      background: var(--black); color: #fff;
      text-align: center; font-size: 0.9rem;
      transform: translateY(-100%); transition: transform 0.3s ease;
      z-index: 900;
    }
    #banner.visible { transform: translateY(0); }
    /* ===== SECTIONS ===== */
    section {
      display: none;
      position: fixed; inset: 48px 0 144px;
      overflow: auto; padding: 12px 16px;
    }
    section.active { display: block; }
    h1 {
      text-align: center;
      font-weight: 600;
      font-size: 1.25rem;
      margin-bottom: 12px;
    }
    .content { display: flex; flex-direction: column; align-items: center; gap: 16px; }
    .placeholder {
      width: 100%; background: var(--gray);
      border: 1px dashed #aaa; border-radius: var(--r);
    }
    /* ===== BUTTON ===== */
    .btn {
      display: block; width: 90%; max-width: 320px;
      padding: 14px 0; text-align: center;
      background: var(--red); color: #fff;
      border: none; border-radius: var(--r);
      font-weight: 500; font-size: 1rem;
      cursor: pointer; position: relative;
    }
    .btn:disabled { background: #ccc; cursor: not-allowed; }
    /* ===== INPUTS ===== */
    input[type="search"], input[type="time"], input[type="number"], select {
      width: 90%; max-width: 320px;
      padding: 8px; font-size: 1rem;
      border: 1px solid #ccc; border-radius: 6px;
    }
    input[type="range"] {
      -webkit-appearance: none;
      width: 90%; max-width: 320px;
      height: 6px; background: #ddd; border-radius: 3px;
    }
    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 18px; height: 18px; border-radius: 50%;
      background: var(--red); cursor: pointer;
    }
    /* ===== MINI PLAYER ===== */
    #miniPlayer {
      position: fixed; bottom: 88px; left: 0;
      width: 100%; padding: 8px 16px;
      background: #fff; border-top: 1px solid #ddd;
      display: flex; align-items: center; gap: 12px;
      z-index: 500;
    }
    #miniPlayer img {
      width: 40px; height: 40px; border-radius: 6px;
    }
    #miniPlayer .track {
      flex: 1; font-size: 0.95rem; white-space: nowrap;
      overflow: hidden; text-overflow: ellipsis;
    }
    /* ===== NAV ===== */
    nav {
      position: fixed; bottom: 0; left: 0;
      width: 100%; height: 88px;
      background: #fff; border-top: 1px solid #ddd;
      display: flex;
    }
    nav a {
      flex: 1; text-align: center; padding-top: 10px;
      font-size: 0.85rem; color: #666; text-decoration: none;
      cursor: pointer;
    }
    nav a.active { color: var(--red); font-weight: 600; }
    nav svg { display: block; margin: 0 auto 4px; height: 24px; fill: currentColor; }

    /* ===== CONFIRM MODAL ===== */
    #confirmModal {
      position: fixed; inset: 0;
      background: rgba(0,0,0,0.6); display: none;
      align-items: center; justify-content: center;
      z-index: 1001;
    }
    #confirmModal .dialog {
      background: #fff; padding: 20px; border-radius: 8px;
      width: 80%; max-width: 280px; text-align: center;
    }
    #confirmModal button {
      margin: 8px; padding: 8px 12px; font-size: 1rem;
      border: none; border-radius: 6px; cursor: pointer;
    }
    #confirmModal button.confirm {
      background: var(--red); color: #fff; cursor: not-allowed;
    }
    #confirmModal button.cancel {
      background: #ccc; color: #000;
    }
  </style>
</head>
<body>

  <!-- HEADER -->
  <div id="header">
    <div>
      <button id="decreaseText">A–</button>
      <button id="increaseText">A+</button>
    </div>
    <label>
      <input type="checkbox" id="kidsMode"> Kids Mode
    </label>
  </div>

  <!-- BANNER -->
  <div id="banner"></div>

  <!-- DRIVE SCREEN -->
  <section id="Drive" class="active">
    <h1>Drive</h1>
    <div class="content">
      <iframe
        title="Map"
        src="https://maps.google.com/maps?q=37.3947,-122.1500&z=14&output=embed"
        class="placeholder"
        style="height:48vh; border:none; border-radius:var(--r); box-shadow:0 2px 8px rgba(0,0,0,0.1);">
      </iframe>
      <input type="search" id="searchInput" placeholder="Search address…">
      <button class="btn" onclick="doSearch()">Search</button>
      <select id="favInput">
        <option disabled selected>Choose Favorite…</option>
        <option>Home</option>
        <option>Work</option>
      </select>
      <button class="btn" onclick="goFavorite()">Go to Favorite</button>
    </div>
  </section>

  <!-- AUDIO SCREEN -->
  <section id="Audio">
    <h1>Audio</h1>
    <div class="content">
      <img src="https://via.placeholder.com/80" alt="Album art" style="border-radius:6px;">
      <div class="track">Dreams • Fleetwood Mac</div>
      <select id="source" onchange="doSource()" style="margin-top:8px;">
        <option>Bluetooth</option>
        <option>Radio</option>
        <option>Spotify</option>
      </select>
      <input id="volSlider" type="range" min="0" max="100" value="40" oninput="doVolume(this.value)">
    </div>
  </section>

  <!-- CLIMATE SCREEN -->
  <section id="Climate">
    <h1>Climate</h1>
    <div class="content">
      <button class="btn" onclick="syncZones()">Sync All Zones to 72°F</button>
      <label style="width:90%;text-align:left;">Pre-condition time
        <input type="time" id="preTime" value="07:30">
      </label>
      <button class="btn" onclick="schedulePrecondition()">Schedule Pre-Condition</button>
    </div>
  </section>

  <!-- SEATS SCREEN -->
  <section id="Seats">
    <h1>Seats & Storage</h1>
    <div class="content">
      <div class="placeholder" style="height:140px;line-height:140px;text-align:center;">
        Car Top View
      </div>
      <button class="btn" onclick="confirmAction('Unlock vehicle?', unlock)">Unlock / Lock</button>
      <button class="btn" onclick="confirmAction('Open trunk?', openTrunk)">Open Trunk</button>
      <button class="btn" onclick="viewCamera()">View Cabin Camera</button>
    </div>
  </section>

  <!-- MINI PLAYER -->
  <div id="miniPlayer">
    <img src="https://via.placeholder.com/40" alt="">
    <div class="track">Dreams • Fleetwood Mac</div>
    <input id="miniVol" type="range" min="0" max="100" value="40" oninput="doVolume(this.value)">
  </div>

  <!-- NAVIGATION -->
  <nav>
    <a data-target="Drive" class="active"><svg viewBox="0 0 24 24"><path d="M12 4L2 9l10 5 10-5-10-5z"/></svg>Drive</a>
    <a data-target="Audio"><svg viewBox="0 0 24 24"><path d="M3 22h3V2H3v20zm5 0h3V2H8v20zm5 0h3V2h-3v20z"/></svg>Audio</a>
    <a data-target="Climate"><svg viewBox="0 0 24 24"><path d="M6 22l6-20 6 20H6z"/></svg>Climate</a>
    <a data-target="Seats"><svg viewBox="0 0 24 24"><path d="M4 10h16v2H4zm0 4h16v2H4z"/></svg>Seats</a>
  </nav>

  <!-- CONFIRMATION MODAL -->
  <div id="confirmModal">
    <div class="dialog">
      <p id="confirmText"></p>
      <button class="confirm" id="confirmBtn" disabled>Confirm (<span id="countdown">3</span>)</button>
      <button class="cancel" onclick="closeModal()">Cancel</button>
    </div>
  </div>

  <script>
    // NAVIGATION HANDLER
    document.querySelectorAll('nav a').forEach(link => {
      link.addEventListener('click', e => {
        e.preventDefault();
        document.querySelectorAll('nav a').forEach(a => a.classList.remove('active'));
        link.classList.add('active');
        const tgt = link.dataset.target;
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.getElementById(tgt).classList.add('active');
      });
    });

    // HEADER CONTROLS
    document.getElementById('increaseText').onclick = () => {
      let size = parseFloat(getComputedStyle(document.documentElement).fontSize);
      document.documentElement.style.fontSize = (size * 1.1) + 'px';
    };
    document.getElementById('decreaseText').onclick = () => {
      let size = parseFloat(getComputedStyle(document.documentElement).fontSize);
      document.documentElement.style.fontSize = (size * 0.9) + 'px';
    };
    document.getElementById('kidsMode').onchange = e => {
      const disabled = e.target.checked;
      document.getElementById('source').disabled = disabled;
      document.getElementById('volSlider').disabled = disabled;
      document.getElementById('miniVol').disabled = disabled;
      showBanner(disabled ? 'Kids Mode ON' : 'Kids Mode OFF');
      speak(disabled ? 'Kids mode on' : 'Kids mode off');
    };

    // BANNER
    function showBanner(text) {
      const b = document.getElementById('banner');
      b.textContent = text;
      b.classList.add('visible');
      setTimeout(() => b.classList.remove('visible'), 3000);
    }
    // VOICE PROMPTS
    function speak(text) {
      if (!window.speechSynthesis) return;
      const u = new SpeechSynthesisUtterance(text);
      window.speechSynthesis.speak(u);
    }

    // FLOW FUNCTIONS
    function doSearch() {
      const addr = document.getElementById('searchInput').value.trim();
      if (!addr) { showBanner('Please enter an address'); return; }
      showBanner('Routing to ' + addr + '… ETA 10 min');
      speak('Routing to ' + addr);
    }
    function goFavorite() {
      const fav = document.getElementById('favInput').value;
      if (!fav) { showBanner('Please choose a favorite'); return; }
      showBanner('Routing to ' + fav + '… ETA 10 min');
      speak('Routing to ' + fav);
    }
    function doSource() {
      const s = document.getElementById('source').value;
      showBanner('Source set to ' + s);
      speak('Source set to ' + s);
    }
    function doVolume(val) {
      showBanner('Volume ' + val + '%');
      speak('Volume ' + val + 'percent');
    }
    function syncZones() {
      showBanner('All zones synced to 72°F');
      speak('Zones synced');
    }
    function schedulePrecondition() {
      const t = document.getElementById('preTime').value;
      if (!t) { showBanner('Please pick a time'); return; }
      showBanner('Pre-condition set at ' + t);
      speak('Pre condition set at ' + t);
    }
    function viewCamera() {
      showBanner('Showing live cabin camera');
      speak('Live camera view');
    }

    // CONFIRMATION MODAL
    let confirmCallback;
    function confirmAction(text, callback) {
      confirmCallback = callback;
      document.getElementById('confirmText').textContent = text;
      const btn = document.getElementById('confirmBtn');
      const cd = document.getElementById('countdown');
      let count = 3;
      btn.disabled = true;
      cd.textContent = count;
      document.getElementById('confirmModal').style.display = 'flex';
      const interval = setInterval(() => {
        count--;
        cd.textContent = count;
        if (count <= 0) {
          clearInterval(interval);
          btn.disabled = false;
        }
      }, 1000);
    }
    function closeModal() {
      document.getElementById('confirmModal').style.display = 'none';
    }
    document.getElementById('confirmBtn').onclick = () => {
      closeModal();
      if (confirmCallback) confirmCallback();
    };

    function unlock() {
      showBanner('Vehicle unlocked');
      speak('Vehicle unlocked');
    }
    function openTrunk() {
      showBanner('Trunk opened');
      speak('Trunk opened');
    }
  </script>
</body>
</html>
