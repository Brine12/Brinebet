# Brinebet
Betting site
brinebet/
│
├── public/
│   ├── index.html
│   ├── styles.css
│   ├── script.js
│   └── assets/logo.svg
│
├── server/
│   ├── app.js
│   └── data.json
│
├── package.json
├── README.md
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>BrineBet — Live Sports Betting</title>
  <meta name="description" content="BrineBet — modern, responsive sports betting demo. Brand and extend as needed." />
  <style>
    /* =========== Brand variables =========== */
    :root{
      --brand: #0b6efd;        /* primary brand color (change me) */
      --brand-dark: #0749b6;
      --accent: #00c853;
      --bg: #0f1724;
      --card: #0b1220;
      --muted: #9aa7bf;
      --glass: rgba(255,255,255,0.03);
      --radius: 12px;
      --max-width: 1100px;
      --font-sans: "Inter", system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
    }

    /* Reset / base */
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family:var(--font-sans);
      background: linear-gradient(180deg,#071028 0%, #07132a 60%),
                  radial-gradient(800px 300px at 10% 10%, rgba(11,110,253,0.05), transparent 10%),
                  var(--bg);
      color: #e6eef8;
      -webkit-font-smoothing:antialiased;
      -moz-osx-font-smoothing:grayscale;
      padding:24px;
      display:flex;
      justify-content:center;
    }

    /* container */
    .wrap{
      width:100%;
      max-width:var(--max-width);
      display:grid;
      grid-template-columns: 1fr 360px;
      gap:20px;
      align-items:start;
    }

    /* header */
    .header{
      grid-column: 1 / -1;
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:16px;
      margin-bottom:6px;
    }
    .brand{
      display:flex;
      gap:12px;
      align-items:center;
    }
    .logo{
      width:52px;
      height:52px;
      border-radius:10px;
      background: linear-gradient(135deg,var(--brand),var(--brand-dark));
      display:flex;
      align-items:center;
      justify-content:center;
      font-weight:700;
      font-size:18px;
      color:white;
      box-shadow: 0 6px 18px rgba(11,110,253,0.12);
    }
    .brand h1{
      margin:0;
      font-size:18px;
      letter-spacing:0.2px;
    }
    .brand p{margin:0; font-size:12px; color:var(--muted)}

    /* top utilities */
    .top-actions{display:flex; gap:8px; align-items:center;}
    .btn{
      background:var(--brand);
      border:none; color:white; padding:10px 14px;
      border-radius:10px; cursor:pointer;
      font-weight:600;
      box-shadow: 0 6px 18px rgba(11,110,253,0.12);
    }
    .btn.secondary{
      background:transparent; border:1px solid rgba(255,255,255,0.06);
      color:var(--muted); font-weight:600;
    }

    /* main content */
    .main{
      background: linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
      border-radius:var(--radius);
      padding:16px;
      box-shadow: 0 8px 30px rgba(2,6,23,0.6);
      min-height:420px;
    }
    .tabs{display:flex; gap:8px; margin-bottom:12px; flex-wrap:wrap}
    .tab{
      padding:8px 14px; border-radius:10px; font-weight:600; font-size:13px;
      color:var(--muted); background:var(--glass); cursor:pointer;
    }
    .tab.active{background:linear-gradient(90deg,var(--brand),var(--brand-dark)); color:white}

    /* fixtures / card list */
    .fixtures{display:grid; gap:12px}
    .match{
      display:flex; gap:12px; align-items:center; justify-content:space-between;
      padding:12px; border-radius:10px; background:linear-gradient(180deg, rgba(255,255,255,0.01), transparent);
      border:1px solid rgba(255,255,255,0.03);
    }
    .match-left{display:flex; gap:12px; align-items:center}
    .team{min-width:110px}
    .team h4{ margin:0; font-size:15px}
    .team p{margin:0; font-size:12px; color:var(--muted)}
    .odds{display:flex; gap:8px; align-items:center}
    .odd{
      padding:8px 10px; border-radius:8px; min-width:78px; text-align:center;
      cursor:pointer; background:rgba(255,255,255,0.02); border:1px solid rgba(255,255,255,0.03);
      font-weight:700;
    }
    .odd:hover{transform:translateY(-3px)}
    .odd.selected{background:linear-gradient(90deg,var(--accent),#00e676); color:#001b08; box-shadow:0 10px 24px rgba(0,200,83,0.12);}

    .meta{font-size:12px; color:var(--muted)}

    /* sidebar / bet slip */
    .sidebar{
      background: linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
      border-radius:var(--radius);
      padding:16px;
      min-height:420px;
      border:1px solid rgba(255,255,255,0.03);
    }
    .bets-title{display:flex; justify-content:space-between; align-items:center; gap:8px}
    .bet-list{margin-top:12px; display:flex; flex-direction:column; gap:10px; max-height:320px; overflow:auto}
    .bet-item{
      display:flex; gap:10px; align-items:center; justify-content:space-between;
      padding:10px; background:rgba(255,255,255,0.01); border-radius:10px;
      border:1px solid rgba(255,255,255,0.02);
    }
    .bet-item .info{flex:1}
    .bet-item .remove{background:transparent;border:none;color:var(--muted);cursor:pointer;font-weight:700}

    .stake-row{display:flex; gap:8px; margin-top:12px; align-items:center}
    .input{
      padding:8px 10px; border-radius:8px; border:1px solid rgba(255,255,255,0.04);
      background:transparent; color: #e6eef8; width:100%;
    }
    .summary{margin-top:12px; padding:12px; border-radius:8px; background: rgba(0,0,0,0.25); font-weight:600}
    .summary small{display:block; font-weight:500; color:var(--muted); margin-top:6px}
    .place{
      width:100%; margin-top:12px; padding:12px; border-radius:10px;
      border:none; font-weight:800; letter-spacing:0.6px; cursor:pointer;
      background:linear-gradient(90deg,var(--brand),var(--brand-dark));
      color:white;
    }

    footer{grid-column:1/-1; margin-top:6px; display:flex; justify-content:space-between; color:var(--muted); font-size:13px}

    /* responsiveness */
    @media (max-width:1000px){
      .wrap{grid-template-columns:1fr 320px}
    }
    @media (max-width:880px){
      .wrap{grid-template-columns:1fr; padding:0}
      .sidebar{order:2}
      .main{order:1}
      footer{flex-direction:column; gap:8px}
    }
  </style>
</head>
<body>
  <div class="wrap" role="main">
    <header class="header" aria-label="Top">
      <div class="brand" aria-hidden="false">
        <div class="logo" aria-hidden="true">BB</div>
        <div>
          <h1>BrineBet</h1>
          <p>Live odds · Safe demo · Brandable template</p>
        </div>
      </div>
      <div class="top-actions">
        <button class="btn secondary" onclick="alert('Demo — sign in not implemented')">Sign in</button>
        <button class="btn" id="depositBtn">Deposit</button>
      </div>
    </header>

    <!-- Main content -->
    <section class="main" aria-labelledby="upcoming-title">
      <div>
        <div class="tabs" role="tablist" aria-label="Sports tabs">
          <div class="tab active" role="tab">Football</div>
          <div class="tab" role="tab">Basketball</div>
          <div class="tab" role="tab">Tennis</div>
          <div class="tab" role="tab">Esports</div>
        </div>
        <div class="meta">Odds update simulation · Client side demo only</div>
      </div>

      <div class="fixtures" id="fixtures" style="margin-top:12px" aria-live="polite">
        <!-- Matches are injected by script -->
      </div>
    </section>

    <!-- Sidebar: Bet slip -->
    <aside class="sidebar" aria-labelledby="bets-title">
      <div class="bets-title">
        <h3 id="bets-title">Bet Slip</h3>
        <button class="btn secondary" id="clearBets">Clear</button>
      </div>

      <div class="bet-list" id="betList" aria-live="polite" aria-atomic="true">
        <div style="color:var(--muted); font-size:13px; padding-top:10px">No selections yet — click odds to add.</div>
      </div>

      <div class="stake-row">
        <label for="stake" style="min-width:64px">Stake</label>
        <input id="stake" class="input" type="number" min="0" value="10" aria-label="Stake amount" />
      </div>

      <div class="summary" id="summary">
        Total Odds: <span id="totalOdds">0.00</span>
        <small>Potential Return: <span id="potential">0.00</span></small>
      </div>

      <button class="place" id="placeBet">Place Bet (Demo)</button>

      <div style="margin-top:12px; font-size:12px; color:var(--muted)">
        This is a demo template. For a production wagering product you must integrate secure user accounts, age & jurisdiction checks, licensed transaction processors, and responsible gambling controls.
      </div>
    </aside>

    <footer>
      <div>© <strong>BrineBet</strong> <span id="year"></span></div>
      <div>Need help? <a href="#" style="color:var(--muted)">support@brinebet.example</a></div>
    </footer>
  </div>

  <script>
    /* ===== Demo data =====
       Replace this array with server-provided live data in production.
    */
    const sampleMatches = [
      {
        id: 'm1',
        league: 'Premier League',
        teams: ['Arsenal', 'Liverpool'],
        time: 'Sep 14 · 14:30',
        markets: [
          {key:'1', label:'1', odd: 2.20},
          {key:'X', label:'Draw', odd: 3.60},
          {key:'2', label:'2', odd: 3.00}
        ]
      },
      {
        id: 'm2',
        league: 'La Liga',
        teams: ['Real Madrid', 'Barcelona'],
        time: 'Sep 14 · 17:00',
        markets: [
          {key:'1', label:'1', odd: 2.40},
          {key:'X', label:'Draw', odd: 3.30},
          {key:'2', label:'2', odd: 2.85}
        ]
      },
      {
        id: 'm3',
        league: 'NBA',
        teams: ['Lakers', 'Celtics'],
        time: 'Sep 15 · 20:00',
        markets: [
          {key:'1', label:'LAL', odd: 1.95},
          {key:'2', label:'BOS', odd: 1.95}
        ]
      },
      {
        id: 'm4',
        league: 'ATP',
        teams: ['Djokovic', 'Alcaraz'],
        time: 'Sep 16 · 12:00',
        markets: [
          {key:'1', label:'Djokovic', odd: 1.80},
          {key:'2', label:'Alcaraz', odd: 2.05}
        ]
      }
    ];

    // Simple DOM helpers
    const fixturesEl = document.getElementById('fixtures');
    const betListEl = document.getElementById('betList');
    const totalOddsEl = document.getElementById('totalOdds');
    const potentialEl = document.getElementById('potential');
    const stakeEl = document.getElementById('stake');
    const clearBtn = document.getElementById('clearBets');
    const placeBtn = document.getElementById('placeBet');
    const yearEl = document.getElementById('year');

    // Application state
    let selections = []; // {matchId, teams, marketKey, label, odd}

    // Render matches
    function renderFixtures(){
      fixturesEl.innerHTML = '';
      sampleMatches.forEach(match => {
        const m = document.createElement('div');
        m.className = 'match';
        m.setAttribute('data-id', match.id);

        const left = document.createElement('div');
        left.className = 'match-left';
        left.innerHTML = `
          <div style="min-width:8px; height:40px; border-radius:8px; background:linear-gradient(90deg,var(--brand),var(--brand-dark));"></div>
          <div>
            <div style="display:flex; gap:12px; align-items:center">
              <div class="team">
                <h4>${match.teams[0]}</h4>
                <p class="meta">${match.league}</p>
              </div>
              <div style="font-size:12px; color:var(--muted)">vs</div>
              <div class="team">
                <h4>${match.teams[1]}</h4>
                <p class="meta">${match.time}</p>
              </div>
            </div>
          </div>
        `;

        const oddsWrap = document.createElement('div');
        oddsWrap.className = 'odds';
        match.markets.forEach(mkt => {
          const b = document.createElement('button');
          b.className = 'odd';
          b.innerText = `${mkt.label}\n${mkt.odd.toFixed(2)}`;
          b.title = `Select ${mkt.label} (${mkt.odd.toFixed(2)})`;
          b.onclick = () => toggleSelection(match, mkt, b);
          oddsWrap.appendChild(b);
        });

        m.appendChild(left);
        m.appendChild(oddsWrap);
        fixturesEl.appendChild(m);
      });
    }

    // Toggle selection in bet slip
    function toggleSelection(match, market, btnEl){
      const key = `${match.id}|${market.key}`;
      const idx = selections.findIndex(s => s.key === key);
      if (idx === -1){
        // add
        const sel = {
          key,
          matchId: match.id,
          teams: match.teams.join(' vs '),
          league: match.league,
          label: market.label,
          odd: market.odd
        };
        selections.push(sel);
        btnEl.classList.add('selected');
      } else {
        // remove
        selections.splice(idx,1);
        btnEl.classList.remove('selected');
      }
      renderBetList();
      recalc();
    }

    // Render bet list (sidebar)
    function renderBetList(){
      betListEl.innerHTML = '';
      if (selections.length === 0){
        betListEl.innerHTML = '<div style="color:var(--muted); font-size:13px; padding-top:10px">No selections yet — click odds to add.</div>';
        return;
      }
      selections.forEach((s, i) => {
        const item = document.createElement('div');
        item.className = 'bet-item';
        item.innerHTML = `
          <div class="info">
            <div style="font-size:13px">${s.teams}</div>
            <div style="font-size:12px; color:var(--muted)">${s.label} · ${s.league}</div>
          </div>
          <div style="text-align:right; min-width:80px">
            <div style="font-weight:800">${s.odd.toFixed(2)}</div>
            <button class="remove" title="Remove" aria-label="Remove selection" data-idx="${i}">✕</button>
          </div>
        `;
        betListEl.appendChild(item);
      });

      // attach remove handlers
      betListEl.querySelectorAll('.remove').forEach(btn=>{
        btn.onclick = (e) => {
          const idx = Number(btn.dataset.idx);
          // Unselect in main list
          const sel = selections[idx];
          const [matchId, marketKey] = sel.key.split('|');
          const matchEl = document.querySelector(`.match[data-id="${matchId}"]`);
          if (matchEl){
            matchEl.querySelectorAll('.odd').forEach(od=>{
              if (od.textContent.includes(sel.odd.toFixed(2))){
                od.classList.remove('selected');
              }
            });
          }
          selections.splice(idx,1);
          renderBetList(); recalc();
        }
      });
    }

    // recalc totals
    function recalc(){
      const totalOdds = selections.reduce((acc,s)=>acc * s.odd, 1);
      const stake = Number(stakeEl.value) || 0;
      const potential = (selections.length > 0) ? (totalOdds * stake) : 0;
      totalOddsEl.innerText = selections.length ? totalOdds.toFixed(2) : '0.00';
      potentialEl.innerText = selections.length ? potential.toFixed(2) : '0.00';
    }

    // handlers
    stakeEl.addEventListener('input', recalc);
    clearBtn.addEventListener('click', ()=>{
      selections = [];
      document.querySelectorAll('.odd.selected').forEach(b=>b.classList.remove('selected'));
      renderBetList();
      recalc();
    });
    placeBtn.addEventListener('click', ()=>{
      if (selections.length === 0){
        alert('Add at least one selection to place a bet.');
        return;
      }
      const stake = Number(stakeEl.value) || 0;
      if (stake <= 0) { alert('Enter a stake greater than 0.'); return; }
      // Demo: show confirmation summary
      const summary = selections.map(s=>`${s.teams} → ${s.label} @ ${s.odd.toFixed(2)}`).join('\n');
      const totalOdds = selections.reduce((acc,s)=>acc * s.odd, 1);
      const potential = (totalOdds * stake).toFixed(2);
      alert(`Demo Bet Placed (not real):\n\n${summary}\n\nStake: ${stake}\nTotal Odds: ${totalOdds.toFixed(2)}\nPotential Return: ${potential}`);
      // reset demo
      selections = [];
      document.querySelectorAll('.odd.selected').forEach(b=>b.classList.remove('selected'));
      renderBetList(); recalc();
    });

    document.getElementById('depositBtn').addEventListener('click', ()=> alert('Demo: deposit flow not implemented.'));

    // Initialize
    renderFixtures();
    renderBetList();
    recalc();
    yearEl.innerText = new Date().getFullYear();

    // Accessibility: keyboard support for odds (Enter / Space)
    document.addEventListener('keydown', (e) => {
      if (e.key === 'Enter' || e.key === ' '){
        const el = document.activeElement;
        if (el && el.classList.contains('odd')){
          el.click();
          e.preventDefault();
        }
      }
    });

    // FUTURE: integrate real-time odds via WebSocket / API and server-side account handling.
  </script>
</body>
</html>
