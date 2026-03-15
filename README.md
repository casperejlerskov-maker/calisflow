<!DOCTYPE html>

<html lang="da">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<title>CalisFlow</title>

<!-- iOS hjemmeskærm / PWA -->

<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="CalisFlow">
<meta name="mobile-web-app-capable" content="yes">
<meta name="theme-color" content="#0a0a0f">

<!-- App-ikon genereret som inline SVG → data URL -->

<link rel="apple-touch-icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Crect width='512' height='512' rx='112' fill='%230a0a0f'/%3E%3Ctext x='256' y='200' font-family='Arial Black,sans-serif' font-size='210' font-weight='900' fill='%23c8f135' text-anchor='middle' dominant-baseline='middle'%3ECALIS%3C/text%3E%3Ctext x='256' y='370' font-family='Arial Black,sans-serif' font-size='210' font-weight='900' fill='%23ffffff' text-anchor='middle' dominant-baseline='middle'%3EFLOW%3C/text%3E%3C/svg%3E">

<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
:root {
  --bg:#0a0a0f; --surface:#13131c; --card:#1a1a28; --card2:#1f1f30;
  --accent:#c8f135; --accent2:#7b61ff; --text:#f0f0f5; --muted:#8888a8;
  --border:rgba(255,255,255,0.07); --r:16px;
}
*{margin:0;padding:0;box-sizing:border-box;}
body{background:var(--bg);color:var(--text);font-family:'DM Sans',sans-serif;min-height:100vh;display:flex;justify-content:center;overflow-x:hidden;}
body::before{content:'';position:fixed;inset:0;background:radial-gradient(ellipse 80% 50% at 50% -10%,rgba(123,97,255,0.12),transparent);pointer-events:none;z-index:0;}
.app{width:100%;max-width:480px;min-height:100vh;display:flex;flex-direction:column;position:relative;z-index:1;padding-bottom:90px;}

.header{padding:max(22px,env(safe-area-inset-top)) 20px 14px;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;background:rgba(10,10,15,0.95);backdrop-filter:blur(16px);z-index:10;border-bottom:1px solid var(–border);}
.nav{padding-bottom:max(0px,env(safe-area-inset-bottom));}
.logo{font-family:‘Bebas Neue’,sans-serif;font-size:26px;letter-spacing:3px;color:var(–accent);}
.streak-badge{display:flex;align-items:center;gap:6px;background:var(–card);border:1px solid var(–border);border-radius:100px;padding:6px 14px;font-size:14px;font-weight:600;}

.view{display:none;flex-direction:column;animation:fadeUp .35s ease;}
.view.active{display:flex;}
@keyframes fadeUp{from{opacity:0;transform:translateY(14px)}to{opacity:1;transform:translateY(0)}}

.nav{position:fixed;bottom:0;left:50%;transform:translateX(-50%);width:100%;max-width:480px;display:flex;background:rgba(10,10,15,0.97);backdrop-filter:blur(20px);border-top:1px solid var(–border);z-index:20;padding-bottom:env(safe-area-inset-bottom,0px);}
.nav-btn{flex:1;display:flex;flex-direction:column;align-items:center;gap:4px;padding:11px 0 15px;background:none;border:none;color:var(–muted);font-family:‘DM Sans’,sans-serif;font-size:10px;font-weight:600;cursor:pointer;letter-spacing:.8px;transition:color .2s;}
.nav-btn .ni{font-size:20px;}
.nav-btn.active{color:var(–accent);}

.content{padding:20px 20px 0;}
.label{font-size:10px;font-weight:600;letter-spacing:3px;text-transform:uppercase;color:var(–accent);margin-bottom:8px;}
h1{font-family:‘Bebas Neue’,sans-serif;font-size:clamp(36px,9vw,52px);line-height:1;letter-spacing:1px;margin-bottom:6px;}
h1 span{color:var(–accent);}
.sub{font-size:14px;color:var(–muted);line-height:1.5;margin-bottom:22px;}

.card{background:var(–card);border:1px solid var(–border);border-radius:var(–r);padding:20px;margin-bottom:12px;}
.card-title{font-family:‘Bebas Neue’,sans-serif;font-size:17px;letter-spacing:1px;color:var(–accent);margin-bottom:14px;}

/* ── EXERCISE IMAGE BANNER ── */
.ex-img-wrap{
width:100%;
height:220px;
border-radius:18px;
overflow:hidden;
margin-bottom:20px;
position:relative;
background:var(–surface);
border:1px solid var(–border);
}
.ex-img-wrap img{
width:100%;
height:100%;
object-fit:cover;
object-position:center top;
display:block;
transition:opacity .4s;
}
.ex-img-wrap .img-fallback{
position:absolute;
inset:0;
display:flex;
flex-direction:column;
align-items:center;
justify-content:center;
gap:10px;
font-size:56px;
}
.ex-img-wrap .img-fallback span{
font-size:13px;
color:var(–muted);
font-weight:500;
}
.ex-img-label{
position:absolute;
bottom:0; left:0; right:0;
padding:28px 14px 12px;
background:linear-gradient(to top,rgba(10,10,15,0.85),transparent);
font-size:11px;
color:rgba(255,255,255,0.5);
font-style:italic;
}

