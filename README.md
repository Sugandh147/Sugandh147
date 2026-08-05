<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>who goes there?</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@300;400;500&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
<style>
  :root{
    --bg: #05050a;
    --bg-soft: #0a0a14;
    --violet: #6A11CB;
    --blue: #2575FC;
    --cyan: #00c6ff;
    --text: #e9e9f2;
    --muted: #6f7086;
    --line: rgba(233,233,242,0.08);
  }
  *{ margin:0; padding:0; box-sizing:border-box; }
  html{ scroll-behavior:smooth; }
  body{
    background:var(--bg); color:var(--text); font-family:'Inter', sans-serif;
    overflow-x:hidden; cursor:none;
  }
  ::selection{ background:var(--violet); color:#fff; }
  a{ cursor:none; }

  #cursor-dot, #cursor-ring{ position:fixed; top:0; left:0; pointer-events:none; z-index:999; border-radius:50%; }
  #cursor-dot{ width:6px; height:6px; background:var(--cyan); box-shadow:0 0 10px var(--cyan); }
  #cursor-ring{ width:32px; height:32px; border:1px solid rgba(0,198,255,0.5); transition: transform .15s ease, opacity .3s ease, border-color .3s ease; }

  #loader{
    position:fixed; inset:0; z-index:1000; background:#05050a;
    display:flex; flex-direction:column; align-items:center; justify-content:center; gap:18px;
  }
  #loader .lg{ font-family:'JetBrains Mono', monospace; font-size:12px; letter-spacing:0.2em; color:var(--muted); }
  #loader .bar-track{ width:220px; height:1px; background:var(--line); position:relative; overflow:hidden; }
  #loader .bar-fill{ position:absolute; left:0; top:0; height:100%; width:0%; background:linear-gradient(90deg,var(--violet),var(--cyan)); }
  #loader .pct{ font-family:'JetBrains Mono', monospace; font-size:11px; color:var(--cyan); }

  #field{ position:fixed; inset:0; z-index:0; }
  #petals{ position:fixed; inset:0; z-index:1; pointer-events:none; }

  .noise{
    position:fixed; inset:0; z-index:2; pointer-events:none; opacity:0.035;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  }
  .vignette{
    position:fixed; inset:0; z-index:2; pointer-events:none;
    background: radial-gradient(ellipse at center, transparent 35%, rgba(0,0,0,0.8) 100%);
  }

  section{ position:relative; z-index:3; padding: 140px 8vw; max-width:1200px; margin:0 auto; }

  nav{
    position:fixed; top:0; left:0; right:0; z-index:50;
    display:flex; justify-content:space-between; align-items:center;
    padding: 26px 8vw; font-family:'JetBrains Mono', monospace; font-size:12px;
    letter-spacing:0.12em; color:var(--muted); mix-blend-mode:difference;
  }
  nav .dot{ color:var(--cyan); }

  .hero{
    height:100vh; min-height:680px; display:flex; align-items:center; justify-content:center;
    position:relative; padding:0 6vw; overflow:hidden;
  }
  .hero-inner{ text-align:center; position:relative; z-index:4; }

  .hero-figure{
    position:absolute; left:50%; top:52%; transform:translate(-50%,-50%);
    width:min(70vw, 620px); z-index:1; opacity:0.9; pointer-events:none;
  }
  .hero-figure svg{ width:100%; height:auto; filter: drop-shadow(0 0 40px rgba(106,17,203,0.35)); }
  .moon-glow{ animation: moonPulse 6s ease-in-out infinite; }
  @keyframes moonPulse{ 0%,100%{ opacity:0.55; } 50%{ opacity:0.95; } }
  .figure-float{ animation: figFloat 7s ease-in-out infinite; }
  @keyframes figFloat{ 0%,100%{ transform:translateY(0px);} 50%{ transform:translateY(-14px);} }
  .eye-glow{ animation: eyeBlink 4.5s ease-in-out infinite; }
  @keyframes eyeBlink{ 0%,92%,100%{ opacity:1;} 95%{ opacity:0.1;} }

  .eyebrow{
    font-family:'JetBrains Mono', monospace; font-size:13px; letter-spacing:0.25em;
    color:var(--cyan); opacity:0; margin-bottom:22px; text-transform:uppercase;
  }
  .eyebrow .cursor-blink{ display:inline-block; width:8px; height:1em; background:var(--cyan); margin-left:4px; vertical-align:-2px; animation:blink 1s steps(1) infinite; }
  @keyframes blink{ 50%{ opacity:0; } }

  h1.title{
    font-family:'Space Grotesk', sans-serif; font-weight:700;
    font-size:clamp(2.4rem, 8.5vw, 6rem); line-height:0.98; letter-spacing:-0.02em;
    background:linear-gradient(120deg, #fff 0%, #cfd0ff 40%, var(--cyan) 100%);
    -webkit-background-clip:text; background-clip:text; color:transparent;
    opacity:0; min-height:1.1em;
  }

  .subtitle{
    font-family:'Inter', sans-serif; font-weight:300; font-size:clamp(0.95rem,1.6vw,1.2rem);
    color:var(--muted); margin-top:26px; max-width:560px; margin-left:auto; margin-right:auto;
    opacity:0; font-style:italic;
  }

  .scroll-cue{
    position:absolute; bottom:46px; left:50%; transform:translateX(-50%); z-index:4;
    font-family:'JetBrains Mono', monospace; font-size:11px; color:var(--muted);
    letter-spacing:0.2em; display:flex; flex-direction:column; align-items:center; gap:10px; opacity:0;
  }
  .scroll-cue .bar{ width:1px; height:34px; background:linear-gradient(var(--cyan), transparent); animation:drop 1.8s ease-in-out infinite; }
  @keyframes drop{ 0%{ transform:scaleY(0); transform-origin:top; opacity:1;} 50%{ transform:scaleY(1); transform-origin:top;} 100%{ transform:scaleY(0); transform-origin:bottom; opacity:0.3;} }

  .label{
    font-family:'JetBrains Mono', monospace; font-size:12px; letter-spacing:0.22em;
    color:var(--cyan); text-transform:uppercase; margin-bottom:18px; display:flex; align-items:center; gap:10px;
  }
  .label::before{ content:''; width:22px; height:1px; background:var(--cyan); display:inline-block; }
  h2.h-title{
    font-family:'Space Grotesk', sans-serif; font-weight:600; font-size:clamp(1.8rem,4vw,2.8rem);
    letter-spacing:-0.01em; margin-bottom:48px;
  }
  .reveal{ opacity:0; transform:translateY(36px); }

  .about-grid{ display:grid; grid-template-columns: 1.1fr 0.9fr; gap:60px; align-items:center; }
  @media(max-width:800px){ .about-grid{ grid-template-columns:1fr; } }
  .terminal{
    font-family:'JetBrains Mono', monospace; font-size:14px; line-height:2.1;
    background:var(--bg-soft); border:1px solid var(--line); border-radius:10px; padding:28px 30px;
    position:relative; overflow:hidden;
  }
  .terminal::before{
    content:''; position:absolute; inset:0; background:linear-gradient(120deg, transparent 30%, rgba(0,198,255,0.06) 50%, transparent 70%);
    background-size:200% 200%; animation: sheen 5s linear infinite;
  }
  @keyframes sheen{ 0%{ background-position:200% 0;} 100%{ background-position:-200% 0;} }
  .terminal .k{ color:var(--muted); } .terminal .v{ color:var(--text); } .terminal .accent{ color:var(--cyan); }
  .about-copy p{ color:var(--muted); font-weight:300; line-height:1.85; margin-bottom:18px; font-size:1.02rem; }
  .about-copy strong{ color:var(--text); font-weight:500; }
  .torii{ width:120px; opacity:0.5; margin-bottom:26px; }

  .stack-cat{ margin-bottom:38px; }
  .stack-cat h3{ font-family:'JetBrains Mono', monospace; font-size:12px; color:var(--muted); letter-spacing:0.15em; text-transform:uppercase; margin-bottom:16px; }
  .chips{ display:flex; flex-wrap:wrap; gap:10px; }
  .chip{
    font-family:'Space Grotesk', sans-serif; font-size:13.5px; font-weight:500;
    padding:9px 18px; border-radius:100px; border:1px solid var(--line);
    color:var(--text); background:rgba(255,255,255,0.02); transition: all .35s ease;
  }
  .chip:hover{ border-color:var(--cyan); color:var(--cyan); box-shadow:0 0 20px rgba(0,198,255,0.18); transform:translateY(-2px) scale(1.04); }

  .files-grid{ display:grid; grid-template-columns:1fr 1fr; gap:18px; }
  @media(max-width:700px){ .files-grid{ grid-template-columns:1fr; } }
  .file-card{
    background:var(--bg-soft); border:1px solid var(--line); border-radius:14px; padding:34px;
    position:relative; transform-style:preserve-3d; transition: transform .15s ease, border-color .3s ease;
    overflow:hidden;
  }
  .file-card::after{
    content:''; position:absolute; inset:0; opacity:0; transition:opacity .3s ease;
    background:radial-gradient(400px circle at var(--mx,50%) var(--my,50%), rgba(0,198,255,0.12), transparent 60%);
  }
  .file-card:hover::after{ opacity:1; }
  .file-card:hover{ border-color:rgba(0,198,255,0.35); }
  .file-tag{ font-family:'JetBrains Mono', monospace; font-size:10.5px; letter-spacing:0.15em; color:var(--cyan); border:1px solid rgba(0,198,255,0.3); padding:4px 10px; border-radius:100px; display:inline-block; margin-bottom:16px; }
  .file-card h4{ font-family:'Space Grotesk', sans-serif; font-size:1.25rem; margin-bottom:10px; }
  .file-card p{ color:var(--muted); font-size:0.92rem; font-weight:300; line-height:1.7; }

  .channels{ text-align:center; }
  .mask-icon{ width:70px; margin:0 auto 24px; opacity:0.8; }
  .channel-links{ display:flex; gap:18px; justify-content:center; flex-wrap:wrap; margin-top:10px; }
  .channel-btn{
    font-family:'JetBrains Mono', monospace; font-size:13px; letter-spacing:0.05em;
    padding:15px 30px; border-radius:100px; border:1px solid var(--line); color:var(--text);
    text-decoration:none; transition: all .35s ease; background:rgba(255,255,255,0.02);
  }
  .channel-btn:hover{ border-color:var(--cyan); box-shadow:0 0 30px rgba(106,17,203,0.4); transform:translateY(-3px); background:rgba(0,198,255,0.05); }

  footer{ position:relative; z-index:3; text-align:center; padding:60px 20px 40px; font-family:'JetBrains Mono', monospace; font-size:11px; color:var(--muted); letter-spacing:0.1em; }
</style>
</head>
<body>

<div id="loader">
  <div class="lg">DECRYPTING IDENTITY</div>
  <div class="bar-track"><div class="bar-fill" id="barFill"></div></div>
  <div class="pct" id="pct">0%</div>
</div>

<div id="cursor-ring"></div>
<div id="cursor-dot"></div>

<canvas id="field"></canvas>
<canvas id="petals"></canvas>
<div class="noise"></div>
<div class="vignette"></div>

<nav>
  <span><span class="dot">●</span> SYSTEM ONLINE</span>
  <span id="clock">--:--:--</span>
</nav>

<section class="hero">
  <div class="hero-figure">
    <svg viewBox="0 0 400 500" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <radialGradient id="moonGrad" cx="50%" cy="50%" r="50%">
          <stop offset="0%" stop-color="#00c6ff" stop-opacity="0.9"/>
          <stop offset="100%" stop-color="#00c6ff" stop-opacity="0"/>
        </radialGradient>
        <linearGradient id="cloakGrad" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#1a1330"/>
          <stop offset="100%" stop-color="#05050a"/>
        </linearGradient>
      </defs>
      <circle class="moon-glow" cx="200" cy="120" r="90" fill="url(#moonGrad)"/>
      <circle cx="200" cy="120" r="46" fill="#0d0d1c" stroke="#6A11CB" stroke-width="1" opacity="0.8"/>
      <g class="figure-float">
        <path d="M200 190 C 120 210, 90 320, 100 460 L 300 460 C 310 320, 280 210, 200 190 Z" fill="url(#cloakGrad)" stroke="#2575FC" stroke-width="1" opacity="0.9"/>
        <path d="M200 190 C 160 200, 145 240, 150 270 C 165 250, 235 250, 250 270 C 255 240, 240 200, 200 190 Z" fill="#0a0a14" stroke="#6A11CB" stroke-width="1"/>
        <ellipse class="eye-glow" cx="178" cy="238" rx="7" ry="3.4" fill="#00c6ff"/>
        <ellipse class="eye-glow" cx="222" cy="238" rx="7" ry="3.4" fill="#00c6ff"/>
        <path d="M100 460 C 90 380, 60 340, 20 330" stroke="#2575FC" stroke-width="1" fill="none" opacity="0.5"/>
        <path d="M300 460 C 310 380, 340 340, 380 330" stroke="#6A11CB" stroke-width="1" fill="none" opacity="0.5"/>
      </g>
    </svg>
  </div>

  <div class="hero-inner">
    <div class="eyebrow" id="eyebrow">> initializing identity<span class="cursor-blink"></span></div>
    <h1 class="title" id="heroTitle"></h1>
    <p class="subtitle" id="heroSub">"I run parallel to the predictable world — the mystery is that I never converge."</p>
  </div>
  <div class="scroll-cue"><span>SCROLL</span><span class="bar"></span></div>
</section>

<section id="about">
  <div class="label">01 // profile</div>
  <h2 class="h-title reveal" data-scramble>about the signal</h2>
  <div class="about-grid">
    <div class="terminal reveal">
      <div><span class="k">education</span> &nbsp;: <span class="v">B.Tech CSE (AI/ML) — MIET Meerut</span></div>
      <div><span class="k">timeline</span> &nbsp;&nbsp;: <span class="v">2024 – 2028</span></div>
      <div><span class="k">current_op</span> : <span class="accent">hunting first SDE / AI-ML role</span></div>
      <div><span class="k">track_rec</span> &nbsp;: <span class="v">1x hackathon WINNER</span></div>
      <div><span class="k">known_for</span> &nbsp;: <span class="v">shipping before explaining</span></div>
      <div><span class="k">status</span> &nbsp;&nbsp;&nbsp;&nbsp;: <span class="accent">● ONLINE</span></div>
    </div>
    <div class="about-copy reveal">
      <svg class="torii" viewBox="0 0 120 100" xmlns="http://www.w3.org/2000/svg">
        <rect x="10" y="18" width="100" height="8" fill="#6A11CB"/>
        <rect x="0" y="32" width="120" height="6" fill="#2575FC"/>
        <rect x="24" y="38" width="8" height="55" fill="#00c6ff"/>
        <rect x="88" y="38" width="8" height="55" fill="#00c6ff"/>
      </svg>
      <p>A CS student who prefers <strong>building over talking</strong>. Currently deep in AI/ML, backend engineering, and full-stack development — most of it undocumented until it works.</p>
      <p>Open to <strong>interesting, non-trivial projects</strong>. Some things here are public. Most of the process isn't.</p>
    </div>
  </div>
</section>

<section id="arsenal">
  <div class="label">02 // arsenal</div>
  <h2 class="h-title reveal" data-scramble>tech overstack</h2>

  <div class="stack-cat reveal">
    <h3>Languages</h3>
    <div class="chips">
      <span class="chip">Python</span><span class="chip">Java</span><span class="chip">SQL</span>
      <span class="chip">JavaScript</span><span class="chip">HTML5</span><span class="chip">CSS3</span>
    </div>
  </div>
  <div class="stack-cat reveal">
    <h3>Frameworks &amp; Libraries</h3>
    <div class="chips">
      <span class="chip">React.js</span><span class="chip">FastAPI</span><span class="chip">Flask</span><span class="chip">Prisma</span>
    </div>
  </div>
  <div class="stack-cat reveal">
    <h3>Databases</h3>
    <div class="chips"><span class="chip">PostgreSQL</span><span class="chip">SQLite</span></div>
  </div>
  <div class="stack-cat reveal">
    <h3>AI / ML</h3>
    <div class="chips">
      <span class="chip">PyTorch</span><span class="chip">Model Training</span><span class="chip">Jupyter Notebook</span>
    </div>
  </div>
  <div class="stack-cat reveal">
    <h3>Tools &amp; Others</h3>
    <div class="chips">
      <span class="chip">VS Code</span><span class="chip">Claude Code</span><span class="chip">Sentry</span>
      <span class="chip">CodeRabbit</span><span class="chip">Git</span>
    </div>
  </div>
</section>

<section id="files">
  <div class="label">03 // archive</div>
  <h2 class="h-title reveal" data-scramble>declassified files</h2>
  <div class="files-grid">
    <div class="file-card reveal tilt"><span class="file-tag">FLAGSHIP</span><h4>Voicey</h4><p>Full-stack AI Voice SaaS. Built it, broke it, rebuilt it.</p></div>
    <div class="file-card reveal tilt"><span class="file-tag">ACTIVE</span><h4>Recommendation Engine</h4><p>ML-based e-commerce product recommendation system.</p></div>
    <div class="file-card reveal tilt"><span class="file-tag">IN PROGRESS</span><h4>BRAVISI</h4><p>AI Visibility &amp; Brand Intelligence Platform.</p></div>
    <div class="file-card reveal tilt"><span class="file-tag">???</span><h4>Classified</h4><p>There's always one more repo than the page shows.</p></div>
  </div>
</section>

<section id="channels" class="channels">
  <svg class="mask-icon" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
    <path d="M50 8 C20 8 8 32 8 55 C8 78 26 92 50 92 C74 92 92 78 92 55 C92 32 80 8 50 8 Z" fill="#0a0a14" stroke="#6A11CB" stroke-width="1.5"/>
    <ellipse cx="32" cy="52" rx="8" ry="5" fill="#00c6ff" opacity="0.85"/>
    <ellipse cx="68" cy="52" rx="8" ry="5" fill="#00c6ff" opacity="0.85"/>
    <path d="M30 74 Q50 88 70 74" stroke="#2575FC" stroke-width="1.5" fill="none"/>
  </svg>
  <div class="label" style="justify-content:center;">04 // transmit</div>
  <h2 class="h-title reveal" data-scramble>open a channel</h2>
  <div class="channel-links reveal">
    <a class="channel-btn" href="https://linkedin.com/in/sugandh-mahajan" target="_blank">LINKEDIN &nbsp;↗</a>
    <a class="channel-btn" href="mailto:mahajansugandh3@gmail.com">EMAIL &nbsp;↗</a>
  </div>
</section>

<footer>transmission ends here — for now.</footer>

<script>
window.addEventListener('load', ()=>{
  let p = 0;
  const fill = document.getElementById('barFill');
  const pct = document.getElementById('pct');
  const iv = setInterval(()=>{
    p += Math.random()*18;
    if(p >= 100){ p = 100; clearInterval(iv);
      gsap.to('#loader', { opacity:0, duration:0.6, delay:0.2, onComplete:()=>{
        document.getElementById('loader').style.display='none';
        startHero();
      }});
    }
    fill.style.width = p + '%';
    pct.textContent = Math.floor(p) + '%';
  }, 120);
});

const dot = document.getElementById('cursor-dot');
const ring = document.getElementById('cursor-ring');
let rx=0, ry=0;
window.addEventListener('mousemove', (e)=>{
  dot.style.transform = `translate(${e.clientX-3}px, ${e.clientY-3}px)`;
  rx = e.clientX; ry = e.clientY;
});
gsap.ticker.add(()=>{ ring.style.transform = `translate(${rx-16}px, ${ry-16}px)`; });
document.querySelectorAll('a, .chip, .file-card').forEach(el=>{
  el.addEventListener('mouseenter', ()=> ring.style.borderColor = '#00c6ff');
  el.addEventListener('mouseleave', ()=> ring.style.borderColor = 'rgba(0,198,255,0.5)');
});

const canvas = document.getElementById('field');
const ctx = canvas.getContext('2d');
let w,h,particles=[];
const COUNT = 130;
const mouse = { x: -9999, y: -9999 };
function resize(){ w = canvas.width = window.innerWidth; h = canvas.height = window.innerHeight; }
window.addEventListener('resize', resize); resize();
function initParticles(){
  particles = [];
  for(let i=0;i<COUNT;i++){
    const depth = Math.random();
    particles.push({ x:Math.random()*w, y:Math.random()*h, depth, r:depth*1.6+0.4,
      vx:(Math.random()-0.5)*0.15, vy:(Math.random()-0.5)*0.15, hue: Math.random()>0.6?'cyan':'violet' });
  }
}
initParticles();
window.addEventListener('mousemove', (e)=>{ mouse.x=e.clientX; mouse.y=e.clientY; });
window.addEventListener('mouseleave', ()=>{ mouse.x=-9999; mouse.y=-9999; });
function drawField(){
  ctx.clearRect(0,0,w,h);
  for(let i=0;i<particles.length;i++){
    const p = particles[i];
    const dx = mouse.x-p.x, dy = mouse.y-p.y, dist = Math.sqrt(dx*dx+dy*dy);
    if(dist < 160){ const f=(160-dist)/160; p.x -= dx*f*0.02*p.depth; p.y -= dy*f*0.02*p.depth; }
    p.x += p.vx; p.y += p.vy;
    if(p.x<0)p.x=w; if(p.x>w)p.x=0; if(p.y<0)p.y=h; if(p.y>h)p.y=0;
    const color = p.hue==='cyan' ? '0,198,255' : '106,17,203';
    ctx.beginPath(); ctx.arc(p.x,p.y,p.r,0,Math.PI*2);
    ctx.fillStyle = `rgba(${color}, ${0.35+p.depth*0.5})`; ctx.fill();
  }
  for(let i=0;i<particles.length;i++){
    for(let j=i+1;j<particles.length;j++){
      const a=particles[i], b=particles[j];
      const dx=a.x-b.x, dy=a.y-b.y, d=Math.sqrt(dx*dx+dy*dy);
      if(d<110){ ctx.beginPath(); ctx.moveTo(a.x,a.y); ctx.lineTo(b.x,b.y);
        ctx.strokeStyle = `rgba(120,140,255,${(1-d/110)*0.12})`; ctx.lineWidth=0.6; ctx.stroke(); }
    }
  }
  requestAnimationFrame(drawField);
}
drawField();

const pcanvas = document.getElementById('petals');
const pctx = pcanvas.getContext('2d');
let pw,ph,petals=[];
function presize(){ pw = pcanvas.width = window.innerWidth; ph = pcanvas.height = window.innerHeight; }
window.addEventListener('resize', presize); presize();
for(let i=0;i<26;i++){
  petals.push({ x:Math.random()*pw, y:Math.random()*ph, s:Math.random()*5+3,
    speed:Math.random()*0.6+0.3, sway:Math.random()*1.2, angle:Math.random()*Math.PI*2,
    hue: Math.random()>0.5 ? '106,17,203' : '0,198,255' });
}
function drawPetals(){
  pctx.clearRect(0,0,pw,ph);
  petals.forEach(pt=>{
    pt.y += pt.speed; pt.angle += 0.01; pt.x += Math.sin(pt.angle)*pt.sway*0.3;
    if(pt.y > ph){ pt.y = -10; pt.x = Math.random()*pw; }
    pctx.save(); pctx.translate(pt.x, pt.y); pctx.rotate(pt.angle);
    pctx.fillStyle = `rgba(${pt.hue}, 0.35)`;
    pctx.beginPath(); pctx.ellipse(0,0,pt.s,pt.s*0.55,0,0,Math.PI*2); pctx.fill();
    pctx.restore();
  });
  requestAnimationFrame(drawPetals);
}
drawPetals();

function tick(){ document.getElementById('clock').textContent = new Date().toTimeString().slice(0,8); }
tick(); setInterval(tick, 1000);

const CHARS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ#$%&01';
function scrambleInto(el, finalText, duration=900){
  const len = finalText.length;
  let frame = 0;
  const totalFrames = Math.floor(duration/30);
  const interval = setInterval(()=>{
    let out = '';
    for(let i=0;i<len;i++){
      const progress = frame/totalFrames;
      const revealPoint = i/len;
      if(progress > revealPoint + 0.15) out += finalText[i];
      else if(finalText[i] === ' ') out += ' ';
      else out += CHARS[Math.floor(Math.random()*CHARS.length)];
    }
    el.textContent = out;
    frame++;
    if(frame > totalFrames){ el.textContent = finalText; clearInterval(interval); }
  }, 30);
}

gsap.registerPlugin(ScrollTrigger);
function startHero(){
  const tl = gsap.timeline({ delay:0.2 });
  tl.to('#eyebrow', { opacity:1, duration:0.6, ease:'power2.out' })
    .to('#heroTitle', { opacity:1, duration:0.4 }, '+=0.1')
    .call(()=>{ scrambleInto(document.getElementById('heroTitle'), 'WHO GOES THERE?', 1100); })
    .to('#heroSub', { opacity:1, duration:1, ease:'power2.out' }, '+=0.9')
    .to('.scroll-cue', { opacity:1, duration:0.8 }, '-=0.4');

  document.querySelectorAll('.reveal').forEach((el)=>{
    gsap.to(el, { opacity:1, y:0, duration:0.9, ease:'power2.out',
      scrollTrigger:{ trigger: el, start:'top 85%' } });
  });
  document.querySelectorAll('.stack-cat').forEach((el, i)=>{
    gsap.to(el, { opacity:1, y:0, duration:0.7, delay:i*0.05, ease:'power2.out',
      scrollTrigger:{ trigger: el, start:'top 88%' } });
  });
  document.querySelectorAll('[data-scramble]').forEach((el)=>{
    const original = el.textContent;
    ScrollTrigger.create({ trigger: el, start:'top 85%', once:true,
      onEnter: ()=> scrambleInto(el, original, 800) });
  });
}

document.querySelectorAll('.tilt').forEach(card=>{
  card.addEventListener('mousemove', (e)=>{
    const r = card.getBoundingClientRect();
    const x = e.clientX - r.left, y = e.clientY - r.top;
    const rx = ((y / r.height) - 0.5) * -10;
    const ry = ((x / r.width) - 0.5) * 10;
    card.style.transform = `perspective(600px) rotateX(${rx}deg) rotateY(${ry}deg) scale(1.02)`;
    card.style.setProperty('--mx', x+'px');
    card.style.setProperty('--my', y+'px');
  });
  card.addEventListener('mouseleave', ()=>{ card.style.transform = 'perspective(600px) rotateX(0) rotateY(0) scale(1)'; });
});
</script>
</body>
</html>
