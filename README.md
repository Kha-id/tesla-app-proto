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
      --font:   -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;
      --base:   16px;
    }
    * { box-sizing:border-box; margin:0; padding:0; font-family:var(--font); }
    html { font-size: var(--base); }
    body { background:#fff; color:var(--black); overscroll-behavior:none; }
    /* HEADER */
    #header {
      position:fixed; top:0; left:0; right:0; height:48px;
      background:#fff; border-bottom:1px solid #ddd;
      display:flex; align-items:center; justify-content:space-between;
      padding:0 12px; z-index:1000;
    }
    #header button { background:none; border:none; font-size:1rem; cursor:pointer; }
    #header label { font-size:0.9rem; display:flex; align-items:center; gap:4px; }
    /* BANNER */
    #banner {
      position:fixed; top:48px; left:0; right:0;
      padding:10px 12px; background:var(--black); color:#fff;
      text-align:center; font-size:0.9rem;
      transform:translateY(-100%); transition:transform .3s;
      z-index:900;
    }
    #banner.visible { transform:translateY(0); }
    /* SECTIONS */
    section {
      display:none; position:fixed;
      top:48px; bottom:144px; left:0; right:0;
      overflow:auto; padding:12px 16px;
    }
    section.active { display:block; animation:fade .2s; }
    @keyframes fade { from{opacity:0} to{opacity:1} }
    h1 { text-align:center; font:600 1.25rem var(--font); margin-bottom:12px; }
    .content { display:flex; flex-direction:column; align-items:center; gap:16px; }
    .placeholder {
      background:var(--gray); border:1px dashed #aaa; border-radius:var(--r);
    }
    .album-art { width:80px; height:80px; border-radius:6px; }
    /* BUTTONS & INPUTS */
    .btn {
      width:90%; max-width:320px; padding:14px 0;
      background:var(--red); color:#fff; border:none; border-radius:var(--r);
      font-weight:500; font-size:1rem; cursor:pointer;
    }
    select, input[type=search], input[type=time] {
      width:90%; max-width:320px; padding:8px; font-size:1rem;
      border:1px solid #ccc; border-radius:6px;
    }
    input[type=range] {
      -webkit-appearance:none; width:90%; max-width:320px; height:6px;
      background:#ddd; border-radius:3px; margin:12px 0;
    }
    input[type=range]::-webkit-slider-thumb {
      -webkit-appearance:none; width:18px; height:18px;
      border-radius:50%; background:var(--red); cursor:pointer;
    }
    /* MINI PLAYER */
    #miniPlayer {
      position:fixed; bottom:88px; left:0; right:0;
      background:#fff; border-top:1px solid #ddd;
      display:flex; align-items:center; gap:12px; padding:8px 16px;
      z-index:500;
    }
    #miniPlayer .avatar {
      width:40px; height:40px; border-radius:6px; background:var(--gray);
    }
    #miniPlayer .track { flex:1; font-size:0.95rem; }
    /* NAVIGATION */
    nav {
      position:fixed; bottom:0; left:0; right:0; height:88px;
      background:#fff; border-top:1px solid #ddd; display:flex;
    }
    nav a {
      flex:1; text-align:center; padding-top:10px;
      font-size:0.85rem; color:#666; text-decoration:none; cursor:pointer;
    }
    nav a.active { color:var(--red); font-weight:600; }
    nav svg { display:block; margin:0 auto 4px; height:24px; fill:currentColor; }
    /* CONFIRMATION MODAL */
    #confirmModal {
      position:fixed; inset:0; background:rgba(0,0,0,0.6);
      display:flex; align-items:center; justify-content:center; visibility:hidden;
      opacity:0; transition:visibility 0s .3s, opacity .3s;
    }
    #confirmModal.show {
      visibility:visible; opacity:1; transition-delay:0s;
    }
    #confirmModal .dialog {
      background:#fff; padding:20px; border-radius:8px; text-align:center;
      width:80%; max-width:280px;
    }
    #confirmModal button {
      margin:8px; padding:8px 12px; font-size:1rem; border:none; border-radius:6px; cursor:pointer;
    }
    #confirmModal .confirm { background:var(--red); color:#fff; cursor:not-allowed; }
    #confirmModal .cancel { background:#ccc; color:#000; }
  </style>