/* ── TODAY HERO ── */
.today-hero{background:var(–card);border:1px solid var(–border);border-radius:22px;padding:22px;margin-bottom:12px;position:relative;overflow:hidden;}
.today-hero::before{content:’’;position:absolute;top:-40px;right:-40px;width:200px;height:200px;background:radial-gradient(circle,rgba(200,241,53,0.07),transparent 70%);pointer-events:none;}
.today-day{font-size:11px;font-weight:600;letter-spacing:3px;text-transform:uppercase;color:var(–accent);margin-bottom:5px;}
.today-name{font-family:‘Bebas Neue’,sans-serif;font-size:42px;line-height:1;letter-spacing:1px;margin-bottom:8px;}
.today-tip{font-size:14px;color:var(–muted);margin-bottom:18px;line-height:1.5;}
.today-meta{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:18px;}
.chip{background:var(–surface);border-radius:100px;padding:5px 12px;font-size:12px;font-weight:500;color:var(–muted);display:flex;align-items:center;gap:5px;}
.chip b{color:var(–text);}

/* ── SET ROWS ── */
.set-rows{display:flex;flex-direction:column;gap:7px;margin-bottom:20px;}
.set-row{display:flex;align-items:center;gap:12px;padding:13px 15px;background:var(–surface);border-radius:11px;border:1px solid var(–border);cursor:pointer;transition:all .2s;user-select:none;}
.set-row.done{border-color:rgba(200,241,53,0.35);background:rgba(200,241,53,0.05);}
.set-num{width:28px;height:28px;border-radius:7px;background:var(–card2);display:flex;align-items:center;justify-content:center;font-size:12px;font-weight:700;color:var(–muted);flex-shrink:0;transition:all .2s;}
.set-row.done .set-num{background:var(–accent);color:#0a0a0f;}
.set-info{flex:1;}
.set-name{font-size:14px;font-weight:600;}
.set-detail{font-size:12px;color:var(–muted);margin-top:1px;}
.set-check{width:24px;height:24px;border-radius:50%;border:2px solid var(–border);display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;color:transparent;transition:all .2s;flex-shrink:0;}
.set-row.done .set-check{background:var(–accent);border-color:var(–accent);color:#0a0a0f;}

.done-btn{width:100%;background:var(–accent);color:#0a0a0f;border:none;border-radius:13px;padding:17px;font-family:‘Bebas Neue’,sans-serif;font-size:20px;letter-spacing:2px;cursor:pointer;transition:all .2s;}
.done-btn:hover{transform:translateY(-2px);box-shadow:0 12px 40px rgba(200,241,53,0.3);}
.done-btn:disabled{opacity:.3;cursor:not-allowed;transform:none;box-shadow:none;}
.done-btn.already{background:var(–card);color:var(–accent);border:2px solid rgba(200,241,53,0.3);cursor:default;transform:none;box-shadow:none;}

.rest-card{background:var(–card);border:1px solid var(–border);border-radius:22px;padding:44px 22px;text-align:center;margin-bottom:12px;}
.rest-emoji{font-size:64px;margin-bottom:16px;}
.rest-card h2{font-family:‘Bebas Neue’,sans-serif;font-size:36px;letter-spacing:1px;margin-bottom:8px;}
.rest-card p{font-size:14px;color:var(–muted);line-height:1.6;}

.sg{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:12px;}
.sb{background:var(–card);border:1px solid var(–border);border-radius:13px;padding:16px;}
.sv{font-family:‘Bebas Neue’,sans-serif;font-size:36px;line-height:1;color:var(–accent);}
.sl{font-size:11px;color:var(–muted);margin-top:3px;}

.wg{display:grid;grid-template-columns:repeat(7,1fr);gap:5px;}
.wd{display:flex;flex-direction:column;align-items:center;gap:4px;}
.wdn{font-size:9px;font-weight:600;color:var(–muted);text-transform:uppercase;letter-spacing:.5px;}
.wdd{width:36px;height:36px;border-radius:9px;background:var(–card);border:1px solid var(–border);display:flex;align-items:center;justify-content:center;font-size:14px;}
.wdd.done{background:rgba(200,241,53,0.13);border-color:rgba(200,241,53,0.4);}
.wdd.today{border-color:var(–accent);}

.ml-wrap{position:relative;padding-left:0;}
.ml-line{position:absolute;left:19px;top:0;bottom:0;width:2px;background:var(–border);}
.ml-fill{position:absolute;left:19px;top:0;width:2px;background:var(–accent);transition:height .7s ease;}
.ml{display:flex;align-items:flex-start;gap:14px;padding:0 0 20px;position:relative;}
.ml-dot{width:40px;height:40px;border-radius:11px;background:var(–card2);border:2px solid var(–border);display:flex;align-items:center;justify-content:center;font-size:18px;flex-shrink:0;transition:all .3s;position:relative;z-index:1;}
.ml.reached .ml-dot{background:rgba(200,241,53,0.13);border-color:var(–accent);}
.ml.current .ml-dot{background:var(–accent);border-color:var(–accent);}
.ml-body{padding-top:8px;}
.ml-title{font-weight:600;font-size:14px;margin-bottom:2px;}
.ml-sub{font-size:12px;color:var(–muted);}
.ml-badge{display:inline-block;margin-top:6px;background:rgba(200,241,53,0.1);border:1px solid rgba(200,241,53,0.25);color:var(–accent);border-radius:100px;padding:3px 10px;font-size:11px;font-weight:600;}

.phase-block{margin-bottom:20px;}
.phase-title{font-family:‘Bebas Neue’,sans-serif;font-size:20px;letter-spacing:1px;padding:0 20px 10px;display:flex;align-items:center;gap:10px;}
.phase-sub{font-size:10px;font-weight:600;letter-spacing:2px;color:var(–muted);font-family:‘DM Sans’,sans-serif;}
.plan-row{display:flex;align-items:center;gap:12px;padding:11px 20px;border-bottom:1px solid var(–border);transition:background .15s;cursor:pointer;}
.plan-row:hover{background:rgba(255,255,255,0.02);}
.plan-row.is-today{background:rgba(200,241,53,0.04);}
.pnum{width:30px;height:30px;border-radius:8px;background:var(–card);display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;color:var(–muted);flex-shrink:0;}
.plan-row.pdone .pnum{background:rgba(200,241,53,0.13);color:var(–accent);}
.plan-row.is-today .pnum{background:var(–accent);color:#0a0a0f;}
.pinfo{flex:1;}
.pname{font-size:13px;font-weight:600;}
.psub{font-size:11px;color:var(–muted);margin-top:1px;}
.pstat{font-size:16px;}

/* plan row thumbnail */
.p-thumb{width:38px;height:38px;border-radius:8px;overflow:hidden;flex-shrink:0;background:var(–surface);display:flex;align-items:center;justify-content:center;font-size:18px;}
.p-thumb img{width:100%;height:100%;object-fit:cover;}

/* EXERCISE MODAL */
.modal-overlay{position:fixed;inset:0;background:rgba(5,5,10,0.92);z-index:50;display:flex;align-items:flex-end;justify-content:center;animation:fadeIn .25s ease;}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
.modal{background:var(–card);border-radius:24px 24px 0 0;padding:28px 22px 40px;width:100%;max-width:480px;max-height:90vh;overflow-y:auto;animation:slideUp .3s ease;}
@keyframes slideUp{from{transform:translateY(40px);opacity:0}to{transform:translateY(0);opacity:1}}
.modal-close{float:right;background:var(–surface);border:none;color:var(–muted);width:32px;height:32px;border-radius:50%;font-size:16px;cursor:pointer;display:flex;align-items:center;justify-content:center;margin-bottom:12px;}
.modal-img{width:100%;height:240px;border-radius:14px;overflow:hidden;margin-bottom:16px;background:var(–surface);display:flex;align-items:center;justify-content:center;font-size:64px;}
.modal-img img{width:100%;height:100%;object-fit:cover;object-position:center top;}
.modal-name{font-family:‘Bebas Neue’,sans-serif;font-size:32px;letter-spacing:1px;margin-bottom:6px;}
.modal-tip{font-size:14px;color:var(–muted);line-height:1.6;margin-bottom:16px;}
.modal-steps{display:flex;flex-direction:column;gap:8px;}
.modal-step{display:flex;gap:12px;align-items:flex-start;}
.ms-num{width:24px;height:24px;border-radius:6px;background:var(–accent2);color:#fff;font-size:11px;font-weight:700;display:flex;align-items:center;justify-content:center;flex-shrink:0;margin-top:1px;}
.ms-txt{font-size:13px;color:var(–muted);line-height:1.5;}

.cel-overlay{position:fixed;inset:0;background:rgba(10,10,15,0.92);display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:100;animation:fadeUp .3s ease;}
.cel-box{text-align:center;padding:40px 32px;max-width:340px;}
.cel-em{font-size:80px;margin-bottom:18px;animation:bop 1s infinite;}
@keyframes bop{0%,100%{transform:translateY(0)}50%{transform:translateY(-10px)}}
.cel-box h2{font-family:‘Bebas Neue’,sans-serif;font-size:46px;letter-spacing:2px;color:var(–accent);margin-bottom:8px;}
.cel-box p{font-size:14px;color:var(–muted);margin-bottom:26px;line-height:1.6;}
.cel-btn{background:var(–accent);color:#0a0a0f;border:none;border-radius:13px;padding:15px 40px;font-family:‘Bebas Neue’,sans-serif;font-size:18px;letter-spacing:2px;cursor:pointer;}
.cdot{position:fixed;pointer-events:none;animation:fall linear forwards;}
@keyframes fall{0%{transform:translateY(-20px) rotate(0deg);opacity:1}100%{transform:translateY(110vh) rotate(720deg);opacity:0}}
</style>

</head>
<body>
<div class="app">

<div class="header">
  <div class="logo">CalisFlow</div>
  <div class="streak-badge">🔥 <span id="streakNum">0</span> dage</div>
</div>

<div class="view active" id="vToday">
  <div class="content" id="todayContent"></div>
</div>

<div class="view" id="vProgress">
  <div class="content">
    <div class="label">Din Rejse</div>
    <h1>Frem<span>skridt</span></h1>
    <p class="sub">Se din udvikling og kommende milepæle.</p>
    <div class="sg" id="sgrid"></div>
    <div class="label">Denne Uge</div>
    <div class="card" style="padding:14px;margin-bottom:18px"><div class="wg" id="wgrid"></div></div>
    <div class="label">Milepæle</div>
    <div class="ml-wrap" style="padding-left:8px;position:relative">
      <div class="ml-line"></div>
      <div class="ml-fill" id="mlFill" style="height:0"></div>
      <div id="mlTrack"></div>
    </div>
  </div>
</div>

<div class="view" id="vPlan">
  <div class="content">
    <div class="label">Komplet Program</div>
    <h1>90-Dages <span>Plan</span></h1>
    <p class="sub">Alle dage lagt ud. Tryk på en øvelse for at se billede og beskrivelse.</p>
  </div>
  <div id="planList"></div>
</div>

<nav class="nav">
  <button class="nav-btn active" id="nbToday" onclick="go('Today')"><span class="ni">⚡</span>I DAG</button>
  <button class="nav-btn" id="nbProgress" onclick="go('Progress')"><span class="ni">📈</span>FREMSKRIDT</button>
  <button class="nav-btn" id="nbPlan" onclick="go('Plan')"><span class="ni">📋</span>PLAN</button>
</nav>

</div>

<!-- EXERCISE MODAL -->

<div id="exModal" style="display:none" class="modal-overlay" onclick="closeModal(event)">
  <div class="modal" id="modalBox">
    <button class="modal-close" onclick="closeModal()">✕</button>
    <div class="modal-img" id="mImg"></div>
    <div class="modal-name" id="mName"></div>
    <div class="modal-tip" id="mTip"></div>
    <div style="font-family:'Bebas Neue',sans-serif;font-size:15px;letter-spacing:1px;color:var(--accent);margin-bottom:10px;">SÅDAN GØRES DET</div>
    <div class="modal-steps" id="mSteps"></div>
  </div>
</div>

<!-- CELEBRATION -->

<div id="celOverlay" style="display:none" class="cel-overlay">
  <div class="cel-box">
    <div class="cel-em" id="celEm">🎉</div>
    <h2 id="celTitle">KLARET!</h2>
    <p id="celMsg">Endnu en dag i mål!</p>
    <button class="cel-btn" onclick="closeCel()">FORTSÆT →</button>
  </div>
</div>

<script>
// ── EXERCISE IMAGE + STEPS DATABASE ─────────────────
// Images: Wikimedia Commons (public domain / CC)
const EXDB = {
  'Push-ups': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/ab/Push-up_-_Crossfit_de_Grenoble.jpg/640px-Push-up_-_Crossfit_de_Grenoble.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Læg dig med hænderne lidt bredere end skulderbredde',
      'Hold kroppen som et lige bræt fra hæl til hoved',
      'Sænk brystet mod gulvet ved at bøje albuerne',
      'Skub dig op igen til udgangsposition',
    ]
  },
  'Squat': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Body_weight_squats.jpg/480px-Body_weight_squats.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Stå med fødder i skulderbredde, tæer let udad',
      'Hold ryggen rank og kig fremad',
      'Bøj knæ og hofter som om du sætter dig på en stol',
      'Gå ned til lårene er parallelle med gulvet, skub op igen',
    ]
  },
  'Plank': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/Plank_Position.jpg/640px-Plank_Position.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Start i armbøjningsposition eller på underarmene',
      'Hold kroppen i en ret linje fra hæl til nakke',
      'Spænd maven hårdt og klem ballerne',
      'Hold positionen i det angivne antal sekunder',
    ]
  },
  'Glute Bridge': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Glute_bridge.jpg/640px-Glute_bridge.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Læg dig på ryggen med knæ bøjet, fødder fladt i gulvet',
      'Pres hælene i gulvet og løft hofterne op',
      'Dan en ret linje fra knæ til skuldre',
      'Hold 1 sekund i toppen, sænk roligt ned',
    ]
  },
  'Mountain Climbers': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/Mountain_climber_exercise.jpg/640px-Mountain_climber_exercise.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Start i armbøjningsposition med strakte arme',
      'Hold hofterne nede i linje med ryggen',
      'Træk ét knæ hurtigt frem mod brystet',
      'Skift ben hurtigt i en løbebevægelse',
    ]
  },
  'Lunges': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/4b/Lunges.jpg/480px-Lunges.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Stå oprejst med fødder samlet',
      'Tag et stort skridt frem med ét ben',
      'Sænk bag-knæet mod gulvet (stop 2 cm fra)',
      'Skub fra frem-foden og tilbage til start',
    ]
  },
  'Diamond Push-ups': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/9/96/Diamond_push_up_-_starting_position.jpg/640px-Diamond_push_up_-_starting_position.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Form en diamant med tommel- og pegefingre under brystet',
      'Hold albuerne ind mod kroppen under bevægelsen',
      'Sænk dig roligt ned til næsen rammer hænderne',
      'Skub op til fuld udstrækning — fokus på triceps',
    ]
  },
  'Jump Squat': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Body_weight_squats.jpg/480px-Body_weight_squats.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Start i normal squat-position',
      'Sæt dig ned til 90 grader med eksplosiv kraft',
      'Spring op så hurtigt og højt som muligt',
      'Land blødt på forfoden med bøjede knæ for at absorbere stød',
    ]
  },
  'Side Plank': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/Side_plank.jpg/480px-Side_plank.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Læg dig på siden og støt på underarmen',
      'Løft hofterne op så kroppen danner en ret linje',
      'Hoftebenet pegler op mod loftet',
      'Hold positionen og skift side efter pausen',
    ]
  },
  'Single-leg Glute Bridge': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Glute_bridge.jpg/640px-Glute_bridge.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Læg på ryggen, ét ben bøjet med fod i gulvet',
      'Stræk det andet ben ud i luften',
      'Pres hælen i gulvet og løft hofterne',
      'Hold bækkenet vandret — skift ben efter sættet',
    ]
  },
  'Burpees': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Burpee.jpg/640px-Burpee.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Start stående, sæt hænderne i gulvet og hop benene tilbage',
      'Lav én push-up (eller skyd brystet ned)',
      'Hop benene frem til hænderne igen',
      'Spring op i luften med armene over hovedet',
    ]
  },
  'Reverse Lunges': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/4b/Lunges.jpg/480px-Lunges.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Stå oprejst med fødder samlet',
      'Tag et skridt baglæns med ét ben',
      'Sænk bag-knæet mod gulvet i kontrol',
      'Skub fra frem-foden tilbage til startposition',
    ]
  },
  'Clapping Push-ups': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/ab/Push-up_-_Crossfit_de_Grenoble.jpg/640px-Push-up_-_Crossfit_de_Grenoble.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Start i normal push-up position',
      'Sænk dig kontrolleret ned til gulvet',
      'Eksplodér op med maksimal kraft',
      'Klap hænderne mens du er i luften, land blødt og gentag',
    ]
  },
  'Pistol Squat (støttet)': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/Pistol_squat.jpg/480px-Pistol_squat.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Hold i en stoleryg eller dørkarmen for balance',
      'Stå på ét ben og stræk det andet ben frem',
      'Sænk dig roligt ned i squat på ét ben',
      'Hold ryggen rank, skub op til start — skift ben',
    ]
  },
  'Hollow Body Hold': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/Plank_Position.jpg/640px-Plank_Position.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Læg dig på ryggen med arme over hovedet',
      'Løft skuldre og ben fra gulvet (ca. 15 cm)',
      'Pres lænden helt flad mod gulvet — dette er nøglen',
      'Hold positionen og ånd roligt mens maven er spændt',
    ]
  },
  'Nordic Curl (negatives)': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b3/Nordic_hamstring_curl.jpg/640px-Nordic_hamstring_curl.jpg',
    credit: 'Wikimedia Commons / CC BY-SA',
    steps: [
      'Sæt fødder fast under en sofa eller bænk',
      'Knæl oprejst med kroppen rank',
      'Sænk dig MEGET langsomt fremad mod gulvet (3-5 sek)',
      'Brug hænderne til at tage imod og skub dig op igen',
    ]
  },
  'Explosive Burpees': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Burpee.jpg/640px-Burpee.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Som normale burpees — men med fuld fart og eksplosivitet',
      'Push-up del: eksplodér hurtigt op',
      'Hop-del: spring så højt som muligt med armene over hovedet',
      'Minimal pause mellem reps — hold tempoet oppe',
    ]
  },
  'Skater Jumps': {
    img: 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Body_weight_squats.jpg/480px-Body_weight_squats.jpg',
    credit: 'Wikimedia Commons',
    steps: [
      'Start stående på ét ben med let foroverbøj',
      'Spring sidelæns til det andet ben — som en skøjteløber',
      'Land blødtog kontrolleret på ét ben',
      'Spring straks tilbage til den anden side og gentag',
    ]
  },
};

