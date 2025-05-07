<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>SYS 469 Mid-Fi Prototype</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <style>
    :root {
      --red:   #E82127;
      --black: #171A20;
      --gray:  #E0E0E0;
      --r:      12px;
      --font:   -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;
    }
    * { box-sizing:border-box; margin:0; padding:0; font-family:var(--font); }
    body { background:#fff; color:var(--black); overscroll-behavior:none; }
    section {
      display:none;
      position:fixed; inset:0;
      padding:64px 0 88px;
      overflow:auto;
    }
    section.active {
      display:block;
      animation:fade .2s;
    }
    @keyframes fade { from { opacity:0 } to { opacity:1 } }
    h1 {
      text-align:center;
      font:600 20px/1 var(--font);
      margin-bottom:12px;
    }
    .content { padding:0 20px; }
    .placeholder {
      background:var(--gray);
      border:1px dashed #aaa;
      border-radius:var(--r);
    }
    .btn {
      display:block;
      width:90%; max-width:320px;
      margin:18px auto;
      padding:14px 0;
      border:2px solid var(--black);
      border-radius:var(--r);
      text-align:center;
      font-weight:500;
      cursor:pointer;
    }
    input[type=range] {
      -webkit-appearance:none;
      width:100%; height:6px;
      margin:12px 0;
      background:#ccc;
      border-radius:3px;
    }
    input[type=range]::-webkit-slider-thumb {
      -webkit-appearance:none;
      width:18px; height:18px;
      border-radius:50%;
      background:var(--red);
      cursor:pointer;
    }
    nav {
      position:fixed; bottom:0; left:0;
      width:100%; height:88px;
      background:#fff; border-top:1px solid #ddd;
      display:flex;
    }
    nav a {
      flex:1;
      text-align:center;
      padding-top:10px;
      font:12px/1 var(--font);
      color:#666;
      text-decoration:none;
      cursor:pointer;
    }
    nav a.active {
      color:var(--red);
      font-weight:600;
    }
    nav svg {
      display:block;
      margin:0 auto 4px;
      height:24px;
      fill:currentColor;
    }
  </style>
</head>
<body>

  <!-- DRIVE -->
  <section id="Drive" class="active">
    <h1>Drive</h1>
    <div class="content">
      <div class="placeholder" style="height:48vh;"></div>
      <div class="btn" onclick="alert('Autopilot engaged!')">Start Drive</div>
    </div>
  </section>

  <!-- AUDIO -->
  <section id="Audio">
    <h1>Audio</h1>
    <div class="content" style="text-align:center;">
      <div class="placeholder"
           style="width:220px;height:220px;margin:0 auto;border-radius:var(--r);"></div>
      <div style="margin:14px 0;font-size:17px;">Fleetwood Mac – Dreams</div>
      <label>
        Volume 
        <input type="range" oninput="this.nextElementSibling.textContent=this.value">
        <span>50</span>
      </label><br><br>
      <label>
        Source 
        <select onchange="alert('Source → '+this.value)">
          <option>Bluetooth</option>
          <option>Radio</option>
          <option>Spotify</option>
        </select>
      </label>
    </div>
  </section>

  <!-- CLIMATE -->
  <section id="Climate">
    <h1>Climate</h1>
    <div class="content">
      <label>Driver Temp <input type="number" value="72" style="width:64px;"></label><br><br>
      <label>Passenger Temp <input type="number" value="72" style="width:64px;"></label><br><br>
      <label>Fan <input type="range"></label><br><br>
      <label><input type="checkbox" checked> Auto</label>
    </div>
  </section>

  <!-- SEATS -->
  <section id="Seats">
    <h1>Seats / Storage</h1>
    <div class="content">
      <div class="placeholder"
           style="height:160px;border-radius:var(--r);margin-bottom:18px;
                  line-height:160px;text-align:center;">
        Car Top View
      </div>
      <label>Seat Position <input type="range"></label><br><br>
      <div class="btn" onclick="alert('Vehicle unlocked')">Unlock / Lock</div>
      <div class="btn" onclick="alert('Trunk opened')">Open Trunk</div>
    </div>
  </section>

  <!-- NAVIGATION -->
  <nav>
    <a data-target="Drive"  class="active">
      <svg viewBox="0 0 24 24"><path d="M12 4L2 9l10 5 10-5-10-5z"/></svg>Drive
    </a>
    <a data-target="Audio">
      <svg viewBox="0 0 24 24"><path d="M3 22h3V2H3v20zm5 0h3V2H8v20zm5 0h3V2h-3v20z"/></svg>Audio
    </a>
    <a data-target="Climate">
      <svg viewBox="0 0 24 24"><path d="M6 22l6-20 6 20H6z"/></svg>Climate
    </a>
    <a data-target="Seats">
      <svg viewBox="0 0 24 24"><path d="M4 10h16v2H4zm0 4h16v2H4z"/></svg>Seats
    </a>
  </nav>

  <script>
    // single handler for all tabs
    document.querySelectorAll('nav a').forEach(link => {
      link.addEventListener('click', e => {
        e.preventDefault();
        // deactivate sections
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        // activate target section
        const tgt = link.getAttribute('data-target');
        document.getElementById(tgt).classList.add('active');
        // update nav highlight
        document.querySelectorAll('nav a').forEach(a => a.classList.remove('active'));
        link.classList.add('active');
      });
    });
  </script>
</body>
</html>
