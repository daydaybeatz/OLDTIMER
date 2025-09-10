<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>OLDTIMER · Pomodoro v0.1</title>
  <style>
    :root{
      --bg:#0f1220; --card:#171a2b; --ink:#e9ecff; --muted:#aab1d6; --accent:#8cf; --accent2:#7ef2c9;
      --danger:#ff6b6b; --ok:#21d07a; --ring:#2a2f4a;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font:16px/1.4 system-ui, -apple-system, Segoe UI, Roboto, Inter, Arial; color:var(--ink);
      background: radial-gradient(1200px 600px at 50% -10%, #1b1f35, #0f1220 60%), var(--bg);
      display:grid; place-items:center; padding:24px;
    }
    .app{
      width:min(850px, 100%); background:linear-gradient(180deg, #1a1d31, #14172a); border:1px solid #2b3050;
      border-radius:18px; box-shadow:0 10px 40px #0008, inset 0 1px 0 #fff1;
    }
    header{
      padding:20px 24px; display:flex; align-items:center; justify-content:space-between; gap:12px;
      border-bottom:1px solid #2b3050;
    }
    .title{font-weight:800; letter-spacing:.8px; display:flex; align-items:center; gap:12px}
    .title b{font-size:20px}
    .badge{font-size:12px; padding:3px 8px; border-radius:999px; background:#111632; color:#a7b7ff; border:1px solid #263164}
    .controls{display:flex; gap:10px; align-items:center; flex-wrap:wrap}
    .switch{display:inline-flex; align-items:center; gap:10px; cursor:pointer; user-select:none}
    .switch input{appearance:none; width:42px; height:24px; background:#2a2f4a; border-radius:999px; position:relative; outline:none; transition:.2s}
    .switch input:checked{background:#1f5a88}
    .switch input::after{content:""; position:absolute; top:3px; left:3px; width:18px; height:18px; border-radius:50%; background:white; transition:.2s}
    .switch input:checked::after{left:21px}
    .pill{border:1px solid #2b3050; background:#121528; color:var(--ink); border-radius:999px; padding:8px 12px; font-weight:600}
    .btn{border:1px solid #2b3050; background:#0f1329; color:var(--ink); padding:12px 14px; border-radius:12px; font-weight:700; letter-spacing:.4px; cursor:pointer; transition:transform .05s ease, background .2s, border-color .2s}
    .btn:hover{border-color:#3a4272; background:#151a36}
    .btn:active{transform:translateY(1px)}
    .btn.primary{background:linear-gradient(180deg, #1e2d6b, #15265a); border-color:#2c3f82}
    .btn.primary:hover{filter:brightness(1.05)}
    .btn.danger{background:linear-gradient(180deg, #5b1b2b, #471424); border-color:#7a2a3c}
    .stack{display:grid; gap:14px}
    main{padding:24px; display:grid; gap:24px}
    .grid{display:grid; grid-template-columns: 1.2fr .9fr; gap:24px}
    @media (max-width:900px){ .grid{grid-template-columns:1fr} }

    .card{background:var(--card); border:1px solid #2b3050; border-radius:16px; padding:20px}
    .clockWrap{display:grid; place-items:center; padding:18px}
    .mode{display:flex; gap:10px; justify-content:center; align-items:center; margin-top:8px}
    .mode .chip{padding:6px 10px; border-radius:999px; border:1px solid #334; color:#cfe1ff; background:#11162a}

    .ring{ width:280px; height:280px; border-radius:50%; display:grid; place-items:center; background:
      radial-gradient(circle at 50% 50%, #0e1122 53%, transparent 54%), 
      conic-gradient(var(--accent) 0deg, var(--ring) 0deg);
      transition: background .25s ease;
      position:relative; box-shadow:0 6px 40px #0008, inset 0 1px 0 #fff1; border:1px solid #2b3050;
    }
    .ring::before{content:""; position:absolute; inset:12px; border-radius:50%; border:1px dashed #2f3559}
    .time{font-size:58px; font-weight:900; letter-spacing:1px}
    .sub{color:var(--muted); font-size:13px; margin-top:6px}
    .actions{display:flex; gap:10px; justify-content:center; margin-top:16px; flex-wrap:wrap}

    .settings h3{margin:0 0 10px 0; font-size:14px; letter-spacing:.6px; color:#cde}
    .rows{display:grid; grid-template-columns:1fr 1fr 1fr; gap:12px}
    @media (max-width:560px){ .rows{grid-template-columns:1fr} }
    .field{background:#10142a; border:1px solid #2b3050; border-radius:12px; padding:12px}
    .label{font-size:12px; color:#ccd3ff; opacity:.9}
    .num{display:flex; align-items:center; gap:10px; margin-top:6px}
    .num input{width:100%; padding:10px 12px; font-weight:800; font-size:18px; border-radius:10px; border:1px solid #2c3256; background:#0d1130; color:#e9ecff}

    .footer{padding:12px 18px; border-top:1px solid #2b3050; color:#90a1d9; font-size:12px; display:flex; justify-content:space-between; gap:8px}
    .kbd{font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", monospace; background:#0a0d1e; border:1px solid #2b3050; border-bottom-color:#1a1e38; border-radius:6px; padding:1px 6px}
    .muted{color:#8fa0d0}
  </style>
</head>
<body>
  <div class="app" id="app">
    <header>
      <div class="title"><b>OLDTIMER</b><span class="badge">Pomodoro v0.1</span></div>
      <div class="controls">
        <label class="switch" title="Toggle auto?loop (continue automatically)">
          <input type="checkbox" id="autoLoop" />
          <span class="muted">Auto?loop</span>
        </label>
        <button class="btn pill" id="resetBtn" title="Reset (R)">Reset</button>
      </div>
    </header>

    <main>
      <section class="grid">
        <div class="card clockWrap">
          <div class="ring" id="ring">
            <div class="stack" style="place-items:center">
              <div class="time" id="time">25:00</div>
              <div class="sub" id="sub">Work · Session 1</div>
            </div>
          </div>
          <div class="mode" id="modeChips"></div>
          <div class="actions">
            <button class="btn primary" id="startPauseBtn" title="Start/Pause (Space)">Start</button>
            <button class="btn" id="skipBtn" title="Skip to next">Skip</button>
          </div>
        </div>

        <div class="card settings">
          <h3>Intervals (minutes)</h3>
          <div class="rows">
            <div class="field">
              <div class="label">Work</div>
              <div class="num"><input type="number" min="1" step="1" id="workMin"></div>
            </div>
            <div class="field">
              <div class="label">Break</div>
              <div class="num"><input type="number" min="1" step="1" id="breakMin"></div>
            </div>
            <div class="field">
              <div class="label">Long Break (every 5th break)</div>
              <div class="num"><input type="number" min="1" step="1" id="longBreakMin"></div>
            </div>
          </div>
          <div class="actions" style="justify-content:flex-start; margin-top:14px">
            <button class="btn" id="applyBtn">Apply</button>
            <span class="muted" id="savedMsg" style="opacity:.0; transition:opacity .25s">Saved ?</span>
          </div>
          <div class="muted" style="margin-top:8px; font-size:12px">
            Tip: You can change the numbers at any time; they’re saved locally and used for upcoming intervals.
          </div>
        </div>
      </section>
    </main>

    <div class="footer">
      <div class="muted">Keyboard: <span class="kbd">Space</span> start/pause · <span class="kbd">R</span> reset · <span class="kbd">L</span> auto?loop</div>
      <div class="muted">Designed for focus · No accounts · No tracking</div>
    </div>
  </div>

  <script>
  (function(){
    const $ = sel => document.querySelector(sel);

    // Elements
    const timeEl = $('#time');
    const subEl = $('#sub');
    const ringEl = $('#ring');
    const startPauseBtn = $('#startPauseBtn');
    const skipBtn = $('#skipBtn');
    const resetBtn = $('#resetBtn');
    const autoLoopEl = $('#autoLoop');
    const workMinEl = $('#workMin');
    const breakMinEl = $('#breakMin');
    const longBreakMinEl = $('#longBreakMin');
    const applyBtn = $('#applyBtn');
    const savedMsg = $('#savedMsg');
    const chips = $('#modeChips');

    // Settings (persisted)
    const STORE_KEY = 'oldt_v01_settings';
    const defaults = { work:25, break:5, longBreak:15, autoLoop:true };
    const state = {
      mode: 'work', // 'work' | 'break' | 'long'
      running: false,
      remaining: defaults.work * 60,
      sessionCount: 1, // counts work sessions completed/active label
      breakCount: 0,   // completed/started breaks; every 5th break is long
      t: null,
      settings: loadSettings()
    };

    // Init UI with settings
    function init(){
      workMinEl.value = state.settings.work;
      breakMinEl.value = state.settings.break;
      longBreakMinEl.value = state.settings.longBreak;
      autoLoopEl.checked = !!state.settings.autoLoop;
      setMode('work', /*resetSession=*/true, /*autostart=*/false);
      renderChips();
    }

    function saveSettings(){
      const next = {
        work: clampInt(workMinEl.value, 1, 999),
        break: clampInt(breakMinEl.value, 1, 999),
        longBreak: clampInt(longBreakMinEl.value, 1, 999),
        autoLoop: !!autoLoopEl.checked
      };
      state.settings = next;
      localStorage.setItem(STORE_KEY, JSON.stringify(next));
      flashSaved();
    }

    function loadSettings(){
      try{
        const raw = localStorage.getItem(STORE_KEY);
        if(!raw) return {...defaults};
        const parsed = JSON.parse(raw);
        return { ...defaults, ...parsed };
      }catch{ return {...defaults}; }
    }

    function clampInt(v, min, max){ v = Math.round(parseFloat(v) || 0); return Math.min(max, Math.max(min, v)); }

    function secondsFor(mode){
      if(mode==='work') return state.settings.work * 60;
      if(mode==='long') return state.settings.longBreak * 60;
      return state.settings.break * 60; // short break
    }

    function setMode(mode, resetSession=false, autostart=false){
      state.mode = mode;
      state.remaining = secondsFor(mode);
      if(mode==='work' && resetSession){ state.sessionCount = Math.max(1, state.sessionCount); }
      updateRing();
      updateLabels();
      if(autostart) start(); else renderStartBtn(false);
    }

    function renderChips(){
      chips.innerHTML = '';
      const mk = (text, active) => `<span class="chip" style="opacity:${active?1:.6}; border-color:${active? '#3a5896':'#334'}; background:${active? '#0f1736':'#11162a'}">${text}</span>`;
      chips.insertAdjacentHTML('beforeend', mk('Work', state.mode==='work'));
      chips.insertAdjacentHTML('beforeend', mk('Break', state.mode==='break'));
      chips.insertAdjacentHTML('beforeend', mk('Long', state.mode==='long'));
    }

    function updateLabels(){
      const m = state.mode;
      const sessionTxt = m==='work' ? `Work · Session ${state.sessionCount}`
                        : (m==='long' ? `Long Break · #${state.breakCount}` : `Break · #${state.breakCount}`);
      subEl.textContent = sessionTxt;
      renderChips();
    }

    function format(sec){
      const m = Math.floor(sec/60), s = Math.floor(sec%60);
      return `${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
    }

    function updateTime(){ timeEl.textContent = format(state.remaining); }

    function updateRing(){
      const total = secondsFor(state.mode);
      const pct = 1 - (state.remaining / total);
      const deg = Math.max(0, Math.min(360, pct * 360));
      ringEl.style.background = `radial-gradient(circle at 50% 50%, #0e1122 53%, transparent 54%), conic-gradient(var(--accent) ${deg}deg, var(--ring) ${deg}deg)`;
      updateTime();
    }

    function tick(){
      if(!state.running) return;
      state.remaining -= 1;
      if(state.remaining <= 0){
        state.remaining = 0; updateTime();
        completePhase();
        return;
      }
      updateRing();
    }

    function completePhase(){
      pause(); // stop current interval
      // Decide next mode
      let nextMode;
      if(state.mode === 'work'){
        const upcomingBreakIndex = state.breakCount + 1; // we are about to START this break
        const isLong = (upcomingBreakIndex % 5 === 0);
        state.breakCount = upcomingBreakIndex; // record it
        nextMode = isLong ? 'long' : 'break';
      }else{
        // after any break returns to work, increment session count
        state.sessionCount += 1;
        nextMode = 'work';
      }
      setMode(nextMode, /*resetSession=*/nextMode==='work', /*autostart=*/state.settings.autoLoop);
    }

    function start(){
      if(state.running) return;
      state.running = true;
      startPauseBtn.textContent = 'Pause';
      state.t = setInterval(tick, 1000);
    }

    function pause(){
      if(!state.running) return;
      state.running = false;
      startPauseBtn.textContent = 'Start';
      clearInterval(state.t); state.t = null;
    }

    function renderStartBtn(forceRunning){
      const running = forceRunning ?? state.running;
      startPauseBtn.textContent = running ? 'Pause' : 'Start';
    }

    function reset(){
      pause();
      state.sessionCount = 1;
      state.breakCount = 0;
      setMode('work', /*resetSession=*/true, /*autostart=*/false);
    }

    // --- Events ---
    startPauseBtn.addEventListener('click', ()=>{ state.running ? pause() : start(); });
    skipBtn.addEventListener('click', ()=>{ state.remaining = 0; updateRing(); completePhase(); });
    resetBtn.addEventListener('click', reset);
    autoLoopEl.addEventListener('change', ()=>{ saveSettings(); });

    applyBtn.addEventListener('click', ()=>{
      saveSettings();
      // If not running, apply immediately to remaining for current mode
      if(!state.running){
        // keep same mode, just reset remaining to new duration
        state.remaining = secondsFor(state.mode);
        updateRing(); updateLabels();
      }
    });

    // Keyboard shortcuts
    window.addEventListener('keydown', (e)=>{
      if(e.code==='Space'){ e.preventDefault(); state.running ? pause() : start(); }
      else if(e.key==='r' || e.key==='R'){ reset(); }
      else if(e.key==='l' || e.key==='L'){ autoLoopEl.checked = !autoLoopEl.checked; saveSettings(); }
    });

    // Helpers
    function flashSaved(){
      savedMsg.style.opacity = 1;
      setTimeout(()=> savedMsg.style.opacity = 0, 900);
    }

    // Boot
    init();
  })();
  </script>
</body>
</html>