// Fallback emoji per exercise
const EX_EMOJI = {
  'Push-ups':'🫸','Squat':'🦵','Plank':'🧱','Glute Bridge':'🍑',
  'Mountain Climbers':'🧗','Lunges':'🚶','Diamond Push-ups':'💎',
  'Jump Squat':'⬆️','Side Plank':'↔️','Single-leg Glute Bridge':'🦶',
  'Burpees':'🔄','Reverse Lunges':'↩️','Clapping Push-ups':'👏',
  'Pistol Squat (støttet)':'🔫','Hollow Body Hold':'🌙',
  'Nordic Curl (negatives)':'🍖','Explosive Burpees':'💥','Skater Jumps':'⛸️',
};

// ── 90-DAY PLAN ──────────────────────────────
const PHASES = [
  { label:'FUNDAMENT', range:'Dag 1–35', weeks:5, days:[
    {name:'Push-ups',        base:8,  inc:1, sets:3, tip:'Hold kroppen rank som et bræt. Sænk dig til brystet næsten rammer gulvet.'},
    {name:'Squat',           base:12, inc:2, sets:3, tip:'Fødder skulderbredde, knæ følger tæerne. Sid ned som på en stol.'},
    {name:'Plank',           base:20, inc:5, sets:3, tip:'Spænd maven, hold ryggen lige. Ånd roligt.', time:true},
    {name:'Glute Bridge',    base:12, inc:2, sets:3, tip:'Hæle i gulvet, løft hofterne til en ret linje.'},
    {name:'Mountain Climbers',base:10,inc:2,sets:3, tip:'Hold hofterne nede, alternér knæene hurtigt.', perSide:true},
    {name:'Lunges',          base:8,  inc:1, sets:3, tip:'Skridt frem, bag-knæ svæver 2 cm over gulvet.', perSide:true},
    {rest:true},
  ]},
  { label:'STYRKE', range:'Dag 36–63', weeks:4, days:[
    {name:'Diamond Push-ups',base:6,  inc:1, sets:4, tip:'Hænder danner diamantform under brystet.'},
    {name:'Jump Squat',      base:10, inc:2, sets:4, tip:'Eksplodér opad, land blødt på forfoden.'},
    {name:'Side Plank',      base:20, inc:5, sets:3, tip:'Hoftebenet løfter sig mod loftet. Krop i én linje.', time:true, perSide:true},
    {name:'Single-leg Glute Bridge',base:10,inc:2,sets:3,tip:'Hold bækkenet vandret, skift ben.', perSide:true},
    {name:'Burpees',         base:5,  inc:1, sets:3, tip:'Kontrolleret ned, eksplosiv op. Hold formen.'},
    {name:'Reverse Lunges',  base:8,  inc:2, sets:4, tip:'Skridt baglæns, front-knæ 90 grader.', perSide:true},
    {rest:true},
  ]},
  { label:'KRAFT', range:'Dag 64–90', weeks:4, days:[
    {name:'Clapping Push-ups',base:5, inc:1, sets:4, tip:'Fuld udstrækning, eksplodér opad og klap.'},
    {name:'Pistol Squat (støttet)',base:6,inc:1,sets:4,tip:'Hold i stoleryg, sæt dig dybt ned på ét ben.', perSide:true},
    {name:'Hollow Body Hold',base:20, inc:5, sets:4, tip:'Lænden presset i gulvet, arme og ben strakte ud.', time:true},
    {name:'Nordic Curl (negatives)',base:4,inc:1,sets:3,tip:'Fødder fast, sænk dig så langsomt du kan.'},
    {name:'Explosive Burpees',base:5, inc:1, sets:4, tip:'Hop så højt som muligt ved toppen.'},
    {name:'Skater Jumps',    base:8,  inc:2, sets:4, tip:'Spring sidelæns, land på ét ben, saml.', perSide:true},
    {rest:true},
  ]},
];

