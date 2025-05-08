<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>SYS 469 • D3 Mid-Fi (8 Flows)</title>
<meta name="viewport" content="width=device-width,initial-scale=1">
<style>
  :root{--red:#E82127;--black:#171A20;--gray:#E0E0E0;--r:12px;--font:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif}
  *{box-sizing:border-box;margin:0;padding:0;font-family:var(--font)}
  body{background:#fff;color:var(--black);overscroll-behavior:none}
  section{display:none;position:fixed;inset:0;padding:64px 0 88px;overflow:auto}
  section.active{display:block;animation:fade .2s}
  @keyframes fade{from{opacity:0}to{opacity:1}}
  h1{text-align:center;font:600 20px/1 var(--font);margin-bottom:12px}
  .content{padding:0 20px}
  .placeholder{background:var(--gray);border:1px dashed #aaa;border-radius:var(--r)}
  .btn{display:block;width:90%;max-width:320px;margin:14px auto;padding:12px 0;
       border:2px solid var(--black);border-radius:var(--r);
       text-align:center;font-weight:500;cursor:pointer}
  select,input[type=number],input[type=time]{width:90%;max-width:320px;
       margin:8px auto;display:block;padding:8px;border:1px solid #ccc;border-radius:4px}
  input[type=range]{-webkit-appearance:none;width:90%;max-width:320px;height:6px;
       margin:12px auto;background:#ccc;border-radius:3px;display:block}
  input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;
       width:18px;height:18px;border-radius:50%;background:var(--red);cursor:pointer}
  nav{position:fixed;bottom:0;left:0;width:100%;height:88px;
      background:#fff;border-top:1px solid #ddd;display:flex}
  nav a{flex:1;text-align:center;padding-top:10px;font:12px/1 var(--font);
         color:#666;text-decoration:none;cursor:pointer}
  nav a.active{color:var(--red);font-weight:600}
  nav svg{display:block;margin:0 auto 4px;height:24px;fill:currentColor}
</style>
</head>
<body>

<!-- 1. Destination Entry -->
<section id="Drive" class="active">
  <h1>Drive</h1>
  <div class="content">
    <div class="placeholder" style="height:48vh;"></div>
    <!-- flow #1 -->
    <select id="addrInput">
      <option disabled selected>Type or choose address…</option>
      <option>123 HCI Road</option>
    </select>
    <div class="btn" onclick="alert('Routing to '+addrInput.value)">Start Drive</div>
    <!-- flow #2: favorite shortcut -->
    <select id="favInput" style="margin-top:24px;">
      <option disabled selected>Favorites…</option>
      <option>Home</option>
      <option>Work</option>
    </select>
    <div class="btn" onclick="alert('Routing to favorite: '+favInput.value)">
      Go to Favorite
    </div>
  </div>
</section>

<!-- 2–4. Audio: Switch source & Adjust volume -->
<section id="Audio">
  <h1>Audio</h1>
  <div class="content" style="text-align:center">
    <div class="placeholder" style="width:180px;height:180px;margin:0 auto;border-radius:var(--r)"></div>
    <div style="margin:12px 0;font-size:17px;">Fleetwood Mac – Dreams</div>
    <!-- flow #3 -->
    <select id="source" onchange="alert('Source → '+this.value)">
      <option>Bluetooth</option>
      <option>Radio</option>
      <option>Spotify</option>
    </select>
    <!-- flow #4 -->
    <input type="range" min="0" max="100" value="50" oninput="volOut.textContent=this.value">
    <div>Volume: <span id="volOut">50</span>%</div>
  </div>
</section>

<!-- 5–6. Climate: Sync & Pre-condition -->
<section id="Climate">
  <h1>Climate</h1>
  <div class="content">
    <!-- flow #5 -->
    <div class="btn" onclick="alert('Zones synced at 72°F')">Sync Zones to 72°F</div>
    <!-- flow #6 -->
    <label>Pre-condition time</label>
    <input type="time" id="preTime" value="07:30">
    <div class="btn" onclick="alert('Pre-condition set at '+preTime.value)">
      Schedule Pre-Condition
    </div>
  </div>
</section>

<!-- 7–8. Seats & Storage: Unlock/Trunk & Camera -->
<section id="Seats">
  <h1>Seats / Storage</h1>
  <div class="content">
    <div class="placeholder" style="height:140px;border-radius:var(--r);margin-bottom:16px;line-height:140px;text-align:center">
      Car Top View
    </div>
    <!-- flow #7a -->
    <div class="btn" onclick="alert('Vehicle unlocked')">Unlock / Lock</div>
    <!-- flow #7b -->
    <div class="btn" onclick="alert('Trunk opened')">Open Trunk</div>
    <!-- flow #8 -->
    <div class="btn" onclick="alert('Showing cabin camera view')">View Cabin Camera</div>
  </div>
</section>

<!-- bottom nav -->
<nav>
  <a data-target="Drive"  class="active"><svg viewBox="0 0 24 24"><path d="M12 4L2 9l10 5 10-5-10-5z"/></svg>Drive</a>
  <a data-target="Audio"><svg viewBox="0 0 24 24"><path d="M3 22h3V2H3v20zm5 0h3V2H8v20zm5 0h3V2h-3v20z"/></svg>Audio</a>
  <a data-target="Climate"><svg viewBox="0 0 24 24"><path d="M6 22l6-20 6 20H6z"/></svg>Climate</a>
  <a data-target="Seats"><svg viewBox="0 0 24 24"><path d="M4 10h16v2H4zm0 4h16v2H4z"/></svg>Seats</a>
</nav>

<script>
  document.querySelectorAll('nav a').forEach(link=>{
    link.addEventListener('click', e=>{
      e.preventDefault();
      document.querySelectorAll('section').forEach(s=>s.classList.remove('active'));
      document.getElementById(link.dataset.target).classList.add('active');
      document.querySelectorAll('nav a').forEach(a=>a.classList.remove('active'));
      link.classList.add('active');
    });
  });
</script>
</body>
</html>