</head>
<body>

  <!-- HEADER -->
  <div id="header">
    <div>
      <button id="decreaseText">A–</button>
      <button id="increaseText">A+</button>
    </div>
    <label><input type="checkbox" id="kidsMode"> Kids Mode</label>
  </div>

  <!-- BANNER -->
  <div id="banner"></div>

  <!-- DRIVE SCREEN -->
  <section id="Drive" class="active">
    <h1>Drive</h1>
    <div class="content">
      <iframe
        src="https://maps.google.com/maps?q=37.3947,-122.1500&z=14&output=embed"
        class="placeholder" style="height:48vh; border:none; border-radius:var(--r);">
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
      <div class="placeholder album-art"></div>
      <div class="track">Dreams • Fleetwood Mac</div>
      <select id="source" onchange="doSource()">
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
      <label>Pre-condition time
        <input type="time" id="preTime" value="07:30">
      </label>
      <button class="btn" onclick="schedulePrecondition()">Schedule Pre-Condition</button>
    </div>
  </section>

  <!-- SEATS SCREEN -->
  <section id="Seats">
    <h1>Seats &amp; Storage</h1>
    <div class="content">
      <div class="placeholder" style="height:140px; line-height:140px; text-align:center; border-radius:var(--r);">
        Car Top View
      </div>
      <button class="btn" onclick="confirmAction('Unlock vehicle?', unlock)">Unlock / Lock</button>
      <button class="btn" onclick="confirmAction('Open trunk?', openTrunk)">Open Trunk</button>
      <button class="btn" onclick="viewCamera()">View Cabin Camera</button>
    </div>
  </section>

  <!-- MINI PLAYER -->
  <div id="miniPlayer">
    <div class="avatar"></div>
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

  <!-- CONFIRM MODAL -->
  <div id="confirmModal">
    <div class="dialog">
      <p id="confirmText"></p>
      <button class="confirm" id="confirmBtn" disabled>Confirm (<span id="countdown">3</span>)</button>
      <button class="cancel" onclick="closeModal()">Cancel</button>
    </div>
  </div>

  <script>
    // NAVIGATION
    document.querySelectorAll('nav a').forEach(link => {
      link.addEventListener('click', e => {
        e.preventDefault();
        document.querySelectorAll('nav a').forEach(a=>a.classList.remove('active'));
        link.classList.add('active');
        document.querySelectorAll('section').forEach(s=>s.classList.remove('active'));
        document.getElementById(link.dataset.target).classList.add('active');
      });
    });

    // TEXT-SIZING
    document.getElementById('increaseText').onclick = () => {
      let s = parseFloat(getComputedStyle(document.documentElement).fontSize);
      document.documentElement.style.fontSize = (s*1.1)+'px';
    };
    document.getElementById('decreaseText').onclick = () => {
      let s = parseFloat(getComputedStyle(document.documentElement).fontSize);
      document.documentElement.style.fontSize = (s*0.9)+'px';
    };

    // KIDS MODE TOGGLE
    document.getElementById('kidsMode').onchange = e => {
      let dis = e.target.checked;
      document.getElementById('source').disabled = dis;
      document.getElementById('volSlider').disabled = dis;
      document.getElementById('miniVol').disabled = dis;
      showBanner(dis?'Kids Mode ON':'Kids Mode OFF');
    };

    // BANNER UTILITY
    function showBanner(txt) {
      let b = document.getElementById('banner');
      b.textContent = txt; b.classList.add('visible');
      setTimeout(()=>b.classList.remove('visible'),3000);
    }

    // FLOW FUNCTIONS
    function doSearch() {
      let a = document.getElementById('searchInput').value.trim();
      if(!a) return showBanner('Enter address');
      showBanner('Routing to '+a);
    }
    function goFavorite() {
      let f = document.getElementById('favInput').value;
      if(!f) return showBanner('Choose favorite');
      showBanner('Routing to '+f);
    }
    function doSource() {
      showBanner('Source set to '+document.getElementById('source').value);
    }
    function doVolume(v) {
      showBanner('Volume '+v+'%');
    }
    function syncZones() {
      showBanner('All zones synced');
    }
    function schedulePrecondition() {
      let t = document.getElementById('preTime').value;
      if(!t) return showBanner('Pick time');
      showBanner('Pre-condition '+t);
    }
    function viewCamera() {
      showBanner('Showing cabin camera');
    }

    // CONFIRMATION MODAL
    let confirmCallback;
    function confirmAction(txt, cb) {
      confirmCallback = cb;
      document.getElementById('confirmText').textContent = txt;
      document.getElementById('confirmModal').classList.add('show');
      let c=3, btn=document.getElementById('confirmBtn'), cd=document.getElementById('countdown');
      btn.disabled=true; cd.textContent=c;
      let iv = setInterval(()=>{
        c--; cd.textContent=c;
        if(c<=0){ clearInterval(iv); btn.disabled=false; }
      },1000);
    }
    function closeModal() {
      document.getElementById('confirmModal').classList.remove('show');
    }
    document.getElementById('confirmBtn').onclick = ()=>{
      closeModal(); if(confirmCallback) confirmCallback();
    };
    function unlock(){ showBanner('Vehicle unlocked'); }
    function openTrunk(){ showBanner('Trunk opened'); }
  </script>
</body>
</html>