const PLAN = [];
PHASES.forEach(ph => {
  for(let w=0;w<ph.weeks;w++){
    ph.days.forEach(t=>{
      if(PLAN.length>=90) return;
      if(t.rest){ PLAN.push({dayNum:PLAN.length+1,rest:true,phase:ph.label,pRange:ph.range}); }
      else {
        PLAN.push({
          dayNum:PLAN.length+1, phase:ph.label, pRange:ph.range,
          name:t.name, reps:t.base+Math.floor(w*t.inc),
          sets:t.sets, tip:t.tip, time:!!t.time, perSide:!!t.perSide,
        });
      }
    });
  }
});

// ── STATE ─────────────────────────────────────
const SK='cf90_v2';
let S={startDate:null,completed:{},streak:0,best:0};
function save(){localStorage.setItem(SK,JSON.stringify(S));}
function load(){
  try{const r=localStorage.getItem(SK);if(r) S={...S,...JSON.parse(r)};}catch(e){}
  if(!S.startDate){S.startDate=isoToday();save();}
}
function isoToday(){return new Date().toISOString().split('T')[0];}
function daysSince(){return Math.floor((new Date(isoToday())-new Date(S.startDate))/86400000);}
function curDay(){return Math.min(daysSince()+1,90);}
function restreak(){
  let s=0,d=new Date(isoToday());
  for(let i=0;i<200;i++){
    const iso=d.toISOString().split('T')[0];
    const off=Math.floor((new Date(iso)-new Date(S.startDate))/86400000);
    const e=PLAN[off];
    if(!e) break;
    if(e.rest||S.completed[e.dayNum]){s++;d.setDate(d.getDate()-1);}
    else break;
  }
  S.streak=s;if(s>S.best)S.best=s;
}

