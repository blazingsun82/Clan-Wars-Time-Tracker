<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Clan War Time Advisor</title>
  <style>
    :root{
      --bg1:#0f172a; /* slate-900 */
      --bg2:#1e293b; /* slate-800 */
      --card:#0b1220cc; /* glass */
      --muted:#94a3b8; /* slate-400 */
      --text:#e2e8f0; /* slate-200 */
      --accent:#7c3aed; /* violet-600 */
      --accent2:#06b6d4; /* cyan-500 */
      --good:#22c55e; /* green-500 */
      --warn:#f59e0b; /* amber-500 */
      --bad:#ef4444; /* red-500 */
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, Noto Sans, "Helvetica Neue", Arial;
      color:var(--text);
      background: radial-gradient(1200px 600px at 20% 10%, #111827, transparent 60%),
                  radial-gradient(1000px 500px at 80% 0%, #0b1020, transparent 60%),
                  linear-gradient(160deg, var(--bg1), var(--bg2));
    }
    .wrap{max-width:1000px; margin:0 auto; padding:24px;}
    header{display:flex; align-items:center; gap:12px; margin:8px 0 20px}
    .logo{
      width:48px; height:48px; border-radius:12px;
      background: conic-gradient(from 220deg, var(--accent), var(--accent2));
      display:grid; place-items:center; color:white; font-weight:900; letter-spacing:0.5px;
      box-shadow: 0 8px 30px #7c3aed44, inset 0 0 12px #06b6d455;
    }
    h1{font-size:clamp(20px, 3.8vw, 36px); margin:0; line-height:1.1}
    .subtitle{color:var(--muted); margin-top:4px}

    .card{background:var(--card); backdrop-filter: blur(10px); border:1px solid #ffffff0f; border-radius:18px; padding:16px; box-shadow:0 10px 30px #0008}
    .grid{display:grid; gap:16px}
    @media(min-width:840px){.grid{grid-template-columns:1.2fr 1fr}}

    .controls{display:grid; gap:12px}
    .row{display:flex; gap:10px; flex-wrap:wrap}
    label{font-size:12px; color:var(--muted)}
    input, select, button{
      background:#0b1220; color:var(--text); border:1px solid #ffffff1a; border-radius:12px; padding:12px 14px; font-size:14px;
      outline:none;
    }
    input:focus, select:focus{border-color:#ffffff33}
    button{
      cursor:pointer; border:none; background:linear-gradient(135deg, var(--accent), var(--accent2)); font-weight:700;
      box-shadow:0 10px 25px #06b6d444; transition: transform .08s ease-in-out, filter .08s;
    }
    button:hover{filter:brightness(1.05)}
    button:active{transform:translateY(1px)}

    .chips{display:flex; gap:8px; flex-wrap:wrap; margin-top:8px}
    .chip{padding:6px 10px; border-radius:999px; font-size:12px; color:#0b1220; font-weight:700}
    .chip.best{background:var(--good)}
    .chip.decent{background:#34d399}
    .chip.peak{background:#818cf8}
    .chip.avoid{background:var(--bad)}

    .results{display:grid; gap:16px}
    .result-card{display:grid; gap:8px; border-left:4px solid transparent}
    .result-card.best{border-color:var(--good)}
    .result-card.decent{border-color:#34d399}
    .result-card.peak{border-color:#818cf8}
    .result-card.avoid{border-color:var(--bad)}
    .result-title{font-weight:800; letter-spacing:0.2px}
    .range{font-size:18px}
    .why{color:var(--muted); font-size:12px}

    .timeline{display:grid; grid-template-columns:repeat(24, minmax(0,1fr)); gap:2px; margin-top:8px}
    .slot{height:22px; border-radius:5px}
    .slot.best{background:linear-gradient(180deg, #16a34a, #22c55e)}
    .slot.decent{background:linear-gradient(180deg, #2dd4bf, #34d399)}
    .slot.peak{background:linear-gradient(180deg, #6366f1, #8b5cf6)}
    .slot.avoid{background:linear-gradient(180deg, #f43f5e, #ef4444)}
    .legend{display:flex; gap:12px; align-items:center; color:var(--muted); font-size:12px}
    .box{width:14px; height:14px; border-radius:4px; display:inline-block; margin-right:6px}

    footer{margin-top:18px; color:var(--muted); font-size:12px}
    .note{color:var(--muted); font-size:12px; margin-top:8px}
    .small{font-size:12px}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <div class="logo">⚔️</div>
      <div>
        <h1>Clan War Time Advisor</h1>
        <div class="subtitle">Type your city or timezone. Get peak, early-morning (quieter), and times to avoid.</div>
      </div>
    </header>

    <div class="grid">
      <section class="card controls">
        <div class="row">
          <div style="flex:1; min-width:260px">
            <label for="where">City or IANA timezone</label>
            <input id="where" list="cities" placeholder="e.g., Edmonton or America/Edmonton" />
            <datalist id="cities"></datalist>
          </div>
          <div>
            <label>&nbsp;</label>
            <button id="useDevice">Use my device timezone</button>
          </div>
        </div>
        <div class="row">
          <div>
            <label for="peakStart">Regional peak window</label><br/>
            <select id="peakStart">
              <option value="18:00">Start: 18:00</option>
              <option value="17:00">Start: 17:00</option>
              <option value="19:00">Start: 19:00</option>
            </select>
            <select id="peakEnd">
              <option value="23:00">End: 23:00</option>
              <option value="22:00">End: 22:00</option>
              <option value="00:00">End: 00:00</option>
            </select>
          </div>
          <div>
            <label for="early">Early-morning window</label><br/>
            <select id="early">
              <option value="05:00-09:00">05:00–09:00</option>
              <option value="06:00-10:00">06:00–10:00</option>
              <option value="04:00-08:00">04:00–08:00</option>
            </select>
          </div>
          <div>
            <label for="avoidThresh">Avoid threshold</label><br/>
            <select id="avoidThresh">
              <option value="3" selected>3+ regions in peak = AVOID</option>
              <option value="4">4+ regions in peak = AVOID</option>
            </select>
          </div>
          <div>
            <label for="refresh">War Deck refresh bump</label><br/>
            <select id="refresh">
              <option value="10:00">10:00 UTC (default)</option>
            </select>
            <select id="bump">
              <option value="60">±60 min</option>
              <option value="45">±45 min</option>
              <option value="30">±30 min</option>
            </select>
          </div>
        </div>
        <div class="row">
          <button id="go">Get recommendations</button>
        </div>
        <div class="note">Tip: You can type either a city name (from the list) or an IANA timezone like <code>America/Edmonton</code>. If your city isn’t listed, use the timezone format.</div>
      </section>

      <section class="card results">
        <div class="legend">
          <span><span class="box slot best"></span>Best (quiet)</span>
          <span><span class="box slot decent"></span>Decent</span>
          <span><span class="box slot peak"></span>Peak (busy)</span>
          <span><span class="box slot avoid"></span>Avoid</span>
        </div>

        <div id="peakCard" class="result-card peak" style="display:none">
          <div class="result-title">Peak time to attack (busy, fast matchmaking)</div>
          <div class="range" id="peakRange">–</div>
          <div class="why" id="peakWhy"></div>
        </div>

        <div id="earlyCard" class="result-card decent" style="display:none">
          <div class="result-title">Decent early‑morning window (quieter)</div>
          <div class="range" id="earlyRange">–</div>
          <div class="why" id="earlyWhy"></div>
        </div>

        <div id="avoidCard" class="result-card avoid" style="display:none">
          <div class="result-title">Times to avoid</div>
          <div class="range" id="avoidRange">–</div>
          <div class="why" id="avoidWhy"></div>
        </div>

        <div class="card" style="padding:12px">
          <div class="small" id="tzEcho"></div>
          <div class="timeline" id="timeline" title="Full-day timeline: 24 blocks = 1 hour each (top is midnight)"></div>
          <div class="chips" id="chips"></div>
        </div>
        <footer>
          Heuristics only: uses global evening overlaps and a UTC refresh bump. Tune settings to fit your clan.
        </footer>
      </section>
    </div>
  </div>

<script>
// ===== City → IANA timezone map (add/extend as needed) =====
const CITY_TZ = Object.fromEntries([
  // Canada
  ["edmonton","America/Edmonton"],["calgary","America/Edmonton"],["vancouver","America/Los_Angeles"],
  ["victoria","America/Vancouver"],["winnipeg","America/Winnipeg"],["regina","America/Regina"],
  ["saskatoon","America/Regina"],["toronto","America/Toronto"],["ottawa","America/Toronto"],
  ["montreal","America/Toronto"],["quebec city","America/Toronto"],["halifax","America/Halifax"],
  ["st. john's","America/St_Johns"],
  // USA
  ["los angeles","America/Los_Angeles"],["seattle","America/Los_Angeles"],["san francisco","America/Los_Angeles"],
  ["phoenix","America/Phoenix"],["denver","America/Denver"],["chicago","America/Chicago"],
  ["new york","America/New_York"],["miami","America/New_York"],["boston","America/New_York"],
  ["dallas","America/Chicago"],["atlanta","America/New_York"],
  // Latin America
  ["mexico city","America/Mexico_City"],["bogota","America/Bogota"],["lima","America/Lima"],
  ["sao paulo","America/Sao_Paulo"],["são paulo","America/Sao_Paulo"],["buenos aires","America/Argentina/Buenos_Aires"],
  // Europe
  ["london","Europe/London"],["dublin","Europe/Dublin"],["lisbon","Europe/Lisbon"],
  ["paris","Europe/Paris"],["berlin","Europe/Berlin"],["madrid","Europe/Madrid"],["rome","Europe/Rome"],
  ["amsterdam","Europe/Amsterdam"],["stockholm","Europe/Stockholm"],["warsaw","Europe/Warsaw"],
  // Africa / Middle East
  ["cairo","Africa/Cairo"],["johannesburg","Africa/Johannesburg"],["nairobi","Africa/Nairobi"],
  ["riyadh","Asia/Riyadh"],["dubai","Asia/Dubai"],
  // Asia / Oceania
  ["mumbai","Asia/Kolkata"],["delhi","Asia/Kolkata"],["karachi","Asia/Karachi"],
  ["singapore","Asia/Singapore"],["bangkok","Asia/Bangkok"],["jakarta","Asia/Jakarta"],
  ["tokyo","Asia/Tokyo"],["seoul","Asia/Seoul"],["beijing","Asia/Shanghai"],["shanghai","Asia/Shanghai"],
  ["sydney","Australia/Sydney"],["melbourne","Australia/Melbourne"],["auckland","Pacific/Auckland"]
]);

// Representative world regions to estimate global evening overlaps
const REGIONS = [
  ["USA West","America/Los_Angeles"],
  ["USA Central","America/Chicago"],
  ["USA East","America/New_York"],
  ["Mexico","America/Mexico_City"],
  ["Brazil","America/Sao_Paulo"],
  ["UK/Ireland","Europe/London"],
  ["Central Europe","Europe/Berlin"],
  ["South Africa","Africa/Johannesburg"],
  ["Middle East","Asia/Riyadh"],
  ["India","Asia/Kolkata"],
  ["Southeast Asia","Asia/Singapore"],
  ["Japan","Asia/Tokyo"],
  ["Australia East","Australia/Sydney"],
  ["New Zealand","Pacific/Auckland"],
];

// Build datalist options
(function fillCities(){
  const dl = document.getElementById('cities');
  const names = Object.keys(CITY_TZ).sort();
  names.forEach(n=>{ const opt=document.createElement('option'); opt.value=n.replace(/\b\w/g,c=>c.toUpperCase()); dl.appendChild(opt);});
})();

// Helpers: time parsing & formatting
function parseHM(s){ const [h,m]=s.split(':').map(Number); return {h, m}; }
function minutes(h,m){ return h*60 + m; }
function inWindow(h,m, sH,sM, eH,eM){
  const t = minutes(h,m), start = minutes(sH,sM), end = minutes(eH,eM);
  if(start <= end) return t>=start && t<=end; // same-day window
  return t>=start || t<=end; // crosses midnight
}
function fmt12(h,m){
  const ampm = h>=12? 'PM':'AM';
  const h12 = ((h+11)%12)+1; const mm = String(m).padStart(2,'0');
  return `${h12}:${mm} ${ampm}`;
}
function rangeFmt(h1,m1,h2,m2){
  const sameDay = (h2*60+m2) >= (h1*60+m1);
  return `${fmt12(h1,m1)} – ${fmt12(h2,m2)}${sameDay?'':' (+1)'}`;
}

// Get HH & MM in a specific timeZone for a UTC Date
function hmInTZ(dateUTC, timeZone){
  const fmt = new Intl.DateTimeFormat('en-GB', { timeZone, hourCycle:'h23', hour:'2-digit', minute:'2-digit', year:'numeric', month:'2-digit', day:'2-digit'});
  const parts = fmt.formatToParts(dateUTC);
  let h=0,m=0, day=0;
  for(const p of parts){ if(p.type==='hour') h=Number(p.value); if(p.type==='minute') m=Number(p.value); if(p.type==='day') day=Number(p.value); }
  return {h,m, day};
}

function resolveTZ(input){
  const s = (input||'').trim();
  if(!s) return null;
  const key = s.toLowerCase();
  if(CITY_TZ[key]) return CITY_TZ[key];
  // Try stripping after comma (e.g., "Edmonton, AB")
  const guess = key.split(',')[0].trim();
  if(CITY_TZ[guess]) return CITY_TZ[guess];
  // Fallback: assume user typed an IANA TZ
  return s;
}

function buildSchedule(userTZ, opts){
  const {peakStart, peakEnd, avoidThresh, refreshUTC, bumpMinutes} = opts;
  // Base = today at 00:00 UTC so DST is current
  const now = new Date();
  const base = new Date(Date.UTC(now.getUTCFullYear(), now.getUTCMonth(), now.getUTCDate(), 0,0,0));
  const stepMin = 30; const slots=[];
  const userBaseDay = hmInTZ(base, userTZ).day;

  for(let i=0;i<48;i++){
    const t = new Date(base.getTime() + i*stepMin*60000);
    const userHM = hmInTZ(t, userTZ);
    const uMins = minutes(userHM.h, userHM.m);
    const dayIndex = (userHM.day===userBaseDay)?0:1; // wrap after midnight local

    // Count regions in their evening window at this instant
    let regionsInPeak = 0;
    for(const [, tz] of REGIONS){
      const rHM = hmInTZ(t, tz);
      if(inWindow(rHM.h, rHM.m, peakStart.h, peakStart.m, peakEnd.h, peakEnd.m)) regionsInPeak++;
    }

    // Refresh bump around e.g., 10:00 UTC ± N minutes
    const utcHM = hmInTZ(t, 'UTC');
    const utcMin = minutes(utcHM.h, utcHM.m);
    const diff = Math.abs(utcMin - minutes(refreshUTC.h, refreshUTC.m));
    const wrapDiff = Math.min(diff, 1440 - diff); // handle wraparound midnight
    const isRefresh = wrapDiff <= bumpMinutes;

    let label;
    if(isRefresh || regionsInPeak >= avoidThresh){ label = 'avoid'; }
    else if(regionsInPeak >= 2){ label = 'peak'; }
    else if(regionsInPeak === 1){ label = 'decent'; }
    else { label = 'best'; }

    slots.push({
      tUTC:t, label, regionsInPeak, isRefresh,
      userHM, uMins, norm:uMins + dayIndex*1440
    });
  }

  // Sort by local day progression
  slots.sort((a,b)=>a.norm-b.norm);

  // Merge contiguous slots with same label
  const merged=[];
  for(const s of slots){
    if(!merged.length){ merged.push({start:s, end:s, label:s.label, anyRefresh:s.isRefresh}); continue;}
    const last = merged[merged.length-1];
    const contiguous = (s.norm - last.end.norm) === stepMin && s.label===last.label;
    if(contiguous){ last.end = s; last.anyRefresh = last.anyRefresh || s.isRefresh; }
    else { merged.push({start:s, end:s, label:s.label, anyRefresh:s.isRefresh}); }
  }

  return {slots, merged};
}

function intersectRange(range, winStart, winEnd){
  // Inputs are {start:{uMins}, end:{uMins}} in local minutes-of-day [0..2879]
  const a1 = range.start.uMins + (range.start.norm>=1440?1440:0);
  const a2 = range.end.uMins + (range.end.norm>=1440?1440:0);
  const b1 = winStart, b2 = winEnd; // assumed same day, no wrap
  const s = Math.max(a1,b1), e = Math.min(a2,b2);
  return (e>s) ? [s,e] : null;
}

function minsToHM(mins){ mins = ((mins%1440)+1440)%1440; return {h:Math.floor(mins/60), m:mins%60}; }

function pickPeak(merged, eveningWin){
  // Prefer longest PEAK inside evening; else longest PEAK anywhere
  const eveningRanges = [];
  for(const r of merged){ if(r.label!=='peak') continue; const inter = intersectRange(r, eveningWin[0], eveningWin[1]); if(inter){ eveningRanges.push({len:inter[1]-inter[0], inter}); } }
  const allRanges = merged.filter(r=>r.label==='peak').map(r=>({len:r.end.norm - r.start.norm, range:r}));
  if(eveningRanges.length){ eveningRanges.sort((a,b)=>b.len-a.len); const [s,e]=eveningRanges[0].inter; return [minsToHM(s), minsToHM(e)]; }
  if(allRanges.length){ allRanges.sort((a,b)=>b.len-a.len); const r=allRanges[0].range; return [r.start.userHM, r.end.userHM]; }
  return null;
}

function pickEarly(merged, earlyWin){
  // Choose longest BEST or DECENT overlapping early window
  const candidates = [];
  for(const r of merged){ if(r.label==='avoid') continue; const inter = intersectRange(r, earlyWin[0], earlyWin[1]); if(!inter) continue; const len=inter[1]-inter[0]; const labelScore = (r.label==='best')?2:1; candidates.push({len:len+labelScore, inter}); }
  if(candidates.length){ candidates.sort((a,b)=>b.len-a.len); const [s,e]=candidates[0].inter; return [minsToHM(s), minsToHM(e)]; }
  // fallback: longest BEST anywhere
  const bests = merged.filter(r=>r.label==='best').map(r=>({len:r.end.norm-r.start.norm, r})).sort((a,b)=>b.len-a.len);
  if(bests.length){ const r=bests[0].r; return [r.start.userHM, r.end.userHM]; }
  return null;
}

function listAvoid(merged){
  const out=[];
  for(const r of merged){ if(r.label!=='avoid') continue; out.push({
    start:r.start.userHM, end:r.end.userHM, refresh:r.anyRefresh
  }); }
  return out;
}

function renderTimeline(userTZ, slots){
  const tl = document.getElementById('timeline'); tl.innerHTML='';
  // 24 blocks (each = 1 hour) made of two 30-min slots
  for(let h=0;h<24;h++){
    const bucket = slots.filter(s=> s.start===undefined && false); // noop to hint structure
  }
  // Simple approach: 24 slots each 60m using the majority label of the two half hours
  for(let hour=0; hour<24; hour++){
    // find two slots closest to this hour start
    // slots is sorted; find ones where local hour equals hour within same day index 0
    let chosen = null; let bestDiff = 9999;
    for(const s of slots){
      const totalMin = s.uMins + (s.norm>=1440?1440:0);
      const target = hour*60;
      const d = Math.abs(totalMin - target);
      if(d < bestDiff){ bestDiff=d; chosen=s; }
    }
    const div = document.createElement('div');
    div.className = `slot ${chosen.label}`; div.title = `${String(hour).padStart(2,'0')}:00`;
    tl.appendChild(div);
  }
}

function showChips(merged){
  const box = document.getElementById('chips'); box.innerHTML='';
  const order = {best:1, decent:2, peak:3, avoid:4};
  merged.slice().sort((a,b)=>order[a.label]-order[b.label]).forEach(r=>{
    const chip = document.createElement('span');
    chip.className = `chip ${r.label}`;
    chip.textContent = `${r.label.toUpperCase()} ${rangeFmt(r.start.userHM.h, r.start.userHM.m, r.end.userHM.h, r.end.userHM.m)}`;
    box.appendChild(chip);
  });
}

function run(){
  const input = document.getElementById('where').value.trim();
  const tz = resolveTZ(input);
  if(!tz){ alert('Please enter a city or IANA timezone.'); return; }

  const peakStart = parseHM(document.getElementById('peakStart').value);
  const peakEnd = parseHM(document.getElementById('peakEnd').value);
  const avoidThresh = Number(document.getElementById('avoidThresh').value);
  const [eS,eE] = document.getElementById('early').value.split('-');
  const earlyStart = parseHM(eS), earlyEnd = parseHM(eE);
  const refreshUTC = parseHM(document.getElementById('refresh').value);
  const bumpMinutes = Number(document.getElementById('bump').value);

  let schedule;
  try{
    schedule = buildSchedule(tz, {peakStart, peakEnd, avoidThresh, refreshUTC, bumpMinutes});
  }catch(err){
    alert('Time zone not recognized. Try an IANA name like "America/Edmonton".');
    return;
  }

  const {slots, merged} = schedule;

  // Pick recommendations
  const eveningWin = [minutes(17,0), minutes(22,30)];
  const earlyWin = [minutes(earlyStart.h, earlyStart.m), minutes(earlyEnd.h, earlyEnd.m)];
  const peakPick = pickPeak(merged, eveningWin);
  const earlyPick = pickEarly(merged, earlyWin);
  const avoids = listAvoid(merged);

  const tzEcho = document.getElementById('tzEcho');
  tzEcho.textContent = `Using time zone: ${tz}`;

  // Render cards
  const peakCard = document.getElementById('peakCard');
  const peakRange = document.getElementById('peakRange');
  const peakWhy = document.getElementById('peakWhy');
  if(peakPick){
    peakCard.style.display='grid';
    peakRange.textContent = rangeFmt(peakPick[0].h, peakPick[0].m, peakPick[1].h, peakPick[1].m);
    peakWhy.textContent = 'Chosen where multiple world regions are in their evening (busy but not overloaded).';
  } else { peakCard.style.display='none'; }

  const earlyCard = document.getElementById('earlyCard');
  const earlyRange = document.getElementById('earlyRange');
  const earlyWhy = document.getElementById('earlyWhy');
  if(earlyPick){
    earlyCard.style.display='grid';
    earlyRange.textContent = rangeFmt(earlyPick[0].h, earlyPick[0].m, earlyPick[1].h, earlyPick[1].m);
    earlyWhy.textContent = 'Lower overlap with global peaks; good if you want calmer matchups.';
  } else { earlyCard.style.display='none'; }

  const avoidCard = document.getElementById('avoidCard');
  const avoidRange = document.getElementById('avoidRange');
  const avoidWhy = document.getElementById('avoidWhy');
  if(avoids.length){
    avoidCard.style.display='grid';
    const parts = avoids.map(a=> rangeFmt(a.start.h, a.start.m, a.end.h, a.end.m) + (a.refresh?' (deck refresh impact)':''));
    avoidRange.textContent = parts.join('  ·  ');
    avoidWhy.textContent = 'Avoid when many regions peak at once or right around the deck refresh (10:00 UTC).';
  } else { avoidCard.style.display='none'; }

  // Render timeline & chips
  renderTimeline(tz, slots);
  showChips(merged);
}

// UI hooks
 document.getElementById('go').addEventListener('click', run);
 document.getElementById('useDevice').addEventListener('click', ()=>{
   const tz = Intl.DateTimeFormat().resolvedOptions().timeZone || '';
   document.getElementById('where').value = tz;
 });

</script>
</body>
</html>