// ── NAV ───────────────────────────────────────
function go(name){
  document.querySelectorAll('.view').forEach(v=>v.classList.remove('active'));
  document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active'));
  document.getElementById('v'+name).classList.add('active');
  document.getElementById('nb'+name).classList.add('active');
  if(name==='Progress') renderProgress();
  if(name==='Plan') renderPlan();
}

// ── MODAL ─────────────────────────────────────
function showExerciseModal(name, tip){
  const db=EXDB[name]||{};
  const steps=db.steps||[tip];
  const em=EX_EMOJI[name]||'💪';

  const imgHtml = db.img
    ? `<img src="${db.img}" alt="${name}" onerror="this.style.display='none';this.parentNode.querySelector('.fb').style.display='flex'">
       <div class="fb" style="display:none;position:absolute;inset:0;align-items:center;justify-content:center;font-size:64px">${em}</div>
       ${db.credit?`<div style="position:absolute;bottom:6px;right:10px;font-size:10px;color:rgba(255,255,255,0.3);font-style:italic">${db.credit}</div>`:''}`
    : em;

  document.getElementById('mImg').innerHTML=imgHtml;
  document.getElementById('mImg').style.position='relative';
  document.getElementById('mName').textContent=name;
  document.getElementById('mTip').textContent=tip;
  document.getElementById('mSteps').innerHTML=steps.map((s,i)=>`
    <div class="modal-step">
      <div class="ms-num">${i+1}</div>
      <div class="ms-txt">${s}</div>
    </div>`).join('');
  document.getElementById('exModal').style.display='flex';
}

function closeModal(e){
  if(!e||e.target===document.getElementById('exModal')) {
    document.getElementById('exModal').style.display='none';
  }
}

// ── IMAGE BANNER FOR TODAY ─────────────────────
function exImgBanner(name, tip){
  const db=EXDB[name]||{};
  const em=EX_EMOJI[name]||'💪';
  if(db.img){
    return `<div class="ex-img-wrap" onclick="showExerciseModal('${name.replace(/'/g,"\\'")}','${tip.replace(/'/g,"\\'")}')">
      <img src="${db.img}" alt="${name}" onerror="this.style.display='none';this.parentNode.querySelector('.img-fallback').style.display='flex'">
      <div class="img-fallback" style="display:none"><span>${em}</span></div>
      <div class="ex-img-label">Tryk for trin-for-trin guide${db.credit?' · '+db.credit:''}</div>
    </div>`;
  }
  return `<div class="ex-img-wrap" onclick="showExerciseModal('${name.replace(/'/g,"\\'")}','${tip.replace(/'/g,"\\'")}')">
    <div class="img-fallback"><span>${em}</span><span>Tryk for guide</span></div>
  </div>`;
}

// ── TODAY ─────────────────────────────────────
let checkedSets=new Set();
function renderToday(){
  checkedSets=new Set();
  const dn=curDay(), e=PLAN[dn-1];
  const box=document.getElementById('todayContent');
  if(!e){box.innerHTML=`<div class="rest-card"><div class="rest-emoji">🏆</div><h2>PROGRAM FULDFØRT!</h2><p>Du har gennemført alle 90 dage — respekt!</p></div>`;return;}
  if(e.rest){
    box.innerHTML=`<div class="rest-card"><div class="rest-emoji">😴</div><h2>Hviledag</h2><p>Kroppen vokser mens du hviler. Drik vand, spis godt, sov nok. Du er klar igen i morgen.</p></div>${miniStats()}`;
    return;
  }
  const done=!!S.completed[dn];
  const unit=e.time?'sek':(e.perSide?'reps/side':'reps');
  const sets=Array.from({length:e.sets},(_,i)=>i+1);
  const tipSafe=e.tip.replace(/'/g,"\\'");
  const nameSafe=e.name.replace(/'/g,"\\'");

  box.innerHTML=`
  <div class="today-hero">
    <div class="today-day">DAG ${dn} AF 90 · ${e.phase}</div>
    <div class="today-name">${e.name}</div>
    ${exImgBanner(e.name, e.tip)}
    <div class="today-tip">${e.tip}</div>
    <div class="today-meta">
      <div class="chip">🔄 <b>${e.sets} sæt</b></div>
      <div class="chip">${e.time?'⏱':'💪'} <b>${e.reps} ${unit}</b></div>
      <div class="chip">📅 <b>Dag ${dn}</b></div>
      <div class="chip" style="cursor:pointer;border:1px solid rgba(200,241,53,0.2);color:var(--accent)" onclick="showExerciseModal('${nameSafe}','${tipSafe}')">📖 Guide</div>
    </div>
    <div class="set-rows" id="setRows">
      ${sets.map(i=>`
      <div class="set-row${done?' done':''}" id="sr${i}" onclick="toggleSet(${i},${e.sets},${done})">
        <div class="set-num">${i}</div>
        <div class="set-info">
          <div class="set-name">Sæt ${i}</div>
          <div class="set-detail">${e.reps} ${unit}${i<e.sets?' · Hvil ~60 sek':''}</div>
        </div>
        <div class="set-check">${done?'✓':''}</div>
      </div>`).join('')}
    </div>
    <button class="done-btn${done?' already':''}" id="doneBtn" onclick="markDone(${dn})" ${done?'disabled':''}>
      ${done?'✓ DAGENS TRÆNING KLARET':'MARKER SOM FÆRDIG ✓'}
    </button>
  </div>
  ${nextPreview(dn)}
  ${miniStats()}`;
  if(!done) document.getElementById('doneBtn').disabled=true;
}

function toggleSet(i,total,alreadyDone){
  if(alreadyDone) return;
  const row=document.getElementById('sr'+i);
  const chk=row.querySelector('.set-check');
  if(checkedSets.has(i)){checkedSets.delete(i);row.classList.remove('done');chk.style.color='transparent';chk.textContent='';}
  else{checkedSets.add(i);row.classList.add('done');chk.style.color='';chk.textContent='✓';}
  const btn=document.getElementById('doneBtn');
  if(btn) btn.disabled=checkedSets.size<total;
}

function markDone(dn){
  if(S.completed[dn]) return;
  S.completed[dn]=isoToday();restreak();save();
  document.getElementById('streakNum').textContent=S.streak;
  const done=Object.keys(S.completed).length;
  const ms={1:'🔥',7:'⚡',14:'💪',30:'🏅',60:'🥈',90:'🏆'};
  showCel(ms[done]||'✅',ms[done]?`DAG ${done} KLARET!`:'KLARET!',
    ms[done]?`Milepæl! ${done} sessioner fuldført. Fantastisk!`:`Streak: ${S.streak} dage 🔥`);
  renderToday();
}

function nextPreview(dn){
  const nx=PLAN[dn];
  if(!nx) return '';
  const em=EX_EMOJI[nx.name]||'💪';
  const inner=nx.rest
    ?'<p style="color:var(--muted);font-size:14px">Hviledag — nyd det!</p>'
    :`<div style="display:flex;align-items:center;gap:12px;cursor:pointer" onclick="showExerciseModal('${(nx.name||'').replace(/'/g,"\\'")}','${(nx.tip||'').replace(/'/g,"\\'")}')">
        <div style="font-size:28px">${em}</div>
        <div><div style="font-weight:600;font-size:14px">${nx.name}</div>
        <div style="font-size:12px;color:var(--muted)">${nx.sets} sæt × ${nx.reps} ${nx.time?'sek':nx.perSide?'reps/side':'reps'}</div></div>
        <div style="margin-left:auto;font-size:12px;color:var(--accent)">Se øvelse →</div>
      </div>`;
  return `<div class="card"><div class="card-title">⏭ NÆSTE DAG</div>${inner}</div>`;
}

function miniStats(){
  const done=Object.keys(S.completed).length;
  const pct=Math.round(done/90*100);
  const barW=Math.max(pct,2);
  return `<div class="card"><div class="card-title">📊 STATUS</div>
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px">
    <div style="background:var(--surface);border-radius:10px;padding:14px"><div style="font-family:'Bebas Neue',sans-serif;font-size:30px;color:var(--accent)">${done}</div><div style="font-size:11px;color:var(--muted)">Sessioner klaret</div></div>
    <div style="background:var(--surface);border-radius:10px;padding:14px"><div style="font-family:'Bebas Neue',sans-serif;font-size:30px;color:var(--accent)">${pct}%</div><div style="font-size:11px;color:var(--muted)">Af 90 dage</div></div>
  </div>
  <div style="margin-top:12px;height:5px;background:var(--surface);border-radius:3px;overflow:hidden"><div style="height:100%;width:${barW}%;background:var(--accent);border-radius:3px;transition:width .5s"></div></div>
  </div>`;
}

// ── PROGRESS ─────────────────────────────────
function renderProgress(){
  const done=Object.keys(S.completed).length;
  const pct=Math.round(done/90*100);
  document.getElementById('sgrid').innerHTML=`
    <div class="sb"><div class="sv">${done}</div><div class="sl">Sessioner klaret</div></div>
    <div class="sb"><div class="sv">${S.streak}🔥</div><div class="sl">Nuværende streak</div></div>
    <div class="sb"><div class="sv">${S.best}</div><div class="sl">Bedste streak</div></div>
    <div class="sb"><div class="sv">${pct}%</div><div class="sl">Program fuldført</div></div>`;
  const wg=document.getElementById('wgrid');wg.innerHTML='';
  const today=new Date(isoToday());
  const mon=new Date(today);mon.setDate(today.getDate()-((today.getDay()+6)%7));
  ['M','T','O','T','F','L','S'].forEach((n,i)=>{
    const d=new Date(mon);d.setDate(mon.getDate()+i);
    const iso=d.toISOString().split('T')[0];
    const off=Math.floor((d-new Date(S.startDate))/86400000);
    const e=PLAN[off];
    const isDone=e&&S.completed[e.dayNum];
    const isToday=iso===isoToday();
    const isRest=e&&e.rest;
    wg.innerHTML+=`<div class="wd"><div class="wdn">${n}</div><div class="wdd ${isDone?'done':''} ${isToday&&!isDone?'today':''}">${isDone?'✅':isRest?'😴':isToday?'⚡':''}</div></div>`;
  });
  const mls=[
    {day:1,em:'🔥',t:'Første dag',d:'Du mødte op — det er alt.'},
    {day:7,em:'⚡',t:'1 uge klaret',d:'Vanen er begyndt at tage form.'},
    {day:14,em:'💪',t:'2 uger stærk',d:'Din krop mærker forandringen.'},
    {day:30,em:'🏅',t:'30 dage — fundament',d:'Halvvejs til styrke-fasen.'},
    {day:60,em:'🥈',t:'60 dage — styrke',d:'Du er markant stærkere nu.'},
    {day:90,em:'🏆',t:'90 dage fuldført!',d:'Du er et andet menneske.'},
  ];
  const tr=document.getElementById('mlTrack');tr.innerHTML='';
  mls.forEach((m,idx)=>{
    const reached=done>=m.day;
    const current=reached&&(idx===mls.length-1||done<mls[idx+1].day);
    tr.innerHTML+=`<div class="ml ${reached?'reached':''} ${current?'current':''}">
      <div class="ml-dot">${m.em}</div>
      <div class="ml-body">
        <div class="ml-title">${m.t}</div>
        <div class="ml-sub">${m.d}</div>
        ${reached?`<span class="ml-badge">✓ NÅET DAG ${m.day}</span>`:`<span style="font-size:11px;color:var(--muted)">Dag ${m.day}</span>`}
      </div></div>`;
  });
  requestAnimationFrame(()=>{
    const rc=mls.filter(m=>done>=m.day).length;
    const f=document.getElementById('mlFill');
    if(f)f.style.height=(rc/mls.length*100)+'%';
  });
}

// ── PLAN ─────────────────────────────────────
function renderPlan(){
  const pl=document.getElementById('planList');pl.innerHTML='';
  const dn=curDay();
  let curPhase='',blockEl=null;
  PLAN.forEach(e=>{
    if(e.phase!==curPhase){
      curPhase=e.phase;
      blockEl=document.createElement('div');
      blockEl.className='phase-block';
      blockEl.innerHTML=`<div class="phase-title">${e.phase} <span class="phase-sub">${e.pRange}</span></div>`;
      pl.appendChild(blockEl);
    }
    const isToday=e.dayNum===dn;
    const isDone=!!S.completed[e.dayNum];
    const isPast=e.dayNum<dn&&!isDone&&!e.rest;
    const unit=e.time?'sek':e.perSide?'reps/side':'reps';
    const em=e.rest?'😴':(EX_EMOJI[e.name]||'💪');
    const db=e.rest?null:EXDB[e.name];
    const thumbHtml=db&&db.img
      ?`<div class="p-thumb"><img src="${db.img}" alt="${e.name}" onerror="this.parentNode.innerHTML='${em}'"></div>`
      :`<div class="p-thumb">${em}</div>`;
    const row=document.createElement('div');
    row.className=`plan-row ${isDone?'pdone':''} ${isToday?'is-today':''}`;
    if(!e.rest){
      row.onclick=()=>showExerciseModal(e.name,e.tip);
    }
    row.innerHTML=`
      ${thumbHtml}
      <div class="pnum">${e.dayNum}</div>
      <div class="pinfo">
        <div class="pname">${e.rest?'Hviledag 😴':e.name}</div>
        <div class="psub">${e.rest?'Restitution':`${e.sets} sæt × ${e.reps} ${unit}`}</div>
      </div>
      <div class="pstat">${isDone?'✅':isToday?'⚡':isPast?'❌':''}</div>`;
    blockEl.appendChild(row);
  });
}

// ── CELEBRATION ───────────────────────────────
function showCel(em,title,msg){
  document.getElementById('celEm').textContent=em;
  document.getElementById('celTitle').textContent=title;
  document.getElementById('celMsg').textContent=msg;
  document.getElementById('celOverlay').style.display='flex';
  const cols=['#c8f135','#7b61ff','#ff6b6b','#61d5ff','#ffb347'];
  for(let i=0;i<60;i++){
    const d=document.createElement('div');d.className='cdot';
    d.style.cssText=`left:${Math.random()*100}vw;top:0;background:${cols[i%5]};width:${6+Math.random()*6}px;height:${6+Math.random()*6}px;border-radius:${Math.random()>.5?'50%':'2px'};animation-duration:${1.5+Math.random()*2}s;animation-delay:${Math.random()*.5}s;`;
    document.body.appendChild(d);
  }
}
function closeCel(){
  document.getElementById('celOverlay').style.display='none';
  document.querySelectorAll('.cdot').forEach(d=>d.remove());
}

// ── INIT ─────────────────────────────────────
load();restreak();
document.getElementById('streakNum').textContent=S.streak;
renderToday();
</script>

</body>
</html>
