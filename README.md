<!DOCTYPE html>
<!-- saved from url=(0083)file:///C:/Users/hashy/Downloads/Ramo%20de%20Flores%20Amarillas%20%F0%9F%8C%BB.html -->
<html lang="es"><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Ramo de Flores Amarillas 🌻</title>
<link href="./Ramo de Flores Amarillas 🌻_files/css2" rel="stylesheet">
<style>
  :root{
    --gold:#f5c542;
    --gold-deep:#c8901a;
    --cream:#fff8e1;
  }

  *{ box-sizing:border-box; margin:0; padding:0; }

  html,body{ height:100%; }

  body{
    min-height:100vh;
    background:#050301;
    overflow:hidden;
    font-family:'Montserrat', system-ui, sans-serif;
    color:#fff;
    position:relative;
  }

  /* ============================================================
     CANVAS DE PARTÍCULAS
     (por encima de las escenas para que los brillos se vean)
     ============================================================ */
  #sparkles{
    position:fixed; inset:0;
    width:100%; height:100%;
    pointer-events:none;
    z-index:25;
  }

  /* ============================================================
     ESCENAS (comparten estructura)
     ============================================================ */
  .scene{
    position:fixed;
    inset:0;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    opacity:0;
    pointer-events:none;
    transition:opacity 1s ease;
    z-index:2;
  }
  .scene.active{
    opacity:1;
    pointer-events:auto;
  }

  /* ============================================================
     ESCENA 1: CAMPO DE GIRASOLES
     ============================================================ */
  .field-scene{
    background:
      linear-gradient(180deg,
        #04050e 0%,
        #0c0a1a 20%,
        #1e1224 40%,
        #3e2214 58%,
        #8a4e1a 74%,
        #5a3010 88%,
        #2a1608 100%);
    overflow:hidden;
  }

  .field-sky{
    position:absolute;
    inset:0;
    background:
      radial-gradient(ellipse at 50% 62%, rgba(255,180,80,.28) 0%, transparent 55%),
      radial-gradient(ellipse at 50% 58%, rgba(255,120,40,.12) 0%, transparent 42%);
    pointer-events:none;
    z-index:1;
  }

  .field-sun{
    position:absolute;
    left:50%;
    top:56%;
    width:min(75vw, 560px);
    aspect-ratio:1;
    transform:translate(-50%,-50%);
    background:radial-gradient(circle,
      rgba(255,225,130,.42) 0%,
      rgba(255,170,45,.16) 38%,
      transparent 66%);
    pointer-events:none;
    filter:blur(6px);
    z-index:1;
  }

  /* Suelo oscuro desde el horizonte hacia abajo */
  .field-ground{
    position:absolute;
    left:0; right:0;
    top:56%; bottom:0;
    background:linear-gradient(180deg,
      rgba(40,22,8,0) 0%,
      rgba(24,14,6,.55) 55%,
      rgba(10,6,3,.85) 100%);
    pointer-events:none;
    z-index:1;
  }

  /* Contenedor con la perspectiva (el overflow:hidden vive aquí) */
  .field-perspective{
    position:absolute;
    inset:0;
    overflow:hidden;
    perspective:900px;
    perspective-origin:50% 56%;
    z-index:2;
  }

  .flowers-layer{
    position:absolute;
    inset:0;
    transform-style:preserve-3d;
    transition:transform 5.5s cubic-bezier(0.5, 0, 0.75, 1);
    will-change:transform;
  }
  .flowers-layer.zoomed{
    transform:translateZ(4200px);
  }

  /* Cada girasol: la base del tallo queda en el punto (0,0) del horizonte;
     la posición se coloca con translate/scale/rotate y el vaivén con transform */
  .field-flower{
    position:absolute;
    left:50%;
    top:56%;
    width:140px;
    height:467px;            /* cabeza + tallo (ver STEM_LEN en el script) */
    margin-left:-70px;
    margin-top:-467px;
    transform-origin:50% 100%;
    animation:fieldSway 4s ease-in-out infinite alternate;
  }

  .field-flower svg{
    width:100%;
    height:100%;
    display:block;
    overflow:visible;
  }

  @keyframes fieldSway{
    from{ transform:rotate(-1.8deg); }
    to  { transform:rotate( 1.8deg); }
  }

  /* Degradado oscuro abajo para que el texto se lea bien */
  .field-vignette{
    position:absolute;
    left:0; right:0; bottom:0;
    height:34%;
    background:linear-gradient(0deg,
      rgba(8,5,2,.82) 0%,
      rgba(8,5,2,.35) 55%,
      transparent 100%);
    pointer-events:none;
    z-index:3;
  }

  /* Interfaz de la escena 1 */
  .field-ui{
    position:absolute;
    left:50%;
    bottom:8%;
    width:100%;
    max-width:520px;
    padding:0 24px;
    text-align:center;
    z-index:6;
    transform:translateX(-50%);
    transition:opacity .8s ease, transform .8s ease;
  }
  .field-ui.fade-out{
    opacity:0;
    transform:translate(-50%, 24px);
    pointer-events:none;
  }

  .field-ui h2{
    font-family:'Cinzel', serif;
    font-size:clamp(1.25rem, 4.4vw, 1.9rem);
    letter-spacing:.1em;
    color:#ffe27a;
    text-shadow:
      0 0 22px rgba(255,200,60,.55),
      0 2px 5px rgba(0,0,0,.9);
    margin-bottom:10px;
  }

  .field-sub{
    font-size:clamp(.7rem, 2.6vw, .88rem);
    letter-spacing:.06em;
    color:#e0bd75;
    opacity:.9;
    margin-bottom:22px;
  }

  /* ============================================================
     ESCENA 2: RAMO + CARTA
     ============================================================ */
  .bouquet-scene{
    background:radial-gradient(circle at 50% 36%, #3a230c 0%, #1a1006 44%, #070301 100%);
    padding:22px 16px;
    gap:14px;
    overflow-y:auto;
    overflow-x:hidden;
  }

  header{
    position:relative;
    z-index:3;
    text-align:center;
  }

  h1{
    font-family:'Cinzel', serif;
    font-size:clamp(1.25rem, 4.4vw, 2.1rem);
    letter-spacing:.12em;
    color:#ffe27a;
    text-shadow:
      0 0 18px rgba(255,200,60,.45),
      0 2px 4px rgba(0,0,0,.9);
  }

  .subtitle{
    margin-top:8px;
    font-size:clamp(.62rem, 2.4vw, .78rem);
    letter-spacing:.26em;
    text-transform:uppercase;
    color:#c9a24a;
    opacity:.85;
  }

  .bouquet-wrap{
    position:relative;
    z-index:2;
    width:min(88vw, 420px, 48vh);
    filter:drop-shadow(0 22px 34px rgba(0,0,0,.78));
  }

  .glow{
    position:absolute;
    left:50%; top:40%;
    width:150%; aspect-ratio:1;
    transform:translate(-50%,-50%);
    background:radial-gradient(circle,
      rgba(255,205,60,.20) 0%,
      rgba(255,175,30,.07) 46%,
      transparent 70%);
    pointer-events:none;
    z-index:-1;
  }

  .bouquet{
    display:block;
    width:100%;
    height:auto;
    overflow:visible;
  }

  .bouquet-sway{
    transform-origin:250px 570px;
    animation:sway 7.5s ease-in-out infinite alternate;
  }
  @keyframes sway{
    from{ transform:rotate(-1.7deg); }
    to  { transform:rotate( 1.7deg); }
  }

  .bob{
    transform-box:fill-box;
    transform-origin:center;
    animation:bob 6s ease-in-out infinite;
    animation-delay:var(--bd, 0s);
    will-change:transform;
  }
  @keyframes bob{
    0%,100%{ transform:rotate(-1.3deg) translateY(0); }
    50%    { transform:rotate( 1.3deg) translateY(-3px); }
  }

  /* ============================================================
     BOTONES
     ============================================================ */
  .controls{
    position:relative;
    z-index:3;
    display:flex;
    flex-wrap:wrap;
    gap:12px;
    justify-content:center;
    margin-top:4px;
  }

  .btn{
    display:flex;
    align-items:center;
    gap:8px;
    padding:11px 22px;
    font-family:inherit;
    font-size:.85rem;
    font-weight:600;
    letter-spacing:.04em;
    color:#1a0f04;
    border:none;
    border-radius:999px;
    cursor:pointer;
    background:linear-gradient(135deg, #ffdf6b 0%, #d4af37 55%, #a97c12 100%);
    box-shadow:
      0 4px 16px rgba(212,175,55,.32),
      inset 0 1px 0 rgba(255,255,255,.55);
    transition:transform .25s ease, box-shadow .25s ease, filter .25s ease;
  }

  .btn:hover{
    transform:translateY(-2px);
    filter:brightness(1.07);
    box-shadow:
      0 8px 22px rgba(255,214,80,.48),
      inset 0 1px 0 rgba(255,255,255,.6);
  }

  .btn:active{ transform:translateY(0) scale(.98); }

  .btn-enter{
    padding:14px 32px;
    font-size:.95rem;
    animation:pulseBtn 2.2s ease-in-out infinite;
  }

  @keyframes pulseBtn{
    0%,100%{
      box-shadow:
        0 4px 18px rgba(212,175,55,.42),
        inset 0 1px 0 rgba(255,255,255,.55);
    }
    50%{
      box-shadow:
        0 6px 34px rgba(255,220,80,.78),
        inset 0 1px 0 rgba(255,255,255,.55);
    }
  }

  /* ============================================================
     MODAL TARJETA
     ============================================================ */
  .modal{
    position:fixed; inset:0;
    display:none;
    align-items:center;
    justify-content:center;
    padding:22px;
    background:rgba(0,0,0,.82);
    backdrop-filter:blur(6px);
    z-index:40;
  }
  .modal.active{ display:flex; }

  .card{
    position:relative;
    width:100%;
    max-width:450px;
    padding:34px 30px 28px;
    text-align:center;
    border-radius:18px;
    background:
      radial-gradient(circle at 50% 0%, #fffdf2 0%, #fff7e2 60%, #f7ecd4 100%);
    color:#2c1a08;
    box-shadow:
      0 22px 50px rgba(0,0,0,.6),
      inset 0 0 0 2px #d4af37,
      inset 0 0 0 6px rgba(212,175,55,.16);
    animation:popIn .42s cubic-bezier(.175,.885,.32,1.275);
  }
  @keyframes popIn{
    from{ transform:scale(.86) translateY(12px); opacity:0; }
    to  { transform:scale(1) translateY(0);      opacity:1; }
  }

  .card h2{
    font-family:'Cinzel', serif;
    font-size:1.35rem;
    letter-spacing:.06em;
    color:#8b5a2b;
  }

  .card-emojis{
    margin-top:8px;
    font-size:1.1rem;
    letter-spacing:.3em;
    color:#d4af37;
  }

  .card-message{
    margin:20px 0 6px;
    min-height:86px;
    padding:6px;
    font-family:'Great Vibes', cursive;
    font-size:1.75rem;
    line-height:1.45;
    color:#4a321a;
    border:1px dashed transparent;
    border-radius:8px;
    outline:none;
    transition:border-color .25s ease, background .25s ease;
  }
  .card-message:focus{
    border-color:#d4af37;
    background:rgba(255,255,255,.6);
  }

  .card-footer{
    margin-top:14px;
    font-size:.82rem;
    color:#7f6c5b;
    line-height:1.6;
  }

  .close-card{
    position:absolute;
    top:8px; right:14px;
    font-size:1.6rem;
    line-height:1;
    color:#8b5a2b;
    background:none;
    border:none;
    cursor:pointer;
    transition:transform .2s ease, color .2s ease;
  }
  .close-card:hover{ transform:rotate(90deg); color:#c8901a; }

  /* ============================================================
     TRANSICIÓN A NEGRO
     ============================================================ */
  .transition-black{
    position:fixed; inset:0;
    background:#000;
    opacity:0;
    pointer-events:none;
    transition:opacity .6s ease;
    z-index:30;
  }
  .transition-black.active{ opacity:1; }

  /* ============================================================
     TOAST
     ============================================================ */
  .toast{
    position:fixed;
    left:50%; bottom:26px;
    transform:translate(-50%, 20px);
    padding:11px 24px;
    font-size:.85rem;
    font-weight:600;
    color:#1a0f04;
    background:linear-gradient(135deg, #ffe27a, #d4af37);
    border-radius:999px;
    box-shadow:0 8px 24px rgba(0,0,0,.45);
    opacity:0;
    pointer-events:none;
    transition:opacity .35s ease, transform .35s ease;
    z-index:50;
  }
  .toast.show{
    opacity:1;
    transform:translate(-50%, 0);
  }

  /* ============================================================
     ACCESIBILIDAD
     ============================================================ */
  @media (prefers-reduced-motion: reduce){
    .bouquet-sway,
    .bob,
    .field-flower,
    .btn-enter{
      animation:none !important;
    }
    .flowers-layer{
      transition-duration:1.2s !important;
    }
  }
</style>
</head>
<body>

<!-- ============================================================
     SVG OCULTO: PATRÓN DE SEMILLAS + DEFINICIONES DE LOS GIRASOLES
     (las definiciones de las flores las genera el script)
     ============================================================ -->
<svg style="position:absolute;width:0;height:0;overflow:hidden" aria-hidden="true">
  <defs id="fieldDefs">
    <pattern id="fieldSeeds" width="6" height="6" patternUnits="userSpaceOnUse">
      <circle cx="1.5" cy="1.5" r="1.05" fill="#a87b42" opacity=".55"></circle>
      <circle cx="4.5" cy="4.5" r="1.05" fill="#1d0f04" opacity=".6"></circle>
    </pattern>
  <path id="fpetal" d="M0 -14 C 7 -24 10 -42 0 -56 C -10 -42 -7 -24 0 -14 Z"></path><g id="fleaf0">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#565a1b"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#4a4714" stroke-width="1.6"></path>
      </g><g id="fhead0_0"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99700f"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_1"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#996d0b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_2"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#997432"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_3"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#95650b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_4"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99630b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf1">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#546e20"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#415214" stroke-width="1.6"></path>
      </g><g id="fhead1_0"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba910d"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_1"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba8b08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_2"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba9743"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_3"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#b48008"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_4"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba7d08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf2">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#518325"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#385e15" stroke-width="1.6"></path>
      </g><g id="fhead2_0"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbb20c"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_1"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbaa04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_2"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbba54"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_3"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#d39b04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_4"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#db9704"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf3">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#4f9629"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#316816" stroke-width="1.6"></path>
      </g><g id="fhead3_0"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8cf0a"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_1"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8c601"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_2"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8d963"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_3"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#efb301"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_4"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8af01"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><path id="fpetal" d="M0 -14 C 7 -24 10 -42 0 -56 C -10 -42 -7 -24 0 -14 Z"></path><g id="fleaf0">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#565a1b"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#4a4714" stroke-width="1.6"></path>
      </g><g id="fhead0_0"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99700f"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_1"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#996d0b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_2"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#997432"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_3"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#95650b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_4"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99630b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf1">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#546e20"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#415214" stroke-width="1.6"></path>
      </g><g id="fhead1_0"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba910d"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_1"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba8b08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_2"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba9743"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_3"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#b48008"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_4"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba7d08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf2">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#518325"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#385e15" stroke-width="1.6"></path>
      </g><g id="fhead2_0"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbb20c"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_1"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbaa04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_2"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbba54"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_3"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#d39b04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_4"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#db9704"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf3">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#4f9629"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#316816" stroke-width="1.6"></path>
      </g><g id="fhead3_0"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8cf0a"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_1"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8c601"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_2"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8d963"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_3"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#efb301"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_4"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8af01"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><path id="fpetal" d="M0 -14 C 7 -24 10 -42 0 -56 C -10 -42 -7 -24 0 -14 Z"></path><g id="fleaf0">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#565a1b"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#4a4714" stroke-width="1.6"></path>
      </g><g id="fhead0_0"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93620d"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99700f"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99700f"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_1"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93600b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#996d0b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#996d0b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_2"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#93641f"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#997432"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#997432"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_3"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#915c0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#95650b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#95650b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead0_4"><use href="#fpetal" transform="rotate(0)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(22.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(45)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(67.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(90)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(112.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(135)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(157.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(180)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(202.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(225)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(247.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(270)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(292.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(315)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(337.5)" fill="#834d0b"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#935b0b"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#99630b"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#99630b"></use>
          <circle r="21" fill="#4f2e11"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#845a1d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf1">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#546e20"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#415214" stroke-width="1.6"></path>
      </g><g id="fhead1_0"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17b0a"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba910d"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba910d"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_1"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17808"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba8b08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba8b08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_2"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17e25"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba9743"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba9743"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_3"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ae7208"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#b48008"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#b48008"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead1_4"><use href="#fpetal" transform="rotate(0)" fill="#995c08"></use><use href="#fpetal" transform="rotate(22.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(45)" fill="#995c08"></use><use href="#fpetal" transform="rotate(67.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(90)" fill="#995c08"></use><use href="#fpetal" transform="rotate(112.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(135)" fill="#995c08"></use><use href="#fpetal" transform="rotate(157.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(180)" fill="#995c08"></use><use href="#fpetal" transform="rotate(202.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(225)" fill="#995c08"></use><use href="#fpetal" transform="rotate(247.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(270)" fill="#995c08"></use><use href="#fpetal" transform="rotate(292.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(315)" fill="#995c08"></use><use href="#fpetal" transform="rotate(337.5)" fill="#995c08"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#b17108"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#ba7d08"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#ba7d08"></use>
          <circle r="21" fill="#4d2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#9a6e22" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf2">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#518325"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#385e15" stroke-width="1.6"></path>
      </g><g id="fhead2_0"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9408"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbb20c"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbb20c"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_1"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf9004"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbaa04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbaa04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_2"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf982c"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#dbba54"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#dbba54"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_3"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cb8904"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#d39b04"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#d39b04"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead2_4"><use href="#fpetal" transform="rotate(0)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(22.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(45)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(67.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(90)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(112.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(135)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(157.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(180)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(202.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(225)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(247.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(270)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(292.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(315)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(337.5)" fill="#ae6a04"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#cf8704"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#db9704"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#db9704"></use>
          <circle r="21" fill="#4c2d10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#b18328" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fleaf3">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="#4f9629"></path>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="#316816" stroke-width="1.6"></path>
      </g><g id="fhead3_0"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaab06"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8cf0a"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8cf0a"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_1"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eaa601"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8c601"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8c601"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_2"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#eab032"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8d963"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8d963"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_3"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#e59d01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#efb301"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#efb301"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g><g id="fhead3_4"><use href="#fpetal" transform="rotate(0)" fill="#c27701"></use><use href="#fpetal" transform="rotate(22.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(45)" fill="#c27701"></use><use href="#fpetal" transform="rotate(67.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(90)" fill="#c27701"></use><use href="#fpetal" transform="rotate(112.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(135)" fill="#c27701"></use><use href="#fpetal" transform="rotate(157.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(180)" fill="#c27701"></use><use href="#fpetal" transform="rotate(202.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(225)" fill="#c27701"></use><use href="#fpetal" transform="rotate(247.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(270)" fill="#c27701"></use><use href="#fpetal" transform="rotate(292.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(315)" fill="#c27701"></use><use href="#fpetal" transform="rotate(337.5)" fill="#c27701"></use><use href="#fpetal" transform="rotate(11.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(33.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(56.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(78.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(101.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(123.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(146.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(168.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(191.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(213.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(236.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(258.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(281.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(303.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(326.25) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(348.75) scale(.93)" fill="#ea9b01"></use><use href="#fpetal" transform="rotate(5.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(30.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(56.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(82.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(107.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(133.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(159.29) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(185.00) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(210.71) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(236.43) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(262.14) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(287.86) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(313.57) scale(.8)" fill="#f8af01"></use><use href="#fpetal" transform="rotate(339.29) scale(.8)" fill="#f8af01"></use>
          <circle r="21" fill="#4a2c10"></circle>
          <circle r="21" fill="url(#fieldSeeds)"></circle>
          <circle r="21" fill="none" stroke="#c5962d" stroke-width="1.6" opacity=".8"></circle>
          <circle r="9" fill="#000" opacity=".18"></circle>
        </g></defs>
</svg>

<canvas id="sparkles" width="1878" height="931" style="width: 1878px; height: 931px;"></canvas>

<!-- ============================================================
     ESCENA 1: CAMPO DE GIRASOLES
     ============================================================ -->
<section class="scene field-scene" id="fieldScene">
  <div class="field-sky"></div>
  <div class="field-sun"></div>
  <div class="field-ground"></div>

  <div class="field-perspective">
    <div class="flowers-layer zoomed" id="flowersLayer"><div class="field-flower" style="translate: -1808.7px 438.4px -1700.2px; scale: 1.215; rotate: 1.15deg; animation-duration: 5.05s; animation-delay: -1.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1841.8px 446.8px -1706.9px; scale: 1.296; rotate: -1.1deg; animation-duration: 5.85s; animation-delay: -0.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 709px 441.6px -1539.6px; scale: 1.132; rotate: 1.78deg; animation-duration: 5.65s; animation-delay: -5.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 4061px 440.8px -3487.6px; scale: 1.075; rotate: -1.52deg; animation-duration: 4.9s; animation-delay: -5.33s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -329.8px 446.3px -2089.8px; scale: 0.937; rotate: -2.99deg; animation-duration: 4s; animation-delay: -0.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 2336.7px 447.4px -1788.3px; scale: 1.282; rotate: 3.87deg; animation-duration: 4.43s; animation-delay: -3.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -616.3px 430.5px -3451px; scale: 1.28; rotate: -0.49deg; animation-duration: 4.53s; animation-delay: -0.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -843.4px 432.8px -541.6px; scale: 1.1; rotate: -3.69deg; animation-duration: 4.39s; animation-delay: -4.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1134.7px 427.1px -1533.2px; scale: 1.201; rotate: 3.15deg; animation-duration: 4.12s; animation-delay: -3.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 2047.5px 435.2px -991.8px; scale: 0.621; rotate: 0.31deg; animation-duration: 3.98s; animation-delay: -2.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -2515.2px 452.3px -2050.7px; scale: 0.949; rotate: -2.61deg; animation-duration: 3.41s; animation-delay: -1.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -4092px 427.9px -2589.4px; scale: 0.656; rotate: 0.37deg; animation-duration: 4.29s; animation-delay: -1.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 949.8px 436.2px -1900.5px; scale: 1.33; rotate: 3.88deg; animation-duration: 5.04s; animation-delay: -1.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -4312.8px 446.1px -3245.9px; scale: 1.277; rotate: 3.48deg; animation-duration: 4.36s; animation-delay: -1.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -1056.2px 443.1px -1533.8px; scale: 1.058; rotate: -2.79deg; animation-duration: 4.02s; animation-delay: -0.19s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1094.9px 430.5px -3539.1px; scale: 0.997; rotate: 0.54deg; animation-duration: 5.89s; animation-delay: -0.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2157.1px 429.8px -1636.6px; scale: 0.825; rotate: 2.22deg; animation-duration: 5.09s; animation-delay: -0.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -2535.6px 453px -2635.8px; scale: 1.29; rotate: -0.95deg; animation-duration: 5.71s; animation-delay: -3.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1355px 430.3px -462.8px; scale: 0.911; rotate: 1.74deg; animation-duration: 4.22s; animation-delay: -5.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 507.5px 447.4px -1409px; scale: 1.213; rotate: -0.97deg; animation-duration: 5.29s; animation-delay: -3.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 2132.4px 434.1px -2092px; scale: 1.334; rotate: 0.07deg; animation-duration: 5.37s; animation-delay: -0.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 1463.7px 435.8px -682.2px; scale: 0.764; rotate: -3.32deg; animation-duration: 5.29s; animation-delay: -4.31s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 2358.3px 441.1px -2997.1px; scale: 0.763; rotate: -0.4deg; animation-duration: 5.84s; animation-delay: -1.92s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 3610.4px 437.6px -2875.3px; scale: 1.261; rotate: -2.56deg; animation-duration: 5.43s; animation-delay: -0.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 321.7px 435px -2239.4px; scale: 0.993; rotate: -3.37deg; animation-duration: 4.24s; animation-delay: -5.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 3115.7px 441px -1722.9px; scale: 0.903; rotate: -1.1deg; animation-duration: 5.04s; animation-delay: -4.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 1588.5px 449.6px -1144.4px; scale: 0.792; rotate: -2.11deg; animation-duration: 3.88s; animation-delay: -4.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -1395.6px 436.3px -590.7px; scale: 1.282; rotate: -3.46deg; animation-duration: 3.57s; animation-delay: -3.19s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 3634.5px 436.7px -2935.7px; scale: 1.314; rotate: 2.25deg; animation-duration: 4.32s; animation-delay: -0.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1515.3px 438.8px -2986.4px; scale: 0.874; rotate: 1.39deg; animation-duration: 5.49s; animation-delay: -3.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1917.4px 430px -1754px; scale: 1.106; rotate: -2.3deg; animation-duration: 3.71s; animation-delay: -1.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 1395.3px 428.9px -2595.6px; scale: 0.703; rotate: 0.07deg; animation-duration: 4.51s; animation-delay: -0.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -2265px 435.8px -1849px; scale: 1.19; rotate: 1.89deg; animation-duration: 4.91s; animation-delay: -1.47s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 346.8px 440.1px -1050.2px; scale: 1.104; rotate: -3.98deg; animation-duration: 3.56s; animation-delay: -2.04s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 500.6px 433.4px -3487.3px; scale: 1.053; rotate: 3.06deg; animation-duration: 4.8s; animation-delay: -4.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 459.7px 451.1px -2279.9px; scale: 1.041; rotate: 3.4deg; animation-duration: 5.27s; animation-delay: -2.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -3783.5px 430.8px -2908.4px; scale: 1.159; rotate: 0.34deg; animation-duration: 4.1s; animation-delay: -3.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -1528.8px 433.7px -3025.1px; scale: 1.218; rotate: -0.4deg; animation-duration: 4.33s; animation-delay: -1.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -956.1px 436.7px -2761.8px; scale: 1.245; rotate: 0.39deg; animation-duration: 3.92s; animation-delay: -2.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -350.4px 439.1px -1072.9px; scale: 0.991; rotate: 0.16deg; animation-duration: 4.01s; animation-delay: -1.16s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 265.2px 428.6px -3295.6px; scale: 1.314; rotate: -0.43deg; animation-duration: 4.26s; animation-delay: -3.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 919.9px 451.1px -998.4px; scale: 0.94; rotate: -1.63deg; animation-duration: 4.34s; animation-delay: -2.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1263.9px 441.9px -2521.4px; scale: 1.113; rotate: 2.22deg; animation-duration: 4.69s; animation-delay: -3.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 1840.4px 450.5px -813.3px; scale: 1.327; rotate: 3.55deg; animation-duration: 4.5s; animation-delay: -1.34s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 735.5px 440.4px -3565.5px; scale: 0.822; rotate: 2.48deg; animation-duration: 3.93s; animation-delay: -3.14s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 3463.8px 441.4px -2439.6px; scale: 0.642; rotate: 0.61deg; animation-duration: 3.8s; animation-delay: -2.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 522.1px 442.6px -539.3px; scale: 1.033; rotate: 2.01deg; animation-duration: 5.16s; animation-delay: -5.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 3091.6px 439.4px -2344.3px; scale: 0.894; rotate: -1.57deg; animation-duration: 5.51s; animation-delay: -1.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 532.5px 435.8px -3185.4px; scale: 1.076; rotate: 1.86deg; animation-duration: 5.35s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1383.6px 439.7px -754px; scale: 0.676; rotate: 3.83deg; animation-duration: 5.2s; animation-delay: -2.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 206px 449.6px -670.8px; scale: 1.069; rotate: 2.82deg; animation-duration: 3.75s; animation-delay: -2.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -557.5px 451.8px -414.2px; scale: 0.605; rotate: -3.92deg; animation-duration: 5.36s; animation-delay: -1.38s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 713.7px 447.4px -3590.6px; scale: 0.736; rotate: 0.65deg; animation-duration: 4.46s; animation-delay: -4.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 2831.9px 442.1px -3258.6px; scale: 0.833; rotate: 3.75deg; animation-duration: 4.01s; animation-delay: -5.25s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -4671.1px 427px -3228.5px; scale: 1.295; rotate: -3.56deg; animation-duration: 3.64s; animation-delay: -5.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1272.3px 445.3px -394.7px; scale: 0.656; rotate: 0.6deg; animation-duration: 4.67s; animation-delay: -1.57s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 4627.8px 427.9px -3383px; scale: 1.24; rotate: 1.77deg; animation-duration: 5.34s; animation-delay: -4.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -1062.1px 450.4px -2881.4px; scale: 1.181; rotate: 1.13deg; animation-duration: 4.56s; animation-delay: -3.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 1397.1px 440.1px -601.8px; scale: 0.698; rotate: 0.74deg; animation-duration: 3.42s; animation-delay: -2.32s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -3563.1px 429.7px -2912.2px; scale: 1.033; rotate: -3.31deg; animation-duration: 5.44s; animation-delay: -1.23s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 3082.2px 430.2px -3574.3px; scale: 1.038; rotate: -1.61deg; animation-duration: 4.06s; animation-delay: -1.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -729.3px 439.5px -309.6px; scale: 1.151; rotate: 0.27deg; animation-duration: 5.15s; animation-delay: -2.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 2422.4px 430px -1725px; scale: 1.03; rotate: -2.63deg; animation-duration: 4.86s; animation-delay: -0.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -3137.3px 438.7px -2778.2px; scale: 0.873; rotate: 3.79deg; animation-duration: 5.08s; animation-delay: -1.94s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 562.9px 447.9px -1018px; scale: 1.18; rotate: -1.62deg; animation-duration: 5.33s; animation-delay: -0.15s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1117px 434.3px -2330.8px; scale: 0.712; rotate: 3.94deg; animation-duration: 4.44s; animation-delay: -0.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 4888.4px 432.8px -3563.9px; scale: 1.096; rotate: 1.09deg; animation-duration: 5.79s; animation-delay: -5.52s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -3947.3px 451.8px -3135.2px; scale: 0.938; rotate: 1.18deg; animation-duration: 4.44s; animation-delay: -5.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -2138.1px 449px -1392.9px; scale: 0.667; rotate: -3.28deg; animation-duration: 5.67s; animation-delay: -0.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 2148.9px 432.1px -3285.9px; scale: 1.325; rotate: 1.33deg; animation-duration: 5.33s; animation-delay: -1.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -866.9px 443.5px -2573.7px; scale: 0.682; rotate: -2.91deg; animation-duration: 4.77s; animation-delay: -4.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 144.2px 445.6px -2546.4px; scale: 0.762; rotate: -2.83deg; animation-duration: 5.33s; animation-delay: -5.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 2320.7px 428.9px -1661.8px; scale: 1.226; rotate: -0.16deg; animation-duration: 5.2s; animation-delay: -3.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 1450.5px 438.7px -3114.3px; scale: 0.945; rotate: 1.13deg; animation-duration: 3.98s; animation-delay: -1.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1314.1px 437.1px -3222px; scale: 1.221; rotate: -3.66deg; animation-duration: 4.88s; animation-delay: -3.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 661.6px 450.3px -3549.2px; scale: 0.772; rotate: -1.77deg; animation-duration: 3.99s; animation-delay: -0.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 3877.8px 433.1px -3296.4px; scale: 0.929; rotate: 0.62deg; animation-duration: 4.69s; animation-delay: -4.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -494.3px 435.7px -667.4px; scale: 0.988; rotate: -2.2deg; animation-duration: 3.98s; animation-delay: -0.97s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -3194.9px 441.5px -1929.5px; scale: 1.12; rotate: -1.68deg; animation-duration: 3.67s; animation-delay: -5.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -783.6px 429.5px -2239.4px; scale: 0.699; rotate: -2.11deg; animation-duration: 3.57s; animation-delay: -1.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 3071.1px 447px -3301.5px; scale: 0.86; rotate: 2.17deg; animation-duration: 5.4s; animation-delay: -2.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 1178px 435.3px -1117.5px; scale: 0.92; rotate: -1.22deg; animation-duration: 5.35s; animation-delay: -3.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -3136px 450.3px -2316.7px; scale: 0.975; rotate: -1.86deg; animation-duration: 3.42s; animation-delay: -3.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 773.7px 447.8px -3187.9px; scale: 1.238; rotate: -0.72deg; animation-duration: 4.26s; animation-delay: -4.21s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1454.5px 434.4px -728.3px; scale: 0.95; rotate: -0.65deg; animation-duration: 5.11s; animation-delay: -3.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -2818.1px 448.6px -2898.6px; scale: 0.695; rotate: 3.21deg; animation-duration: 5.82s; animation-delay: -2.55s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2598px 429px -2514.5px; scale: 0.816; rotate: 2.54deg; animation-duration: 4.9s; animation-delay: -4.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1051.1px 430.1px -964.3px; scale: 0.799; rotate: -0.59deg; animation-duration: 3.5s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -616.7px 444.7px -333.8px; scale: 0.621; rotate: -0.36deg; animation-duration: 5.97s; animation-delay: -1.35s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 1273.8px 441.6px -1407px; scale: 1.265; rotate: -1.24deg; animation-duration: 4.92s; animation-delay: -3.05s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -2034.8px 451.5px -1849.9px; scale: 1.188; rotate: 3.39deg; animation-duration: 5.5s; animation-delay: -0.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -816.7px 432.9px -3474.9px; scale: 1.021; rotate: -3.68deg; animation-duration: 5.89s; animation-delay: -5.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 399.6px 444.2px -1839.4px; scale: 1.302; rotate: -3.31deg; animation-duration: 5.29s; animation-delay: -4.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1568.2px 452.3px -900.5px; scale: 0.795; rotate: 3.08deg; animation-duration: 3.84s; animation-delay: -4.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -3818.6px 429.6px -2469.8px; scale: 0.795; rotate: 1.41deg; animation-duration: 4.7s; animation-delay: -3.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 2572.4px 448.2px -1398px; scale: 0.803; rotate: 0.82deg; animation-duration: 5.66s; animation-delay: -0.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 789.3px 449.4px -2567.6px; scale: 0.662; rotate: 2.45deg; animation-duration: 4.58s; animation-delay: -3.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1991px 444.3px -1230px; scale: 1.008; rotate: 2.02deg; animation-duration: 3.5s; animation-delay: -1.25s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 1756.7px 437.4px -828.9px; scale: 1.067; rotate: 1.61deg; animation-duration: 4.61s; animation-delay: -2.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 883.4px 451.2px -3570.5px; scale: 1.238; rotate: -0.55deg; animation-duration: 5.86s; animation-delay: -0.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 1772.9px 439.8px -1547.2px; scale: 0.911; rotate: -1.58deg; animation-duration: 4.38s; animation-delay: -5.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 4007px 441px -2500.3px; scale: 1.076; rotate: 1.81deg; animation-duration: 3.52s; animation-delay: -3.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -586.5px 437.3px -354.4px; scale: 0.751; rotate: 1.23deg; animation-duration: 4.68s; animation-delay: -2.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -3037.7px 437.9px -3300.5px; scale: 1.204; rotate: -3.1deg; animation-duration: 4.03s; animation-delay: -3.97s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 882.9px 438.9px -2422.3px; scale: 1.043; rotate: 1.8deg; animation-duration: 4.39s; animation-delay: -5.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -815.2px 445.1px -1558.5px; scale: 1.163; rotate: -0.1deg; animation-duration: 5.68s; animation-delay: -2.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 2470.6px 438.1px -2033.3px; scale: 1.038; rotate: 2.47deg; animation-duration: 4.47s; animation-delay: -3.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -975.6px 450.5px -438.2px; scale: 1.345; rotate: -0.89deg; animation-duration: 4.63s; animation-delay: -0.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -1912.3px 430.4px -3049.1px; scale: 0.82; rotate: 0.81deg; animation-duration: 4s; animation-delay: -2.97s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2833px 435.1px -3207.1px; scale: 0.892; rotate: 2.64deg; animation-duration: 4.06s; animation-delay: -5.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1940.7px 444.1px -3591.8px; scale: 0.658; rotate: -0.73deg; animation-duration: 5.12s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 1251.7px 446.8px -1293px; scale: 1.094; rotate: 0.55deg; animation-duration: 3.55s; animation-delay: -4.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -4741.4px 438.6px -3482.5px; scale: 0.627; rotate: 0.84deg; animation-duration: 4.81s; animation-delay: -5.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1077.6px 442.3px -900.1px; scale: 1.271; rotate: -2.04deg; animation-duration: 5.46s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -2417.3px 431.9px -2253.8px; scale: 0.947; rotate: 2.77deg; animation-duration: 5.76s; animation-delay: -0.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 1394px 446.5px -685.8px; scale: 1.226; rotate: -2.18deg; animation-duration: 5.7s; animation-delay: -4.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1634.2px 452.3px -2185.8px; scale: 0.753; rotate: 1.84deg; animation-duration: 3.53s; animation-delay: -5.61s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2662.3px 436.3px -3510.7px; scale: 1.197; rotate: 0.23deg; animation-duration: 4.27s; animation-delay: -1.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1269.4px 451.4px -804.5px; scale: 0.815; rotate: 0.42deg; animation-duration: 3.56s; animation-delay: -4.61s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -1822.3px 427.7px -2562.5px; scale: 0.774; rotate: 1.51deg; animation-duration: 5.41s; animation-delay: -5.32s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -517.5px 446.9px -1073.7px; scale: 1.128; rotate: -1.5deg; animation-duration: 5.07s; animation-delay: -0.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -1773.7px 445.6px -735.7px; scale: 1.152; rotate: 0.57deg; animation-duration: 4.38s; animation-delay: -0.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1258.4px 440.7px -410.6px; scale: 0.96; rotate: 2.71deg; animation-duration: 4.66s; animation-delay: -3.33s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1367.8px 433.1px -2988.3px; scale: 1; rotate: 3.08deg; animation-duration: 3.7s; animation-delay: -2.58s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -4791.9px 446.8px -3118.3px; scale: 1.286; rotate: -1deg; animation-duration: 5.98s; animation-delay: -5.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -2534.6px 437.2px -2898.3px; scale: 1.144; rotate: 0.3deg; animation-duration: 5.75s; animation-delay: -0.92s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1721.4px 431.4px -2817.7px; scale: 0.708; rotate: -2.75deg; animation-duration: 5.68s; animation-delay: -2.92s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -792.9px 438.9px -478.5px; scale: 1.137; rotate: 3.96deg; animation-duration: 5.71s; animation-delay: -3.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -1447px 436.3px -2991.6px; scale: 0.979; rotate: -0.48deg; animation-duration: 3.91s; animation-delay: -4.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -902.9px 435.8px -1924.8px; scale: 0.938; rotate: -2.67deg; animation-duration: 5.96s; animation-delay: -4.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -2040.3px 431.9px -2677px; scale: 1.065; rotate: 0.97deg; animation-duration: 5.54s; animation-delay: -5.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -130.7px 452.6px -764px; scale: 0.75; rotate: -1.96deg; animation-duration: 3.71s; animation-delay: -2.02s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 530.8px 432.4px -3569.9px; scale: 1.016; rotate: 0.12deg; animation-duration: 5.68s; animation-delay: -4.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1624.3px 430.3px -3243.7px; scale: 0.763; rotate: 2.58deg; animation-duration: 5.15s; animation-delay: -1.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 187.5px 442px -894.4px; scale: 0.807; rotate: 1.4deg; animation-duration: 5.51s; animation-delay: -2.34s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1534.8px 435.6px -756.6px; scale: 0.731; rotate: -2.34deg; animation-duration: 4.12s; animation-delay: -1.23s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 2493.6px 429px -2330px; scale: 0.889; rotate: -2.36deg; animation-duration: 3.62s; animation-delay: -3.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -2548.3px 443.7px -2535.9px; scale: 1.33; rotate: -3.95deg; animation-duration: 5.26s; animation-delay: -1.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1758.5px 439.9px -2451.3px; scale: 1.166; rotate: -0.23deg; animation-duration: 5.53s; animation-delay: -0.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 951.3px 437.3px -1208.2px; scale: 1.099; rotate: -1.42deg; animation-duration: 3.53s; animation-delay: -3.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 2960.9px 447.2px -3008.4px; scale: 0.689; rotate: 3.15deg; animation-duration: 5.88s; animation-delay: -4.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 3353.9px 453px -3141px; scale: 0.737; rotate: -0.86deg; animation-duration: 5.32s; animation-delay: -5.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -3500.8px 451.3px -3330.1px; scale: 1.275; rotate: 3.07deg; animation-duration: 5.17s; animation-delay: -2.59s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1718.4px 434.2px -3035.7px; scale: 0.905; rotate: -2.59deg; animation-duration: 5.93s; animation-delay: -2.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 2220.7px 439.1px -2583.6px; scale: 1.156; rotate: -2.84deg; animation-duration: 5.11s; animation-delay: -5.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -643.3px 434.6px -831.4px; scale: 1.227; rotate: 0.26deg; animation-duration: 5.96s; animation-delay: -1.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -1269.1px 435.2px -309.6px; scale: 1.333; rotate: 2.59deg; animation-duration: 3.48s; animation-delay: -0.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1039.4px 448.2px -291.1px; scale: 1.207; rotate: 2.26deg; animation-duration: 4.14s; animation-delay: -2.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 1266px 442.3px -640.9px; scale: 1.119; rotate: 3.8deg; animation-duration: 5.02s; animation-delay: -4.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1962.7px 441.5px -1214.5px; scale: 0.929; rotate: -1.81deg; animation-duration: 3.65s; animation-delay: -5.04s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -1795.6px 432.6px -605.7px; scale: 1.132; rotate: -3.45deg; animation-duration: 4.17s; animation-delay: -3.73s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -983.5px 427.4px -1503.4px; scale: 1.183; rotate: 2.26deg; animation-duration: 5.41s; animation-delay: -3.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 3078.6px 434.5px -2887px; scale: 0.881; rotate: 2.31deg; animation-duration: 4.8s; animation-delay: -0.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -2644.7px 445.9px -1701.2px; scale: 0.742; rotate: 2.56deg; animation-duration: 3.4s; animation-delay: -2.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -2121.2px 438.6px -2764.5px; scale: 1.092; rotate: 3.27deg; animation-duration: 4.5s; animation-delay: -5.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -3030px 438.8px -2005.8px; scale: 1.025; rotate: -0.71deg; animation-duration: 5.31s; animation-delay: -1.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 240.1px 433.4px -706.5px; scale: 1.148; rotate: 2.75deg; animation-duration: 5.94s; animation-delay: -2.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1895.6px 428.4px -2315.2px; scale: 1.101; rotate: -3.41deg; animation-duration: 4.66s; animation-delay: -5.92s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 296.1px 447.5px -271.8px; scale: 1.169; rotate: 1.5deg; animation-duration: 3.44s; animation-delay: -0.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -1379.7px 427.3px -3301.4px; scale: 0.617; rotate: -2.26deg; animation-duration: 5.28s; animation-delay: -6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -827.6px 431.6px -3102.1px; scale: 0.603; rotate: 0.12deg; animation-duration: 5.61s; animation-delay: -4.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -3766.7px 435.4px -2426.9px; scale: 0.754; rotate: -3.7deg; animation-duration: 4.25s; animation-delay: -1.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1078.9px 442.1px -2091.7px; scale: 1.207; rotate: 0.62deg; animation-duration: 3.72s; animation-delay: -3.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -3995px 448.3px -3051.6px; scale: 0.604; rotate: -2.33deg; animation-duration: 4.6s; animation-delay: -1.59s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 1366.9px 435px -279.2px; scale: 1.031; rotate: 1.45deg; animation-duration: 5.09s; animation-delay: -5.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -361.3px 434.6px -826.8px; scale: 0.83; rotate: 1.3deg; animation-duration: 5.23s; animation-delay: -5.88s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 2886.2px 441.6px -3170.6px; scale: 0.986; rotate: 1.58deg; animation-duration: 4.92s; animation-delay: -2.02s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2295.4px 448px -1377.9px; scale: 1.069; rotate: 2.67deg; animation-duration: 4.19s; animation-delay: -4.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 795px 435.5px -696.2px; scale: 0.825; rotate: -0.86deg; animation-duration: 4.36s; animation-delay: -0.24s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1674.7px 441.4px -3580.8px; scale: 0.613; rotate: 0.3deg; animation-duration: 4.24s; animation-delay: -2.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 837px 436.1px -1898.5px; scale: 1.018; rotate: -2.34deg; animation-duration: 5.5s; animation-delay: -2.58s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 1484.2px 438.8px -1596.8px; scale: 0.985; rotate: -3.45deg; animation-duration: 3.61s; animation-delay: -3.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 3848.9px 450.8px -3247px; scale: 0.891; rotate: 3.99deg; animation-duration: 4.07s; animation-delay: -2.31s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1006.2px 441px -871.9px; scale: 1.022; rotate: -1.08deg; animation-duration: 5s; animation-delay: -3.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -3040.1px 441.1px -3565.7px; scale: 1.274; rotate: -2.98deg; animation-duration: 5.76s; animation-delay: -4.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -2258.3px 440.5px -2097.6px; scale: 1.019; rotate: -0.93deg; animation-duration: 5.18s; animation-delay: -4.31s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 4355.7px 449.6px -2870.1px; scale: 1.153; rotate: 1.51deg; animation-duration: 4.9s; animation-delay: -5.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -1727.9px 436.4px -1857.3px; scale: 1.07; rotate: -2.39deg; animation-duration: 4.62s; animation-delay: -3.55s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -2860.7px 428.8px -1713px; scale: 1.228; rotate: 1.61deg; animation-duration: 4.91s; animation-delay: -2.73s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 682.7px 430.6px -1131.3px; scale: 1.32; rotate: 2.29deg; animation-duration: 5s; animation-delay: -5.14s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 1376.3px 443.9px -773.2px; scale: 0.759; rotate: -1.58deg; animation-duration: 4.36s; animation-delay: -3.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -1542px 443.8px -3107.8px; scale: 0.641; rotate: 0.82deg; animation-duration: 4.22s; animation-delay: -1.73s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 2977.6px 443px -2325.5px; scale: 1.136; rotate: 0deg; animation-duration: 4.6s; animation-delay: -3.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 1221.5px 440.5px -1002.9px; scale: 1.151; rotate: -2.92deg; animation-duration: 5s; animation-delay: -5.47s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -492.8px 448.9px -260.7px; scale: 0.784; rotate: 2.89deg; animation-duration: 5.7s; animation-delay: -3.24s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 1630.2px 435.5px -1987.3px; scale: 1.314; rotate: -0.88deg; animation-duration: 5.23s; animation-delay: -4.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -1273px 433px -1204.9px; scale: 0.799; rotate: -3.94deg; animation-duration: 4.28s; animation-delay: -5.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -679.9px 438.6px -435.9px; scale: 1.252; rotate: -3.42deg; animation-duration: 4.1s; animation-delay: -0.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -436.7px 427.6px -2010.7px; scale: 1.195; rotate: 3.91deg; animation-duration: 3.86s; animation-delay: -3.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -244.1px 431.2px -1550.9px; scale: 0.846; rotate: -3.09deg; animation-duration: 4.84s; animation-delay: -5.37s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -2501.8px 430.5px -1574.4px; scale: 0.669; rotate: 0.59deg; animation-duration: 4.62s; animation-delay: -5.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 3360.9px 429.4px -2244.8px; scale: 1.134; rotate: 1.54deg; animation-duration: 5.74s; animation-delay: -5.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1294px 431.6px -415.3px; scale: 1.084; rotate: -0.1deg; animation-duration: 3.79s; animation-delay: -3.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -1165px 442.4px -3426.1px; scale: 1.25; rotate: -3.25deg; animation-duration: 5.66s; animation-delay: -4.5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -4728.9px 450.9px -3262.2px; scale: 0.658; rotate: -3.73deg; animation-duration: 4.71s; animation-delay: -4.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 491.7px 427.4px -1540.7px; scale: 0.98; rotate: -1.92deg; animation-duration: 4.46s; animation-delay: -3.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -3484.5px 437.3px -3402.8px; scale: 1.246; rotate: 1.81deg; animation-duration: 5.91s; animation-delay: -4.21s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2312px 440.5px -1716.3px; scale: 0.67; rotate: -3.03deg; animation-duration: 4.5s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -3339.8px 449.4px -3353.8px; scale: 0.84; rotate: 2.02deg; animation-duration: 3.66s; animation-delay: -5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 962.1px 449.7px -503.7px; scale: 1.076; rotate: 3.2deg; animation-duration: 5.22s; animation-delay: -4.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -1209.5px 449.6px -933.5px; scale: 0.618; rotate: 2.9deg; animation-duration: 5.13s; animation-delay: -4.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 205.7px 449.8px -1771.1px; scale: 0.656; rotate: -2.39deg; animation-duration: 5.02s; animation-delay: -2.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1277.1px 430.5px -3500.3px; scale: 0.797; rotate: -0.94deg; animation-duration: 5.21s; animation-delay: -5.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -3331.2px 437.2px -2308.1px; scale: 0.809; rotate: 3.94deg; animation-duration: 5.02s; animation-delay: -5.37s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -3283.3px 446.6px -2286.2px; scale: 0.709; rotate: -1.57deg; animation-duration: 3.97s; animation-delay: -4.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -1401.3px 445.4px -1324.2px; scale: 0.785; rotate: 0.04deg; animation-duration: 5.75s; animation-delay: -1.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 3371.9px 427.7px -1999px; scale: 0.836; rotate: -2.83deg; animation-duration: 3.75s; animation-delay: -2.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1812px 439.8px -2139.6px; scale: 1.303; rotate: 2.74deg; animation-duration: 5.15s; animation-delay: -1.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 5317.6px 438.3px -3548.1px; scale: 0.887; rotate: 3.06deg; animation-duration: 4.58s; animation-delay: -3.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1238.1px 434.4px -1316.1px; scale: 0.75; rotate: -2.94deg; animation-duration: 5.97s; animation-delay: -1.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 1740.9px 451.3px -1461.1px; scale: 0.608; rotate: 1.1deg; animation-duration: 5.44s; animation-delay: -2.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -828.3px 437.7px -3454.3px; scale: 1.053; rotate: 2.5deg; animation-duration: 5.3s; animation-delay: -2.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1279.3px 431.6px -2425.4px; scale: 0.742; rotate: -2.48deg; animation-duration: 5.29s; animation-delay: -4.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1545.2px 449px -1526px; scale: 0.896; rotate: 3.23deg; animation-duration: 4.21s; animation-delay: -1.05s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -2401.6px 436.1px -1449.6px; scale: 0.928; rotate: -2.39deg; animation-duration: 3.51s; animation-delay: -4.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 501.6px 428.9px -549.8px; scale: 1.063; rotate: 2.71deg; animation-duration: 4.26s; animation-delay: -3.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -758.5px 438.7px -3179.4px; scale: 0.735; rotate: -1.2deg; animation-duration: 5.81s; animation-delay: -1.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 4823.2px 428.2px -3467.4px; scale: 0.703; rotate: -3.69deg; animation-duration: 4.87s; animation-delay: -0.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 602.1px 429.5px -1893.2px; scale: 0.961; rotate: -1.54deg; animation-duration: 4.59s; animation-delay: -2.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 1539.5px 427.3px -2991.4px; scale: 0.792; rotate: 2.09deg; animation-duration: 5.66s; animation-delay: -4.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -784px 447.9px -2724.4px; scale: 0.931; rotate: -2.87deg; animation-duration: 5.21s; animation-delay: -4.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 2084.9px 447.9px -1935.1px; scale: 1.24; rotate: -0.12deg; animation-duration: 5.2s; animation-delay: -4.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 3719px 447px -2263.9px; scale: 1.071; rotate: -2.07deg; animation-duration: 5.52s; animation-delay: -0.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -2115.1px 435.2px -3556.8px; scale: 1.154; rotate: 1.85deg; animation-duration: 5.65s; animation-delay: -5.29s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -4813.8px 433.6px -3567.6px; scale: 1.254; rotate: -2.78deg; animation-duration: 5.65s; animation-delay: -5.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -3518.4px 452.5px -2284.6px; scale: 0.65; rotate: 0.25deg; animation-duration: 4.71s; animation-delay: -3.19s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -2083px 433.6px -986px; scale: 0.626; rotate: 2.42deg; animation-duration: 5.86s; animation-delay: -1.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -3727.2px 452.7px -2973px; scale: 1.266; rotate: -3.72deg; animation-duration: 4.26s; animation-delay: -5.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1600.6px 452.6px -2657.2px; scale: 1.265; rotate: 3.63deg; animation-duration: 3.7s; animation-delay: -0.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1316.2px 441.5px -436.1px; scale: 0.819; rotate: -1.01deg; animation-duration: 4.23s; animation-delay: -5.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 3192.6px 443.4px -3362.4px; scale: 1.182; rotate: -3.84deg; animation-duration: 5.95s; animation-delay: -3.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1398.1px 447.6px -3150.6px; scale: 0.962; rotate: 0.86deg; animation-duration: 4.53s; animation-delay: -4.94s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1002px 435.4px -3440px; scale: 0.764; rotate: -3.4deg; animation-duration: 4.05s; animation-delay: -1.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 447px 432.6px -1530.4px; scale: 0.916; rotate: 1.68deg; animation-duration: 3.59s; animation-delay: -5.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -830.7px 449.1px -1579.2px; scale: 1.332; rotate: 3.29deg; animation-duration: 5.01s; animation-delay: -3.88s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 788.6px 430.5px -660.7px; scale: 0.944; rotate: 0.03deg; animation-duration: 5.12s; animation-delay: -5.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -797.6px 439.9px -2995.3px; scale: 0.815; rotate: 2.15deg; animation-duration: 5.56s; animation-delay: -1.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2051.9px 445.7px -1457.8px; scale: 1.259; rotate: 0.04deg; animation-duration: 4.86s; animation-delay: -4.43s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -1503.2px 438px -3547.1px; scale: 0.774; rotate: -3.34deg; animation-duration: 4.25s; animation-delay: -3.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 2070.5px 427.2px -2504px; scale: 1.203; rotate: -3.78deg; animation-duration: 3.95s; animation-delay: -3.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 685.1px 440.6px -2872.9px; scale: 1.249; rotate: 3.19deg; animation-duration: 4.55s; animation-delay: -0.96s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 3658.5px 445.5px -3532px; scale: 0.625; rotate: 0.27deg; animation-duration: 5.38s; animation-delay: -4.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 2856.5px 436.1px -3247.8px; scale: 1.281; rotate: -3.24deg; animation-duration: 5.8s; animation-delay: -1.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 3709.2px 448.7px -3515px; scale: 1.051; rotate: 2.57deg; animation-duration: 3.71s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -433.7px 451.6px -3447.7px; scale: 1.185; rotate: 0.19deg; animation-duration: 3.95s; animation-delay: -1.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 323.2px 429.3px -739.3px; scale: 0.88; rotate: 1.01deg; animation-duration: 4.36s; animation-delay: -2.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 153.9px 449px -3531.5px; scale: 1.275; rotate: 3.54deg; animation-duration: 4.6s; animation-delay: -4.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -784.3px 431.3px -668.6px; scale: 1.174; rotate: 1.47deg; animation-duration: 5.06s; animation-delay: -2.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1660.1px 428.6px -1371.7px; scale: 0.695; rotate: 2.13deg; animation-duration: 3.87s; animation-delay: -3.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 4728.2px 432.3px -3591.6px; scale: 0.75; rotate: 0.14deg; animation-duration: 5.59s; animation-delay: -4.61s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 508.8px 432.9px -3582.9px; scale: 0.659; rotate: -0.85deg; animation-duration: 5.16s; animation-delay: -1.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 2584.6px 439.6px -3178.9px; scale: 0.902; rotate: 0.62deg; animation-duration: 3.41s; animation-delay: -3.21s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -3361px 443.7px -3526.8px; scale: 0.696; rotate: -2.84deg; animation-duration: 5.4s; animation-delay: -4.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2210.7px 428px -2600.3px; scale: 1.266; rotate: 1.85deg; animation-duration: 4.09s; animation-delay: -3.13s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -2447.8px 429px -1666.9px; scale: 0.843; rotate: 3.46deg; animation-duration: 5.28s; animation-delay: -0.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 4351.1px 435.4px -3510.1px; scale: 1.315; rotate: 3.27deg; animation-duration: 5.52s; animation-delay: -5.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -3974.9px 440.2px -2762.5px; scale: 1.346; rotate: 1deg; animation-duration: 5.98s; animation-delay: -4.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 4799.9px 442.2px -3554px; scale: 1.215; rotate: -3.84deg; animation-duration: 5.24s; animation-delay: -3.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 3861.9px 435.3px -3185.7px; scale: 0.69; rotate: -0.66deg; animation-duration: 5.75s; animation-delay: -5.59s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -568.7px 435.5px -1193.9px; scale: 0.731; rotate: -0.78deg; animation-duration: 4.03s; animation-delay: -1.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 1434.9px 435.9px -2127.3px; scale: 0.964; rotate: -1.56deg; animation-duration: 4.15s; animation-delay: -5.96s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 2042.4px 427.9px -2585px; scale: 1.27; rotate: -1.54deg; animation-duration: 4.76s; animation-delay: -3.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -3606px 430px -2863.1px; scale: 1.115; rotate: 0.87deg; animation-duration: 5.94s; animation-delay: -5.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1713.5px 446.5px -2690.4px; scale: 0.762; rotate: 0.47deg; animation-duration: 3.94s; animation-delay: -5.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -267.9px 442.5px -310.8px; scale: 1.108; rotate: -0.84deg; animation-duration: 4.44s; animation-delay: -5.25s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -2196.1px 430.4px -3345.7px; scale: 0.957; rotate: 3.57deg; animation-duration: 3.53s; animation-delay: -3.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -4211.4px 430.9px -3517.9px; scale: 1.046; rotate: 0.63deg; animation-duration: 4.35s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -3022.7px 435px -1989px; scale: 0.649; rotate: -0.48deg; animation-duration: 3.82s; animation-delay: -5.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1562.7px 428.1px -540.1px; scale: 1.067; rotate: -0.16deg; animation-duration: 6s; animation-delay: -5.31s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 2079.8px 437.1px -2131.9px; scale: 1.293; rotate: -1.61deg; animation-duration: 3.59s; animation-delay: -5.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -2443.6px 447.2px -1643.5px; scale: 0.982; rotate: 0.32deg; animation-duration: 4.61s; animation-delay: -5.13s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 2743.3px 449.5px -2100.9px; scale: 1.282; rotate: -3.18deg; animation-duration: 5.44s; animation-delay: -5.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -510.6px 445.2px -838.2px; scale: 1.309; rotate: 1.73deg; animation-duration: 4.02s; animation-delay: -5.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1480.5px 451.7px -2034.1px; scale: 0.815; rotate: -1.78deg; animation-duration: 3.82s; animation-delay: -0.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1082.8px 449.6px -2145.2px; scale: 0.87; rotate: 3.79deg; animation-duration: 3.88s; animation-delay: -0.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1299.7px 450.9px -827.1px; scale: 0.625; rotate: -3.9deg; animation-duration: 5.05s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 2630.8px 430px -2628.5px; scale: 0.946; rotate: -0.35deg; animation-duration: 4.69s; animation-delay: -3.58s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 549.2px 442.7px -358.8px; scale: 0.683; rotate: -1.43deg; animation-duration: 3.44s; animation-delay: -1.38s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 372px 447.4px -915.3px; scale: 0.746; rotate: 3.44deg; animation-duration: 4.29s; animation-delay: -5.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -1661.7px 432.5px -701.2px; scale: 0.851; rotate: 3.31deg; animation-duration: 3.55s; animation-delay: -5.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 982.2px 436.9px -2876.3px; scale: 0.619; rotate: -1.76deg; animation-duration: 4.18s; animation-delay: -5.21s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1281px 447.2px -263.3px; scale: 1.128; rotate: 2.08deg; animation-duration: 5.68s; animation-delay: -1.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 2142.1px 430.6px -2995.3px; scale: 0.848; rotate: -1.05deg; animation-duration: 5.03s; animation-delay: -3.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 3400.2px 442.8px -3523px; scale: 1.028; rotate: 1.07deg; animation-duration: 4.89s; animation-delay: -4.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 1224.8px 439.4px -3272px; scale: 0.68; rotate: 3.7deg; animation-duration: 3.5s; animation-delay: -1.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -3313.3px 428.5px -2885.8px; scale: 1.025; rotate: -2.12deg; animation-duration: 4.81s; animation-delay: -2.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -155px 445.3px -1485.9px; scale: 0.685; rotate: 1.13deg; animation-duration: 4.06s; animation-delay: -3.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 2101.8px 436.3px -879px; scale: 0.995; rotate: -0.71deg; animation-duration: 4.79s; animation-delay: -3.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 2643.9px 436.8px -2088.8px; scale: 1.263; rotate: -3.59deg; animation-duration: 4.92s; animation-delay: -0.53s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1592.5px 438.1px -1615.8px; scale: 1.12; rotate: -0.68deg; animation-duration: 5.88s; animation-delay: -3.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 1776.1px 447.4px -719.7px; scale: 1.199; rotate: -0.15deg; animation-duration: 3.46s; animation-delay: -5.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 1673.8px 428.5px -2051.5px; scale: 0.93; rotate: 2.73deg; animation-duration: 5.61s; animation-delay: -4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 965.1px 443.7px -2057.7px; scale: 1.118; rotate: -1.04deg; animation-duration: 4.78s; animation-delay: -4.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2528.9px 450.9px -2956.6px; scale: 0.789; rotate: -2.53deg; animation-duration: 5.87s; animation-delay: -2.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2198.2px 448.4px -2520px; scale: 0.935; rotate: 0.13deg; animation-duration: 4.28s; animation-delay: -0.42s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -3871.8px 429.5px -2801.3px; scale: 1.293; rotate: -3.44deg; animation-duration: 5.45s; animation-delay: -4.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1155.1px 439.8px -2129.2px; scale: 0.69; rotate: 2.8deg; animation-duration: 3.62s; animation-delay: -4.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 3189.5px 440.2px -3095.7px; scale: 1.222; rotate: -2.73deg; animation-duration: 5.95s; animation-delay: -5.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2667.4px 443.9px -1845.3px; scale: 0.884; rotate: 0.37deg; animation-duration: 5.97s; animation-delay: -2.04s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -499px 431.7px -1946.2px; scale: 0.691; rotate: -1.54deg; animation-duration: 5.51s; animation-delay: -0.05s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -4446.4px 442.4px -2919.6px; scale: 1.257; rotate: 3.81deg; animation-duration: 5.2s; animation-delay: -3.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 3011.8px 430.1px -3446.1px; scale: 0.824; rotate: -0.64deg; animation-duration: 5.91s; animation-delay: -2.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 4535.6px 439.1px -3501.5px; scale: 0.709; rotate: 1.95deg; animation-duration: 5.03s; animation-delay: -3.29s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 2261.5px 427.4px -3459.4px; scale: 0.796; rotate: -0.67deg; animation-duration: 4.36s; animation-delay: -5.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 492.3px 429.4px -618.9px; scale: 0.617; rotate: -3.53deg; animation-duration: 4.65s; animation-delay: -4.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1781.9px 434.5px -2059.1px; scale: 1.228; rotate: 3.26deg; animation-duration: 4.74s; animation-delay: -3.15s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -5093.7px 427px -3430.3px; scale: 1.017; rotate: 3.56deg; animation-duration: 5.83s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -3500.4px 429.6px -2029.9px; scale: 1.034; rotate: 1.41deg; animation-duration: 4.72s; animation-delay: -5.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 1277.9px 449px -260.4px; scale: 1.08; rotate: -1.32deg; animation-duration: 3.74s; animation-delay: -5.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -833.7px 437.1px -3283.7px; scale: 1.275; rotate: -2.55deg; animation-duration: 5.55s; animation-delay: -1.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1249.9px 435.3px -2612.7px; scale: 1.293; rotate: 0.63deg; animation-duration: 3.9s; animation-delay: -0.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1322.2px 451.7px -1863.7px; scale: 0.857; rotate: -0.22deg; animation-duration: 5.93s; animation-delay: -3.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 479.9px 439.7px -2415px; scale: 0.748; rotate: -0.1deg; animation-duration: 3.55s; animation-delay: -1.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -2362.2px 430.7px -3196px; scale: 0.866; rotate: 3.88deg; animation-duration: 4.85s; animation-delay: -1.28s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1561.2px 429.1px -2290.7px; scale: 0.673; rotate: 0.02deg; animation-duration: 4.91s; animation-delay: -0.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -2126.5px 447.1px -1210.1px; scale: 1.002; rotate: 0.61deg; animation-duration: 3.48s; animation-delay: -5.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1316.8px 430px -2743.1px; scale: 1.171; rotate: -3.82deg; animation-duration: 3.79s; animation-delay: -5.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -2924px 440.9px -1974px; scale: 0.928; rotate: -1.79deg; animation-duration: 4.76s; animation-delay: -2.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -1611.2px 445.1px -606.3px; scale: 0.605; rotate: -3deg; animation-duration: 3.86s; animation-delay: -2.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -228px 431.8px -2578.8px; scale: 0.645; rotate: -1.96deg; animation-duration: 5.87s; animation-delay: -3.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 186.2px 434.1px -885.1px; scale: 0.961; rotate: 0.75deg; animation-duration: 3.43s; animation-delay: -3.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1435.9px 442.2px -3433.7px; scale: 0.689; rotate: 0.05deg; animation-duration: 5.92s; animation-delay: -3.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 627.8px 437.6px -532.4px; scale: 0.806; rotate: 3.98deg; animation-duration: 5.45s; animation-delay: -2.38s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 3494.2px 435.6px -2944.3px; scale: 1.024; rotate: 0.25deg; animation-duration: 4.63s; animation-delay: -4.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1034.5px 449.4px -972.8px; scale: 0.773; rotate: -2.92deg; animation-duration: 5.85s; animation-delay: -4.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 2904.1px 427.6px -1726.3px; scale: 1.129; rotate: 2.34deg; animation-duration: 5.7s; animation-delay: -2.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -645.4px 429.6px -3503.5px; scale: 1.172; rotate: 1.57deg; animation-duration: 3.91s; animation-delay: -0.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 3167px 431.6px -2279.3px; scale: 1.283; rotate: -3.49deg; animation-duration: 3.52s; animation-delay: -2.28s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -3296.8px 448.4px -3496px; scale: 1.34; rotate: -0.63deg; animation-duration: 3.7s; animation-delay: -0.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 939.4px 432.2px -3597.1px; scale: 0.924; rotate: -1.66deg; animation-duration: 5.28s; animation-delay: -1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 3259.7px 448.2px -2767.2px; scale: 1.067; rotate: -1.01deg; animation-duration: 4.84s; animation-delay: -4.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 3713.8px 447.8px -2852.9px; scale: 0.748; rotate: 0.56deg; animation-duration: 4.9s; animation-delay: -0.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -1321.6px 438.5px -1777.9px; scale: 0.987; rotate: 1.13deg; animation-duration: 5.31s; animation-delay: -3.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 495.9px 428.5px -3059.5px; scale: 1.25; rotate: 3.45deg; animation-duration: 4.05s; animation-delay: -3.15s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -820.3px 443.8px -3591.6px; scale: 1.339; rotate: -0.18deg; animation-duration: 5.03s; animation-delay: -3.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 2404.5px 439.1px -1367px; scale: 1.064; rotate: -3.39deg; animation-duration: 5.53s; animation-delay: -3.59s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 1534.9px 431.9px -1624.8px; scale: 0.98; rotate: 1.24deg; animation-duration: 5.85s; animation-delay: -5.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -360.9px 451.4px -1646.5px; scale: 1.21; rotate: -1.2deg; animation-duration: 4.65s; animation-delay: -0.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -1448px 450.3px -3443.9px; scale: 0.629; rotate: -3.18deg; animation-duration: 4.09s; animation-delay: -0.58s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 1701.4px 434.6px -2223.2px; scale: 0.624; rotate: -0.3deg; animation-duration: 3.81s; animation-delay: -3.15s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -2216.2px 434.7px -2951.5px; scale: 0.977; rotate: 2.71deg; animation-duration: 3.59s; animation-delay: -3.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -1205.2px 451.2px -3045.9px; scale: 0.796; rotate: 3.85deg; animation-duration: 4.93s; animation-delay: -5.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 3410.6px 443.7px -2355.7px; scale: 0.823; rotate: -3.44deg; animation-duration: 4.65s; animation-delay: -4.01s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -2517px 437.9px -3241.8px; scale: 0.987; rotate: 0.48deg; animation-duration: 5.65s; animation-delay: -4.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -4741.5px 436.3px -3053.6px; scale: 1.208; rotate: 2.76deg; animation-duration: 4.53s; animation-delay: -4.89s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1900.4px 436.8px -2657.6px; scale: 0.819; rotate: 3.52deg; animation-duration: 4.26s; animation-delay: -4.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 1374.6px 446.1px -442px; scale: 0.779; rotate: 3.31deg; animation-duration: 5.16s; animation-delay: -4.96s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 1385px 433.2px -415.4px; scale: 1.224; rotate: 1.48deg; animation-duration: 3.84s; animation-delay: -1.52s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -2206.1px 446.2px -1119.2px; scale: 1.273; rotate: -3.18deg; animation-duration: 5.36s; animation-delay: -5.88s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 2535.1px 438.8px -2617.2px; scale: 1.262; rotate: -0.28deg; animation-duration: 4.51s; animation-delay: -0.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -1405.4px 451.2px -2352.1px; scale: 1.275; rotate: -1.33deg; animation-duration: 3.64s; animation-delay: -5.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -2913.7px 443.2px -3298.2px; scale: 0.668; rotate: 0.94deg; animation-duration: 4.61s; animation-delay: -4.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1201.1px 440.7px -1151.1px; scale: 0.773; rotate: 2.47deg; animation-duration: 3.93s; animation-delay: -0.01s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 2225.8px 430px -1215.9px; scale: 1.193; rotate: 3.69deg; animation-duration: 4.71s; animation-delay: -2.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -3090px 440.5px -2824.6px; scale: 1.268; rotate: 3.51deg; animation-duration: 5.3s; animation-delay: -0.53s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 1039.7px 432.3px -2703.4px; scale: 1.066; rotate: -0.91deg; animation-duration: 4.57s; animation-delay: -5.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 3362.7px 453px -3436.4px; scale: 1.162; rotate: 0.74deg; animation-duration: 3.48s; animation-delay: -5.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 2023.3px 438.6px -1307.7px; scale: 1.149; rotate: -1.5deg; animation-duration: 3.55s; animation-delay: -2.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -150.3px 440.1px -1604.3px; scale: 1.054; rotate: -1.93deg; animation-duration: 3.98s; animation-delay: -5.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -2155px 452.1px -3365.7px; scale: 0.877; rotate: 3.17deg; animation-duration: 4.32s; animation-delay: -1.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1164.2px 429.3px -3362.9px; scale: 1.086; rotate: 0.81deg; animation-duration: 4.05s; animation-delay: -1.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1731.7px 451px -2317.6px; scale: 1.176; rotate: 0.42deg; animation-duration: 5.61s; animation-delay: -2.97s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -1901.4px 441.1px -1322.4px; scale: 1.038; rotate: 2.47deg; animation-duration: 4.75s; animation-delay: -5.04s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -279.3px 430.2px -457px; scale: 1.133; rotate: -2.34deg; animation-duration: 5.48s; animation-delay: -3.01s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -586.6px 436.9px -322.4px; scale: 0.727; rotate: -1.33deg; animation-duration: 5.39s; animation-delay: -2.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -1459.1px 448.2px -1635.7px; scale: 0.834; rotate: -0.2deg; animation-duration: 3.44s; animation-delay: -0.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -3841.1px 430.9px -2843.3px; scale: 1.165; rotate: 2.12deg; animation-duration: 3.45s; animation-delay: -3.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1566.7px 435.5px -650.3px; scale: 1.168; rotate: 3.49deg; animation-duration: 3.59s; animation-delay: -1.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 884.1px 432px -1774.8px; scale: 1.298; rotate: -1.72deg; animation-duration: 5.1s; animation-delay: -0.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 1825.9px 442.5px -1311.7px; scale: 1.197; rotate: -2.35deg; animation-duration: 5.6s; animation-delay: -4.53s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -5163.7px 432.3px -3525.7px; scale: 1.013; rotate: -3.89deg; animation-duration: 3.5s; animation-delay: -5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 1595px 439.4px -1162.1px; scale: 0.769; rotate: 0deg; animation-duration: 4.62s; animation-delay: -3.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 607.4px 430.8px -535.8px; scale: 1.109; rotate: 1.08deg; animation-duration: 5.42s; animation-delay: -3.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 939.1px 433.7px -1024.5px; scale: 0.755; rotate: 3.19deg; animation-duration: 6s; animation-delay: -5.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 2492.6px 429.8px -1277.1px; scale: 0.968; rotate: -1.61deg; animation-duration: 4.43s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 3924px 447.5px -3226px; scale: 0.821; rotate: -3.43deg; animation-duration: 4.79s; animation-delay: -3.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2811.2px 442.4px -3542.2px; scale: 1.137; rotate: 0.36deg; animation-duration: 4.65s; animation-delay: -3.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -3664.3px 428.8px -3525.3px; scale: 0.749; rotate: 0.78deg; animation-duration: 5.89s; animation-delay: -3.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 782.7px 445.9px -969.6px; scale: 1.099; rotate: 1.85deg; animation-duration: 4.75s; animation-delay: -1.01s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -2455.7px 430.5px -3169.9px; scale: 0.761; rotate: 2.55deg; animation-duration: 4.58s; animation-delay: -5.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -604.7px 429.9px -676.1px; scale: 1.282; rotate: -0.75deg; animation-duration: 3.71s; animation-delay: -5.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 1369.1px 449.7px -3551.8px; scale: 1.185; rotate: -1.77deg; animation-duration: 4.75s; animation-delay: -3.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -3453.8px 439.1px -3477.8px; scale: 0.844; rotate: -2.5deg; animation-duration: 5.43s; animation-delay: -3.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 688.3px 448.5px -515.9px; scale: 1.034; rotate: -2.86deg; animation-duration: 3.88s; animation-delay: -4.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 716.8px 441.5px -2058.7px; scale: 0.658; rotate: 1.05deg; animation-duration: 3.5s; animation-delay: -4.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -2876.3px 439.4px -1676.7px; scale: 1.16; rotate: -1.85deg; animation-duration: 5.91s; animation-delay: -2.59s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 2679.3px 450.3px -2858px; scale: 1.219; rotate: 2.64deg; animation-duration: 3.99s; animation-delay: -1.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -2351.1px 447.5px -1078px; scale: 1.047; rotate: -0.74deg; animation-duration: 3.44s; animation-delay: -1.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1071.7px 446px -494.3px; scale: 0.908; rotate: -2.68deg; animation-duration: 5.33s; animation-delay: -4.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 507.1px 427.2px -1977.8px; scale: 0.723; rotate: 2.88deg; animation-duration: 3.79s; animation-delay: -3.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 1391.1px 448.5px -1115.7px; scale: 0.769; rotate: -1.49deg; animation-duration: 3.47s; animation-delay: -2.5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -3428.8px 435.9px -2016px; scale: 0.674; rotate: -3.93deg; animation-duration: 5.2s; animation-delay: -0.05s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -1371.8px 429.2px -866.5px; scale: 0.607; rotate: -0.08deg; animation-duration: 5.45s; animation-delay: -0.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -308.9px 429.5px -613.6px; scale: 1.096; rotate: -3.86deg; animation-duration: 5.3s; animation-delay: -3.04s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 4060.9px 438.9px -2595.2px; scale: 1.065; rotate: -3.54deg; animation-duration: 4.09s; animation-delay: -0.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 2891.6px 427.3px -3250px; scale: 0.703; rotate: -3.67deg; animation-duration: 4.19s; animation-delay: -3.24s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2014.2px 430.6px -809.9px; scale: 1.098; rotate: 1.3deg; animation-duration: 5.7s; animation-delay: -4.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -928.2px 438.9px -2962.6px; scale: 1.082; rotate: 1.56deg; animation-duration: 4.97s; animation-delay: -0.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2683.1px 433.2px -2121.4px; scale: 0.797; rotate: -1.73deg; animation-duration: 4.21s; animation-delay: -2.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2341.5px 441.9px -3011.8px; scale: 0.643; rotate: -1.26deg; animation-duration: 4.16s; animation-delay: -4.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -2744.4px 442px -2968.2px; scale: 0.669; rotate: 1.81deg; animation-duration: 4.89s; animation-delay: -1.64s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 3639.6px 429.7px -2179.7px; scale: 0.807; rotate: 3.01deg; animation-duration: 5s; animation-delay: -3.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 1910.7px 450.2px -2264.9px; scale: 1.029; rotate: -2.14deg; animation-duration: 4.85s; animation-delay: -4.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 648.8px 437.8px -1448.6px; scale: 1.203; rotate: -3.34deg; animation-duration: 5.14s; animation-delay: -1.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 329.6px 446.2px -1768.8px; scale: 0.855; rotate: -1.11deg; animation-duration: 4.19s; animation-delay: -0.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 2739.8px 432.2px -1696.6px; scale: 1.33; rotate: 1.09deg; animation-duration: 4.88s; animation-delay: -5.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -3725.2px 434px -3257.9px; scale: 1.19; rotate: -3.2deg; animation-duration: 3.88s; animation-delay: -4.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 765px 449.7px -389.3px; scale: 0.917; rotate: -0.77deg; animation-duration: 3.47s; animation-delay: -1.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1627.4px 429.9px -482.8px; scale: 0.68; rotate: 1.54deg; animation-duration: 4.47s; animation-delay: -3.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -2860.5px 452.3px -2825.7px; scale: 0.819; rotate: 1.5deg; animation-duration: 3.78s; animation-delay: -4.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 2457.3px 448.9px -2296.4px; scale: 0.692; rotate: -3.27deg; animation-duration: 3.69s; animation-delay: -3.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1394.8px 443.1px -498.1px; scale: 0.677; rotate: 2.27deg; animation-duration: 4.57s; animation-delay: -2.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -363.2px 436.1px -1761.8px; scale: 0.639; rotate: 2.61deg; animation-duration: 4.5s; animation-delay: -2.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -3627.4px 450.6px -3542.2px; scale: 0.781; rotate: 2.76deg; animation-duration: 5.54s; animation-delay: -1.71s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -2389.5px 440.8px -2141.9px; scale: 1.292; rotate: -0.11deg; animation-duration: 5.49s; animation-delay: -2.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -1333.3px 452px -2700.6px; scale: 0.725; rotate: 2.99deg; animation-duration: 5.38s; animation-delay: -1.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -372.3px 446.1px -3428.6px; scale: 1.229; rotate: -2.52deg; animation-duration: 5.8s; animation-delay: -5.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 226.7px 430.5px -3329px; scale: 0.929; rotate: 1.76deg; animation-duration: 3.84s; animation-delay: -3.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 737.8px 447.4px -811.1px; scale: 0.705; rotate: 0.05deg; animation-duration: 3.58s; animation-delay: -5.14s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -2159.4px 451.7px -3386.4px; scale: 1.063; rotate: -3.23deg; animation-duration: 4.56s; animation-delay: -5.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -2518.5px 434.3px -2981.9px; scale: 0.69; rotate: -1.01deg; animation-duration: 4.59s; animation-delay: -0.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1315.2px 435.5px -872.5px; scale: 1.28; rotate: 3.96deg; animation-duration: 3.51s; animation-delay: -1.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 166.4px 438.1px -1975.4px; scale: 0.753; rotate: -3.38deg; animation-duration: 5.84s; animation-delay: -1.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 4318.3px 436.1px -3282.6px; scale: 1.108; rotate: 2.2deg; animation-duration: 4.78s; animation-delay: -5.82s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 4549.4px 431.9px -3595.1px; scale: 1.282; rotate: -3.87deg; animation-duration: 4.05s; animation-delay: -3.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1408.3px 431.1px -2363.8px; scale: 1.207; rotate: 2.5deg; animation-duration: 5.17s; animation-delay: -1.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 4365.3px 427.2px -2816.5px; scale: 1.035; rotate: -2.61deg; animation-duration: 5.48s; animation-delay: -0.23s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 357.9px 427.9px -2912.7px; scale: 1.295; rotate: -0.9deg; animation-duration: 4.16s; animation-delay: -1.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 244.8px 429.9px -2263.8px; scale: 0.736; rotate: -2.37deg; animation-duration: 4.71s; animation-delay: -4.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 721.8px 435.4px -2919.2px; scale: 0.983; rotate: 3.29deg; animation-duration: 3.8s; animation-delay: -5.54s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1298px 440.4px -1110.6px; scale: 0.947; rotate: -2.38deg; animation-duration: 3.93s; animation-delay: -3.46s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -949.2px 440.9px -718.2px; scale: 0.929; rotate: 3.54deg; animation-duration: 4.41s; animation-delay: -3.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -749.5px 436.7px -3039.1px; scale: 0.853; rotate: -3.64deg; animation-duration: 5.76s; animation-delay: -4.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 3909.9px 450px -3538.2px; scale: 1.296; rotate: -3.69deg; animation-duration: 5.85s; animation-delay: -5.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 3249px 434.5px -2113.1px; scale: 0.846; rotate: 3.53deg; animation-duration: 5.11s; animation-delay: -2.9s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -3053.8px 436.7px -2594.4px; scale: 0.803; rotate: 0.62deg; animation-duration: 4.56s; animation-delay: -2.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 4932.8px 450.6px -3339.7px; scale: 0.671; rotate: -3.71deg; animation-duration: 5.34s; animation-delay: -5.76s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -1777.1px 440.6px -1994.2px; scale: 0.691; rotate: 1.03deg; animation-duration: 3.41s; animation-delay: -4.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -3364.5px 442.7px -2331.6px; scale: 0.766; rotate: -1.65deg; animation-duration: 5.32s; animation-delay: -2.13s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -753.5px 440.9px -888.5px; scale: 0.677; rotate: -3.29deg; animation-duration: 4.26s; animation-delay: -3.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 2091.4px 441.4px -2605.5px; scale: 0.952; rotate: -3.72deg; animation-duration: 4.73s; animation-delay: -2.15s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 4222.7px 437.2px -3006.2px; scale: 0.606; rotate: 0.16deg; animation-duration: 5.73s; animation-delay: -4.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1395.6px 433.1px -3140.7px; scale: 0.711; rotate: 2.85deg; animation-duration: 5.13s; animation-delay: -1.69s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -741.3px 440.3px -1443.5px; scale: 1.342; rotate: -1.74deg; animation-duration: 5.41s; animation-delay: -5.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 3542.8px 450.8px -2101.3px; scale: 0.85; rotate: -2.7deg; animation-duration: 5.34s; animation-delay: -2.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2232px 450.6px -3385.4px; scale: 1.244; rotate: -2.71deg; animation-duration: 5.84s; animation-delay: -0.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2288.4px 447.9px -3543.6px; scale: 0.871; rotate: 2.52deg; animation-duration: 3.74s; animation-delay: -5.07s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2121.9px 435.4px -872.8px; scale: 1.049; rotate: -3.66deg; animation-duration: 4.02s; animation-delay: -5.94s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 497.5px 450.9px -1010.7px; scale: 0.916; rotate: -3.24deg; animation-duration: 4.44s; animation-delay: -5.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -513.8px 451.4px -519.6px; scale: 1.19; rotate: 0.2deg; animation-duration: 3.49s; animation-delay: -1.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 645.5px 439.7px -2262.6px; scale: 0.655; rotate: 0.72deg; animation-duration: 4.29s; animation-delay: -4.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 1802.7px 433.6px -1213.6px; scale: 1.175; rotate: 3.14deg; animation-duration: 4.09s; animation-delay: -2.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 1701.8px 431.1px -3572.8px; scale: 1.092; rotate: 0.17deg; animation-duration: 4.48s; animation-delay: -5.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2415.7px 446.2px -2983.9px; scale: 0.789; rotate: -0.82deg; animation-duration: 5.84s; animation-delay: -1.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -2099.8px 452.1px -1972.2px; scale: 1.255; rotate: 1.61deg; animation-duration: 4.07s; animation-delay: -1.73s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 734.1px 447.7px -514.9px; scale: 1.272; rotate: -0.37deg; animation-duration: 4.66s; animation-delay: -3.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -1715.8px 436.3px -545.5px; scale: 0.728; rotate: -3.33deg; animation-duration: 4.59s; animation-delay: -1.8s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -3727.1px 450.7px -3287.8px; scale: 0.612; rotate: -1.29deg; animation-duration: 3.64s; animation-delay: -1.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 1078px 445.5px -1828.2px; scale: 0.989; rotate: -2.71deg; animation-duration: 5.48s; animation-delay: -1.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -1195.8px 433.2px -1065.9px; scale: 0.818; rotate: -3.35deg; animation-duration: 5.61s; animation-delay: -0.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -708.1px 428.9px -472.8px; scale: 1.142; rotate: 0.72deg; animation-duration: 5.67s; animation-delay: -0.23s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -554.3px 444.8px -608.5px; scale: 0.731; rotate: 2.23deg; animation-duration: 5.89s; animation-delay: -2.55s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 480.9px 443.9px -2188.5px; scale: 0.787; rotate: 3.85deg; animation-duration: 4.11s; animation-delay: -0.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -2897.6px 441.7px -3130.3px; scale: 0.906; rotate: -3.71deg; animation-duration: 4.03s; animation-delay: -1.52s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 118.8px 449.1px -853.5px; scale: 1.264; rotate: 0.93deg; animation-duration: 4.25s; animation-delay: -3.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -3055.4px 437.7px -2135.4px; scale: 1.176; rotate: 0.04deg; animation-duration: 3.84s; animation-delay: -3.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1344.5px 442.2px -956.8px; scale: 0.894; rotate: -1.57deg; animation-duration: 3.61s; animation-delay: -4.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 607.4px 445.4px -1028.4px; scale: 1.267; rotate: 0.64deg; animation-duration: 4.62s; animation-delay: -3.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 1359.6px 430px -1245.5px; scale: 0.815; rotate: 2.05deg; animation-duration: 4.58s; animation-delay: -3.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -1755.9px 444.4px -1489.6px; scale: 1.03; rotate: -0.22deg; animation-duration: 5.37s; animation-delay: -2.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -2284.8px 428.5px -3049px; scale: 1.02; rotate: 0.9deg; animation-duration: 4.11s; animation-delay: -2.12s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1534.2px 434.8px -944.3px; scale: 1.107; rotate: 2.05deg; animation-duration: 5.79s; animation-delay: -4.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -235px 433.2px -367.7px; scale: 1.205; rotate: -2.82deg; animation-duration: 3.58s; animation-delay: -5.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 1215.6px 441.7px -2464.3px; scale: 1.232; rotate: 0.79deg; animation-duration: 4.2s; animation-delay: -0.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 1998.6px 430.7px -1280px; scale: 1.251; rotate: -2.85deg; animation-duration: 5.12s; animation-delay: -3.53s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 1578.4px 440px -555.8px; scale: 1.22; rotate: 1.78deg; animation-duration: 4.95s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 2320.5px 428.7px -1350.9px; scale: 1.229; rotate: 1.09deg; animation-duration: 5.13s; animation-delay: -0.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -557.4px 435.5px -562.4px; scale: 0.907; rotate: -3.12deg; animation-duration: 5.52s; animation-delay: -2.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 558.6px 429.7px -2464.5px; scale: 0.801; rotate: 2.24deg; animation-duration: 3.65s; animation-delay: -3.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 679.2px 429.6px -713.2px; scale: 0.723; rotate: 2.45deg; animation-duration: 3.45s; animation-delay: -4.95s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -1504px 441.8px -3508.5px; scale: 1.267; rotate: 2.1deg; animation-duration: 5.65s; animation-delay: -1.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -971.8px 430.3px -3200.8px; scale: 1.059; rotate: -3.98deg; animation-duration: 5.03s; animation-delay: -3.19s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 4157px 428.9px -3141.3px; scale: 1.302; rotate: -1.83deg; animation-duration: 3.4s; animation-delay: -2.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 807.7px 433px -1701.3px; scale: 0.727; rotate: 3.85deg; animation-duration: 5.27s; animation-delay: -3.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 474.3px 439.4px -2836.3px; scale: 1.059; rotate: -3.8deg; animation-duration: 5.87s; animation-delay: -1.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -842.2px 443.7px -1035.1px; scale: 0.73; rotate: -2.25deg; animation-duration: 3.82s; animation-delay: -3.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -199.3px 429.6px -2247.6px; scale: 1.334; rotate: -3.13deg; animation-duration: 5.79s; animation-delay: -2.42s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -776.5px 436.7px -965.2px; scale: 1.284; rotate: 0.96deg; animation-duration: 4.63s; animation-delay: -0.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -3959.7px 445.4px -3538.8px; scale: 1.016; rotate: -0.33deg; animation-duration: 5.22s; animation-delay: -5.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2170.6px 438px -1680.3px; scale: 0.644; rotate: -0.59deg; animation-duration: 5.44s; animation-delay: -1.38s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 1443.4px 445.7px -465.5px; scale: 0.684; rotate: -2.22deg; animation-duration: 3.89s; animation-delay: -0.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -775.7px 436.9px -2074.9px; scale: 0.691; rotate: -0.74deg; animation-duration: 5.49s; animation-delay: -5.99s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1480.8px 436.2px -2548.6px; scale: 0.773; rotate: -3.69deg; animation-duration: 3.9s; animation-delay: -0.31s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 2716px 442.9px -2093.9px; scale: 0.976; rotate: 0.41deg; animation-duration: 5.03s; animation-delay: -5.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -1528.1px 450.5px -465.3px; scale: 0.734; rotate: 1.95deg; animation-duration: 3.46s; animation-delay: -5.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 920.8px 433.4px -2727.1px; scale: 0.991; rotate: -0.17deg; animation-duration: 5.8s; animation-delay: -1.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 1175.1px 443.8px -682.4px; scale: 0.653; rotate: -3.68deg; animation-duration: 5.05s; animation-delay: -4.58s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: 2895.2px 440.6px -3044.2px; scale: 0.977; rotate: 3.49deg; animation-duration: 4.68s; animation-delay: -2.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -3704.7px 430.8px -3530.4px; scale: 0.868; rotate: -1.51deg; animation-duration: 5.94s; animation-delay: -0.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 3640.2px 427.1px -2605.5px; scale: 1.054; rotate: -0.88deg; animation-duration: 4.54s; animation-delay: -4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: -1094.9px 451.6px -393.5px; scale: 0.859; rotate: -0.76deg; animation-duration: 5.78s; animation-delay: -5.17s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -3599.7px 429.6px -2964.5px; scale: 1.205; rotate: 2.98deg; animation-duration: 5.56s; animation-delay: -5.21s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1327.7px 430.8px -1348.2px; scale: 1.112; rotate: -2.53deg; animation-duration: 3.82s; animation-delay: -4.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 4476.9px 435.9px -3492.2px; scale: 0.966; rotate: 1.47deg; animation-duration: 4.2s; animation-delay: -4.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -2027.2px 452.5px -969.8px; scale: 1.08; rotate: 1.68deg; animation-duration: 5.26s; animation-delay: -5.24s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -458.9px 431.7px -590.3px; scale: 1.276; rotate: 1.67deg; animation-duration: 4.37s; animation-delay: -2.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 852.7px 450.6px -3402.1px; scale: 1.149; rotate: -2.2deg; animation-duration: 5.95s; animation-delay: -3.42s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1316.9px 440.4px -653.1px; scale: 0.984; rotate: 3.12deg; animation-duration: 4.78s; animation-delay: -2.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 3081.9px 439.4px -2006.2px; scale: 1.262; rotate: -1.96deg; animation-duration: 5.88s; animation-delay: -3.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1051.8px 451.3px -651.7px; scale: 0.76; rotate: 2.8deg; animation-duration: 3.85s; animation-delay: -2.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -711.5px 446px -1656.1px; scale: 0.653; rotate: 2.89deg; animation-duration: 4.99s; animation-delay: -0.35s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -221.2px 429.5px -2724.3px; scale: 1.268; rotate: 2.4deg; animation-duration: 5.25s; animation-delay: -3.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1997.7px 438.7px -3184.6px; scale: 0.727; rotate: 1.36deg; animation-duration: 4.11s; animation-delay: -5.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1910.9px 447.2px -1197px; scale: 1.073; rotate: -1.51deg; animation-duration: 5.44s; animation-delay: -4.67s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 408.8px 433px -1151.1px; scale: 0.834; rotate: 3.77deg; animation-duration: 5.56s; animation-delay: -3.57s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 1289.3px 450.8px -860.2px; scale: 0.97; rotate: -2.21deg; animation-duration: 5.19s; animation-delay: -1.09s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 1240.2px 444.5px -2298.2px; scale: 1.301; rotate: -0.56deg; animation-duration: 3.64s; animation-delay: -2.57s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 1230.5px 441.2px -3302.4px; scale: 0.794; rotate: -1.95deg; animation-duration: 4.77s; animation-delay: -1.03s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 3215.3px 444.1px -2132.9px; scale: 1.102; rotate: 1.18deg; animation-duration: 4.03s; animation-delay: -5.49s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2083px 436.5px -931.5px; scale: 1.219; rotate: -0.86deg; animation-duration: 5.18s; animation-delay: -5.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -943.3px 433.2px -1014.6px; scale: 1.207; rotate: 1.45deg; animation-duration: 4.88s; animation-delay: -3.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 3198.5px 449.7px -2807.6px; scale: 0.608; rotate: 0.42deg; animation-duration: 3.83s; animation-delay: -3.48s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -821px 437.9px -320.1px; scale: 0.754; rotate: -1.95deg; animation-duration: 5.02s; animation-delay: -3.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -2775.4px 444.3px -3547.9px; scale: 0.898; rotate: 2.39deg; animation-duration: 5.14s; animation-delay: -4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -294.7px 435.3px -2970px; scale: 1.048; rotate: -1.29deg; animation-duration: 3.98s; animation-delay: -3.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -730.1px 445.8px -293.5px; scale: 0.757; rotate: 0.27deg; animation-duration: 4.91s; animation-delay: -2.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 166.2px 440.1px -3564.6px; scale: 1.139; rotate: 0.64deg; animation-duration: 4.71s; animation-delay: -1.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -811px 439.3px -1593.3px; scale: 1.328; rotate: -0.24deg; animation-duration: 4.66s; animation-delay: -1.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 289.8px 431.2px -317.1px; scale: 1.057; rotate: -2.02deg; animation-duration: 3.89s; animation-delay: -1.25s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -4098.3px 432.8px -2946.9px; scale: 0.691; rotate: 1.84deg; animation-duration: 5.04s; animation-delay: -5.61s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -2779.5px 448px -3470.8px; scale: 0.621; rotate: -1.05deg; animation-duration: 5.26s; animation-delay: -5.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1094.8px 436.6px -800.1px; scale: 1.058; rotate: 2.71deg; animation-duration: 5.93s; animation-delay: -1.83s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -827.8px 429.2px -2695px; scale: 0.904; rotate: -3.12deg; animation-duration: 3.98s; animation-delay: -5.12s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -584.7px 450.3px -446.7px; scale: 0.672; rotate: -1.83deg; animation-duration: 4.37s; animation-delay: -5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 847.2px 435.2px -450.9px; scale: 0.711; rotate: 0.09deg; animation-duration: 5.43s; animation-delay: -0.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 1929.1px 444.4px -3117.7px; scale: 0.772; rotate: -0.18deg; animation-duration: 4.2s; animation-delay: -3.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1548.9px 441.6px -2309.9px; scale: 0.613; rotate: -0.02deg; animation-duration: 4.95s; animation-delay: -1.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -1882.6px 444.5px -1159.1px; scale: 1.054; rotate: -2.96deg; animation-duration: 3.5s; animation-delay: -4.91s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1170.2px 430.4px -3502.7px; scale: 0.692; rotate: 1.17deg; animation-duration: 3.47s; animation-delay: -5.87s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -3277.6px 442.7px -3445.1px; scale: 0.8; rotate: -3.86deg; animation-duration: 4.21s; animation-delay: -0.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 3584.3px 437.7px -2543.1px; scale: 1.091; rotate: -0.17deg; animation-duration: 3.76s; animation-delay: -1.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 194.4px 431.8px -1972.5px; scale: 0.818; rotate: 2.61deg; animation-duration: 3.43s; animation-delay: -3.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -2910px 445.2px -2738.5px; scale: 0.87; rotate: -2.25deg; animation-duration: 5.83s; animation-delay: -0.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 2192px 433.3px -1148.9px; scale: 0.646; rotate: -3.95deg; animation-duration: 4.79s; animation-delay: -4.86s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -1286.2px 451.9px -1712.6px; scale: 0.747; rotate: -2.36deg; animation-duration: 5.57s; animation-delay: -3.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 3118.4px 445.4px -2108.4px; scale: 0.746; rotate: -0.39deg; animation-duration: 4.48s; animation-delay: -5.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -4372.5px 444.4px -3520.3px; scale: 0.674; rotate: 3.97deg; animation-duration: 4.78s; animation-delay: -5.75s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1494.3px 434.9px -1358.3px; scale: 1.092; rotate: 1.99deg; animation-duration: 5.21s; animation-delay: -4.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 1079.6px 434.4px -431.6px; scale: 1.316; rotate: -2.6deg; animation-duration: 5.24s; animation-delay: -1.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -2944.7px 447.2px -2349.6px; scale: 1.016; rotate: -0.36deg; animation-duration: 4.13s; animation-delay: -5.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -1011.9px 443.8px -1299px; scale: 1.221; rotate: 1.67deg; animation-duration: 4.79s; animation-delay: -2.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -861.8px 432.4px -2112.8px; scale: 0.811; rotate: 1.03deg; animation-duration: 3.78s; animation-delay: -4.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 3133.3px 432.9px -3314.6px; scale: 0.857; rotate: -0.85deg; animation-duration: 5.91s; animation-delay: -2.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1121.9px 452.5px -2643.1px; scale: 1.107; rotate: -3.33deg; animation-duration: 4.67s; animation-delay: -2.26s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 1146.7px 449.1px -1645.5px; scale: 1.227; rotate: -1.01deg; animation-duration: 4.68s; animation-delay: -1.94s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 1845.2px 442.1px -1037.8px; scale: 0.818; rotate: -1.26deg; animation-duration: 4.55s; animation-delay: -3.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -3051.3px 431.9px -1957.4px; scale: 0.715; rotate: -0.01deg; animation-duration: 3.47s; animation-delay: -4.56s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 393.9px 441.5px -3065.8px; scale: 1.145; rotate: -3.88deg; animation-duration: 5.7s; animation-delay: -2.1s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 4184px 452px -3530.8px; scale: 0.665; rotate: 3.01deg; animation-duration: 4.68s; animation-delay: -1.25s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -2264.2px 434.4px -2111.2px; scale: 0.976; rotate: -1.5deg; animation-duration: 4.66s; animation-delay: -4.52s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -2555.7px 447.7px -1244.3px; scale: 1.017; rotate: -3.35deg; animation-duration: 3.61s; animation-delay: -3.16s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 519.5px 434.1px -2308.4px; scale: 1.323; rotate: -0.39deg; animation-duration: 5.51s; animation-delay: -1.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 4369.3px 446.3px -2921.6px; scale: 1.195; rotate: 0.52deg; animation-duration: 5.49s; animation-delay: -1.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 1416px 450.1px -2129px; scale: 0.653; rotate: 2.27deg; animation-duration: 4.06s; animation-delay: -4.12s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 1641.5px 450.1px -1444.4px; scale: 1.009; rotate: 2.13deg; animation-duration: 4.75s; animation-delay: -4.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -4265.6px 437.6px -3093.5px; scale: 0.704; rotate: -3.91deg; animation-duration: 3.96s; animation-delay: -1.35s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -738.3px 442.8px -1231.5px; scale: 1.049; rotate: 1.84deg; animation-duration: 4.77s; animation-delay: -4.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 1520.8px 438.4px -2573.9px; scale: 1.02; rotate: -0.79deg; animation-duration: 4.08s; animation-delay: -0.36s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -720.6px 445.1px -1431.2px; scale: 0.81; rotate: -2.6deg; animation-duration: 5.56s; animation-delay: -1.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: -1806px 431.5px -2747.7px; scale: 1.072; rotate: 1.13deg; animation-duration: 3.58s; animation-delay: -0.24s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 3968.7px 439.3px -3098.9px; scale: 1.286; rotate: -0.02deg; animation-duration: 3.83s; animation-delay: -2.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -164.2px 446px -1304.9px; scale: 0.75; rotate: 2.12deg; animation-duration: 5.26s; animation-delay: -4.22s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 2428.1px 440.2px -3003.8px; scale: 0.864; rotate: -2.35deg; animation-duration: 4.42s; animation-delay: -1.43s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 774.6px 447px -485px; scale: 1.157; rotate: 2.27deg; animation-duration: 3.43s; animation-delay: -3.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 1425.3px 442.3px -1316px; scale: 0.878; rotate: 3.49deg; animation-duration: 4.64s; animation-delay: -3.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: -3006.3px 440.4px -2483.9px; scale: 1.04; rotate: -0.14deg; animation-duration: 4.28s; animation-delay: -2.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 2320.5px 440.5px -3411px; scale: 1.027; rotate: -0.45deg; animation-duration: 4.31s; animation-delay: -1.08s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: -406.5px 442.9px -2958.4px; scale: 0.9; rotate: 0.31deg; animation-duration: 5.74s; animation-delay: -1.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1451.8px 434.3px -1393px; scale: 0.892; rotate: 1.53deg; animation-duration: 5.73s; animation-delay: -5.44s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: -1035.3px 443.9px -313.6px; scale: 1.219; rotate: -3.81deg; animation-duration: 5.38s; animation-delay: -1.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 423.3px 438.4px -1118.5px; scale: 0.926; rotate: 1.97deg; animation-duration: 5.06s; animation-delay: -0.74s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -1745.1px 443.4px -846.4px; scale: 1.218; rotate: -1.06deg; animation-duration: 5.87s; animation-delay: -0.81s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -346.3px 430px -1554.8px; scale: 1.151; rotate: 1.17deg; animation-duration: 5.48s; animation-delay: -3.4s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 2574.9px 427.1px -2003.5px; scale: 0.928; rotate: 0.71deg; animation-duration: 4.73s; animation-delay: -0.73s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -1446.2px 441.7px -2033.2px; scale: 0.924; rotate: -3.05deg; animation-duration: 5.86s; animation-delay: -4.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: -599.3px 434.9px -3093.2px; scale: 1.081; rotate: -1.82deg; animation-duration: 5.31s; animation-delay: -1.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: 464.6px 429px -2157.7px; scale: 1.039; rotate: -0.05deg; animation-duration: 5.07s; animation-delay: -0.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -584.5px 439.3px -1599.6px; scale: 0.681; rotate: -1.07deg; animation-duration: 5.4s; animation-delay: -5.06s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1915.2px 436.9px -1006.9px; scale: 0.612; rotate: 2.64deg; animation-duration: 5.5s; animation-delay: -5.57s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: 1021.3px 451.8px -1883.8px; scale: 1.226; rotate: 2.2deg; animation-duration: 5.42s; animation-delay: -1.5s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -4367.1px 429.6px -2918.9px; scale: 1.135; rotate: -0.88deg; animation-duration: 5.85s; animation-delay: -2.27s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1214.8px 429.4px -1006.6px; scale: 1.215; rotate: -0.32deg; animation-duration: 5.5s; animation-delay: -4.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: -314.3px 451px -883.6px; scale: 0.954; rotate: -2.62deg; animation-duration: 3.91s; animation-delay: -5.63s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 359.4px 451.5px -3319px; scale: 1.075; rotate: 3.7deg; animation-duration: 4.46s; animation-delay: -4.14s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: -2414.9px 430.2px -1547.1px; scale: 1.036; rotate: -0.11deg; animation-duration: 5.22s; animation-delay: -5.77s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 8 110 -4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 6.2 130 -6.6 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_3"></use>
    </svg></div><div class="field-flower" style="translate: 4451.6px 440.6px -3534.6px; scale: 0.73; rotate: -1.39deg; animation-duration: 4.73s; animation-delay: -5.39s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 1627.5px 445.4px -602px; scale: 1.057; rotate: -0.85deg; animation-duration: 4.78s; animation-delay: -5.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div><div class="field-flower" style="translate: 2294.5px 431.9px -1165.9px; scale: 0.931; rotate: 2.52deg; animation-duration: 3.63s; animation-delay: -1.38s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 4812.6px 445.5px -3172.1px; scale: 0.906; rotate: -0.9deg; animation-duration: 3.54s; animation-delay: -2.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1359.8px 429.9px -821.6px; scale: 1.1; rotate: 1.5deg; animation-duration: 5.96s; animation-delay: -1.55s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_2"></use>
    </svg></div><div class="field-flower" style="translate: -2786px 431px -3548.3px; scale: 1.246; rotate: 1.12deg; animation-duration: 4.72s; animation-delay: -3.32s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: 2043.2px 448.8px -2436.5px; scale: 0.772; rotate: -1.58deg; animation-duration: 5.09s; animation-delay: -5.7s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: 2728.2px 440.6px -3448.4px; scale: 1.074; rotate: -1.16deg; animation-duration: 5.06s; animation-delay: -5.96s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -7 110 4.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -8.8 130 2.4 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_0"></use>
    </svg></div><div class="field-flower" style="translate: 462.3px 442.2px -861.4px; scale: 0.661; rotate: 0.79deg; animation-duration: 4.48s; animation-delay: -3.78s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 4 110 -2.4 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 2.2 130 -4.2 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -620.3px 452.3px -2300.2px; scale: 1.222; rotate: 0.56deg; animation-duration: 3.84s; animation-delay: -1.18s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: -823.6px 439.6px -1889.6px; scale: 1.208; rotate: 1.08deg; animation-duration: 4.09s; animation-delay: -1.92s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_1"></use>
    </svg></div><div class="field-flower" style="translate: 864.2px 429.1px -645.8px; scale: 0.873; rotate: -0.01deg; animation-duration: 3.52s; animation-delay: -5.84s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -1666.9px 440.3px -2231px; scale: 1.21; rotate: 3.58deg; animation-duration: 4.68s; animation-delay: -3.45s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_3"></use>
    </svg></div><div class="field-flower" style="translate: 3471.4px 442.9px -2610px; scale: 0.706; rotate: 3.13deg; animation-duration: 3.6s; animation-delay: -0.47s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -289.9px 442.8px -1777.3px; scale: 0.754; rotate: 0.15deg; animation-duration: 5.52s; animation-delay: -0.2s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: -1711.2px 442.7px -3301.5px; scale: 1.236; rotate: 1.82deg; animation-duration: 3.5s; animation-delay: -2.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_3"></use>
    </svg></div><div class="field-flower" style="translate: -1933.2px 451.6px -1111px; scale: 1.153; rotate: -3.56deg; animation-duration: 5.83s; animation-delay: -5.98s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -5 110 3.0 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -6.8 130 1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_4"></use>
    </svg></div><div class="field-flower" style="translate: 1413.6px 444px -516.5px; scale: 0.816; rotate: -1.79deg; animation-duration: 4.99s; animation-delay: -4.29s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_3"></use>
    </svg></div><div class="field-flower" style="translate: -557.8px 430.5px -643.1px; scale: 1.218; rotate: -1.6deg; animation-duration: 5.38s; animation-delay: -4.41s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -6 110 3.6 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -7.8 130 1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: 591.7px 430.3px -2179.8px; scale: 0.908; rotate: 3.93deg; animation-duration: 5.98s; animation-delay: -3.51s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: -3157.6px 436px -2182.6px; scale: 0.853; rotate: -1.33deg; animation-duration: 4.15s; animation-delay: -0.6s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_2"></use>
    </svg></div><div class="field-flower" style="translate: -1512px 427.8px -3082.5px; scale: 1.159; rotate: 3.17deg; animation-duration: 5.96s; animation-delay: -0.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -3 110 1.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -4.8 130 -0.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: -1177.2px 436.7px -592.1px; scale: 1.045; rotate: -2.82deg; animation-duration: 3.5s; animation-delay: -2.62s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 5 110 -3.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 3.2 130 -4.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: 2727.2px 449px -1478.2px; scale: 1.206; rotate: -2.9deg; animation-duration: 4.13s; animation-delay: -0.93s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 6 110 -3.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 4.2 130 -5.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_0"></use>
    </svg></div><div class="field-flower" style="translate: 1339.6px 443.6px -1804.7px; scale: 1.268; rotate: -0.23deg; animation-duration: 4.33s; animation-delay: -4.11s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: -956.4px 452.3px -773.8px; scale: 1.331; rotate: -3.24deg; animation-duration: 3.59s; animation-delay: -2.53s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_1"></use>
    </svg></div><div class="field-flower" style="translate: -4362.9px 427.3px -3027.5px; scale: 1.304; rotate: -0.32deg; animation-duration: 5.37s; animation-delay: -3.65s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -8 110 4.8 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -9.8 130 3.0 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_2"></use>
    </svg></div><div class="field-flower" style="translate: 2544.8px 436px -1936.9px; scale: 0.607; rotate: 0.53deg; animation-duration: 5.88s; animation-delay: -1.66s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -1 110 0.6 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -2.8 130 -1.2 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_0"></use>
    </svg></div><div class="field-flower" style="translate: 4521px 448.8px -3040.7px; scale: 1.297; rotate: 3.14deg; animation-duration: 5.34s; animation-delay: -2.3s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -4 110 2.4 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -5.8 130 0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_4"></use>
    </svg></div><div class="field-flower" style="translate: -1284.9px 447.9px -1557.9px; scale: 1.117; rotate: 3deg; animation-duration: 4.7s; animation-delay: -5.68s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 1 110 -0.6 230 0 340" fill="none" stroke="#456a1b" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -0.8 130 -2.4 230 -1.8 328" fill="none" stroke="#6a9631" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf2" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf2" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#2a380d"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#456a1b" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead2_2"></use>
    </svg></div><div class="field-flower" style="translate: 1763.2px 429px -2007.1px; scale: 1.125; rotate: 0.49deg; animation-duration: 4.61s; animation-delay: -4.79s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 7 110 -4.2 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 5.2 130 -6.0 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_1"></use>
    </svg></div><div class="field-flower" style="translate: 2816.6px 427.5px -3581.8px; scale: 1.333; rotate: -0.19deg; animation-duration: 5.33s; animation-delay: -3.57s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C -2 110 1.2 230 0 340" fill="none" stroke="#504d17" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -3.8 130 -0.6 230 -1.8 328" fill="none" stroke="#626321" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf0" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf0" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#423510"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#504d17" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead0_1"></use>
    </svg></div><div class="field-flower" style="translate: 1346.1px 435.9px -953.1px; scale: 1.109; rotate: 0.63deg; animation-duration: 4.98s; animation-delay: -4.72s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 2 110 -1.2 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 0.19999999999999996 130 -3.0 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_0"></use>
    </svg></div><div class="field-flower" style="translate: -3609.1px 432.8px -2568.5px; scale: 0.829; rotate: 1.36deg; animation-duration: 5.25s; animation-delay: -4.35s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 3 110 -1.8 230 0 340" fill="none" stroke="#4a5c19" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C 1.2 130 -3.6 230 -1.8 328" fill="none" stroke="#667c29" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf1" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf1" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#36370f"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#4a5c19" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead1_4"></use>
    </svg></div><div class="field-flower" style="translate: 1475.6px 433px -802.4px; scale: 0.875; rotate: 3.95deg; animation-duration: 4.19s; animation-delay: -3.85s;"><svg viewBox="-60 -60 120 400" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C 0 110 0.0 230 0 340" fill="none" stroke="#40771e" stroke-width="7" stroke-linecap="round"></path>
      <path d="M-1.8 40 C -1.8 130 -1.8 230 -1.8 328" fill="none" stroke="#6ead38" stroke-width="1.6" opacity=".5"></path>
      <use href="#fleaf3" transform="translate(1,205) rotate(-28)"></use>
      <use href="#fleaf3" transform="translate(-1,258) scale(-1,1) rotate(-30)"></use>
      <ellipse cx="0" cy="340" rx="17" ry="4" fill="#1e3a0c"></ellipse>
      <path d="M-15 340 Q-11 314 -5 340 M-7 340 Q-1 306 5 340 M3 340 Q11 316 17 340" fill="none" stroke="#40771e" stroke-width="3" stroke-linecap="round"></path>
      <use href="#fhead3_4"></use>
    </svg></div></div>
  </div>

  <div class="field-vignette"></div>

  <div class="field-ui fade-out" id="fieldUI">
    <h2>Un campo de girasoles</h2>
    <p class="field-sub">Camina entre las flores y encuentra un regalo al final</p>
    <button class="btn btn-enter" id="btnEnter">
      <span>Comenzar el viaje</span> 🌻
    </button>
  </div>
</section>

<!-- ============================================================
     ESCENA 2: RAMO + CARTA
     ============================================================ -->
<section class="scene bouquet-scene active" id="bouquetScene">
  <header>
    <h1>FLORES AMARILLAS PARA TI</h1>
    <p class="subtitle">Un detalle hecho con cariño</p>
  </header>

  <main class="bouquet-wrap">
    <div class="glow"></div>

    <svg class="bouquet" viewBox="0 0 500 700" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Ramo de girasoles amarillos envuelto en papel con lazo dorado">
      <defs>
        <lineargradient id="petalGrad" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#fffbc4"></stop>
          <stop offset="28%" stop-color="#ffe44a"></stop>
          <stop offset="66%" stop-color="#ffc107"></stop>
          <stop offset="100%" stop-color="#e08a00"></stop>
        </lineargradient>

        <lineargradient id="petalBack" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#ffd94f"></stop>
          <stop offset="55%" stop-color="#f0a500"></stop>
          <stop offset="100%" stop-color="#b96f00"></stop>
        </lineargradient>

        <radialgradient id="coreGrad" cx="42%" cy="36%" r="66%">
          <stop offset="0%" stop-color="#8a5f30"></stop>
          <stop offset="42%" stop-color="#4a2c10"></stop>
          <stop offset="100%" stop-color="#180c02"></stop>
        </radialgradient>

        <pattern id="seeds" width="7" height="7" patternUnits="userSpaceOnUse">
          <circle cx="1.8" cy="1.8" r="1.35" fill="#9a6c38" opacity=".5"></circle>
          <circle cx="5.3" cy="5.3" r="1.35" fill="#1d0f04" opacity=".55"></circle>
        </pattern>

        <lineargradient id="stemGrad" x1="0" y1="0" x2="1" y2="0">
          <stop offset="0%" stop-color="#1e3f18"></stop>
          <stop offset="45%" stop-color="#4d8b33"></stop>
          <stop offset="100%" stop-color="#1e3f18"></stop>
        </lineargradient>

        <lineargradient id="leafGrad" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#7cc23a"></stop>
          <stop offset="55%" stop-color="#3f7a1e"></stop>
          <stop offset="100%" stop-color="#23490e"></stop>
        </lineargradient>

        <lineargradient id="wrapFront" x1="0" y1="0" x2="1" y2="0">
          <stop offset="0%" stop-color="#1a0f07"></stop>
          <stop offset="10%" stop-color="#4d3319"></stop>
          <stop offset="23%" stop-color="#2a1a0c"></stop>
          <stop offset="37%" stop-color="#63421f"></stop>
          <stop offset="50%" stop-color="#33200f"></stop>
          <stop offset="63%" stop-color="#63421f"></stop>
          <stop offset="77%" stop-color="#2a1a0c"></stop>
          <stop offset="90%" stop-color="#4d3319"></stop>
          <stop offset="100%" stop-color="#1a0f07"></stop>
        </lineargradient>

        <lineargradient id="wrapBack" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#6b4622"></stop>
          <stop offset="55%" stop-color="#3a2411"></stop>
          <stop offset="100%" stop-color="#1c1008"></stop>
        </lineargradient>

        <lineargradient id="wrapShade" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#000" stop-opacity="0"></stop>
          <stop offset="62%" stop-color="#000" stop-opacity=".22"></stop>
          <stop offset="100%" stop-color="#000" stop-opacity=".62"></stop>
        </lineargradient>

        <lineargradient id="ribbonGrad" x1="0" y1="0" x2="0" y2="1">
          <stop offset="0%" stop-color="#fff3a6"></stop>
          <stop offset="38%" stop-color="#ffd94f"></stop>
          <stop offset="72%" stop-color="#d9a520"></stop>
          <stop offset="100%" stop-color="#9d6f0c"></stop>
        </lineargradient>

        <path id="petal" d="M 0 0 C -11 -28 -15 -60 0 -92 C 15 -60 11 -28 0 0 Z"></path>
        <path id="petalSmall" d="M 0 0 C -9 -20 -13 -42 0 -64 C 13 -42 9 -20 0 0 Z"></path>

        <g id="flowerHead">
          <g fill="url(#petalBack)">
            <use href="#petal" transform="rotate(9)"></use>
            <use href="#petal" transform="rotate(27)"></use>
            <use href="#petal" transform="rotate(45)"></use>
            <use href="#petal" transform="rotate(63)"></use>
            <use href="#petal" transform="rotate(81)"></use>
            <use href="#petal" transform="rotate(99)"></use>
            <use href="#petal" transform="rotate(117)"></use>
            <use href="#petal" transform="rotate(135)"></use>
            <use href="#petal" transform="rotate(153)"></use>
            <use href="#petal" transform="rotate(171)"></use>
            <use href="#petal" transform="rotate(189)"></use>
            <use href="#petal" transform="rotate(207)"></use>
            <use href="#petal" transform="rotate(225)"></use>
            <use href="#petal" transform="rotate(243)"></use>
            <use href="#petal" transform="rotate(261)"></use>
            <use href="#petal" transform="rotate(279)"></use>
            <use href="#petal" transform="rotate(297)"></use>
            <use href="#petal" transform="rotate(315)"></use>
            <use href="#petal" transform="rotate(333)"></use>
            <use href="#petal" transform="rotate(351)"></use>
          </g>
          <g fill="url(#petalGrad)">
            <use href="#petal" transform="rotate(0)"></use>
            <use href="#petal" transform="rotate(18)"></use>
            <use href="#petal" transform="rotate(36)"></use>
            <use href="#petal" transform="rotate(54)"></use>
            <use href="#petal" transform="rotate(72)"></use>
            <use href="#petal" transform="rotate(90)"></use>
            <use href="#petal" transform="rotate(108)"></use>
            <use href="#petal" transform="rotate(126)"></use>
            <use href="#petal" transform="rotate(144)"></use>
            <use href="#petal" transform="rotate(162)"></use>
            <use href="#petal" transform="rotate(180)"></use>
            <use href="#petal" transform="rotate(198)"></use>
            <use href="#petal" transform="rotate(216)"></use>
            <use href="#petal" transform="rotate(234)"></use>
            <use href="#petal" transform="rotate(252)"></use>
            <use href="#petal" transform="rotate(270)"></use>
            <use href="#petal" transform="rotate(288)"></use>
            <use href="#petal" transform="rotate(306)"></use>
            <use href="#petal" transform="rotate(324)"></use>
            <use href="#petal" transform="rotate(342)"></use>
          </g>
          <circle r="40" fill="url(#coreGrad)"></circle>
          <circle r="40" fill="url(#seeds)"></circle>
          <circle r="40" fill="none" stroke="#c99a2e" stroke-width="2.2" opacity=".75"></circle>
          <circle r="35" fill="none" stroke="#000" stroke-width="1.4" opacity=".22"></circle>
          <ellipse cx="-12" cy="-14" rx="14" ry="8" fill="#fff" opacity=".09" transform="rotate(-35)"></ellipse>
        </g>

        <g id="daisy">
          <g fill="url(#petalGrad)">
            <use href="#petalSmall" transform="rotate(0)"></use>
            <use href="#petalSmall" transform="rotate(30)"></use>
            <use href="#petalSmall" transform="rotate(60)"></use>
            <use href="#petalSmall" transform="rotate(90)"></use>
            <use href="#petalSmall" transform="rotate(120)"></use>
            <use href="#petalSmall" transform="rotate(150)"></use>
            <use href="#petalSmall" transform="rotate(180)"></use>
            <use href="#petalSmall" transform="rotate(210)"></use>
            <use href="#petalSmall" transform="rotate(240)"></use>
            <use href="#petalSmall" transform="rotate(270)"></use>
            <use href="#petalSmall" transform="rotate(300)"></use>
            <use href="#petalSmall" transform="rotate(330)"></use>
          </g>
          <circle r="20" fill="url(#coreGrad)"></circle>
          <circle r="20" fill="url(#seeds)"></circle>
          <circle r="20" fill="none" stroke="#c99a2e" stroke-width="1.8" opacity=".8"></circle>
        </g>
      </defs>

      <g class="bouquet-sway">

        <path d="M 72 285 Q 250 215 428 285 L 250 590 Z" fill="url(#wrapBack)"></path>

        <g fill="none" stroke="url(#stemGrad)" stroke-linecap="round">
          <path d="M 250 570 Q 246 400 250 100" stroke-width="10"></path>
          <path d="M 250 570 Q 218 380 196 150" stroke-width="8"></path>
          <path d="M 250 570 Q 282 380 304 150" stroke-width="8"></path>
          <path d="M 250 570 Q 188 400 142 240" stroke-width="9"></path>
          <path d="M 250 570 Q 312 400 358 240" stroke-width="9"></path>
        </g>

        <g>
          <path d="M 208 350 C 155 314 100 312 56 334 C 92 374 152 380 208 350 Z" fill="url(#leafGrad)"></path>
          <path d="M 208 350 Q 130 325 58 334" fill="none" stroke="#9bd85c" stroke-width="1.6" opacity=".45"></path>
          <path d="M 292 350 C 345 314 400 312 444 334 C 408 374 348 380 292 350 Z" fill="url(#leafGrad)"></path>
          <path d="M 292 350 Q 370 325 442 334" fill="none" stroke="#9bd85c" stroke-width="1.6" opacity=".45"></path>
        </g>

        <g class="bob" style="--bd:-2.4s"><use href="#flowerHead" transform="translate(250, 88) scale(0.58)"></use></g>
        <g class="bob" style="--bd:-1.1s"><use href="#flowerHead" transform="translate(196,140) scale(0.68)"></use></g>
        <g class="bob" style="--bd:-3.2s"><use href="#flowerHead" transform="translate(304,140) scale(0.68)"></use></g>

        <g class="bob" style="--bd:-0.6s"><use href="#daisy" transform="translate(78, 185) scale(0.42)"></use></g>
        <g class="bob" style="--bd:-2.1s"><use href="#daisy" transform="translate(422,185) scale(0.42)"></use></g>

        <g class="bob" style="--bd:-1.8s"><use href="#flowerHead" transform="translate(142,232) scale(0.74)"></use></g>
        <g class="bob" style="--bd:-3.8s"><use href="#flowerHead" transform="translate(358,232) scale(0.74)"></use></g>

        <g class="bob" style="--bd:-0.9s"><use href="#flowerHead" transform="translate(250,262) scale(1)"></use></g>

        <path d="M 72 285 Q 250 372 428 285 L 250 590 Z" fill="url(#wrapFront)"></path>
        <path d="M 72 285 Q 250 372 428 285 L 250 590 Z" fill="url(#wrapShade)"></path>
        <path d="M 72 285 Q 250 372 428 285 Q 250 360 72 285 Z" fill="#ffe6a8" opacity=".10"></path>
        <path d="M 196 300 Q 210 430 234 572 L 248 576 Q 226 434 214 300 Z" fill="#fff" opacity=".07"></path>
        <path d="M 300 300 Q 288 430 266 572 L 254 576 Q 274 434 286 300 Z" fill="#fff" opacity=".05"></path>

        <g id="ribbon">
          <path d="M 143 402 Q 250 432 357 402 L 357 434 Q 250 464 143 434 Z" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".8"></path>

          <g transform="translate(250, 428)">
            <path d="M -4 4 Q -34 44 -50 82 Q -36 76 -22 72 Q -12 40 -1 10 Z" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".7"></path>
            <path d="M  4 4 Q  34 44  50 82 Q  36 76  22 72 Q  12 40  1 10 Z" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".7"></path>
            <path d="M -4 4 C -64 -34 -78 26 -8 16 Z" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".9"></path>
            <path d="M  4 4 C  64 -34  78 26  8 16 Z" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".9"></path>
            <ellipse cx="0" cy="8" rx="15" ry="13" fill="url(#ribbonGrad)" stroke="#a97c12" stroke-width=".9"></ellipse>
            <ellipse cx="-5" cy="4" rx="6" ry="4" fill="#fff8c9" opacity=".55"></ellipse>
          </g>
        </g>
      </g>
    </svg>
  </main>

  <div class="controls">
    <button class="btn" id="btnCard">✉️ Ver tarjeta</button>
    <button class="btn" id="btnSparkles">✨ Lluvia de brillos</button>
  </div>
</section>

<!-- ============================================================
     TRANSICIÓN A NEGRO
     ============================================================ -->
<div class="transition-black" id="transitionBlack"></div>

<!-- ============================================================
     MODAL DE LA CARTA
     ============================================================ -->
<div class="modal" id="cardModal">
  <div class="card">
    <button class="close-card" id="btnCloseCard" aria-label="Cerrar">×</button>
    <h2>Para Mi Nina Hermosaaaaaa</h2>
    <div class="card-emojis">🌻 🌻 🌻</div>
    <div class="card-message" contenteditable="true" id="editableMessage">Melissa linda&nbsp; yo se que nunca te e regalado flores amarillas y esta va a hacer mi primera ves esperero que te guste y quiero decirte lo mucho que te amo, eres lo mejor que me a podido pasar en la vida espero que lasss presumaaas preciosaa<span style="font-size: 1.75rem;">.</span></div>
    <div class="card-footer">
      <strong>Con mucho amorrrrrr ❤️</strong>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
(() => {
  'use strict';

  /* ============================================================
     PARTÍCULAS / BRILLOS
     ============================================================ */
  const canvas = document.getElementById('sparkles');
  const ctx    = canvas.getContext('2d');
  const particles = [];

  function resizeCanvas(){
    const dpr = Math.min(window.devicePixelRatio || 1, 2);
    canvas.width  = window.innerWidth  * dpr;
    canvas.height = window.innerHeight * dpr;
    canvas.style.width  = window.innerWidth  + 'px';
    canvas.style.height = window.innerHeight + 'px';
    ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
  }
  window.addEventListener('resize', resizeCanvas);
  resizeCanvas();

  const PALETTE = ['#ffd700', '#ffea00', '#ffffff', '#ffb300'];

  class Particle{
    constructor(x, y, color, vx, vy, size, opts = {}){
      this.x = x;
      this.y = y;
      this.color = color;
      this.vx = vx;
      this.vy = vy;
      this.size = size;
      this.alpha = 1;
      this.decay   = opts.decay   ?? (Math.random() * 0.014 + 0.006);
      this.gravity = opts.gravity ?? 0.012;
      this.blur    = opts.blur    ?? 12;
      this.star    = !!opts.star;
      this.rot     = Math.random() * Math.PI;
      this.spin    = (Math.random() - 0.5) * 0.08;
    }
    update(){
      this.x += this.vx;
      this.y += this.vy;
      this.vy += this.gravity;
      this.vx *= 0.995;
      this.alpha -= this.decay;
      this.rot += this.spin;
    }
    draw(){
      ctx.save();
      ctx.globalAlpha = Math.max(0, this.alpha);
      ctx.fillStyle = this.color;
      ctx.shadowBlur = this.blur;
      ctx.shadowColor = this.color;
      ctx.beginPath();
      if (this.star){
        // estrellita de 4 puntas
        const r = this.size * 2.4, ri = r * 0.28;
        ctx.translate(this.x, this.y);
        ctx.rotate(this.rot);
        for (let i = 0; i < 8; i++){
          const rad = i % 2 === 0 ? r : ri;
          const a = i * Math.PI / 4;
          ctx.lineTo(Math.cos(a) * rad, Math.sin(a) * rad);
        }
        ctx.closePath();
      } else {
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      }
      ctx.fill();
      ctx.restore();
    }
  }

  function spawnAmbient(){
    particles.push(new Particle(
      Math.random() * window.innerWidth,
      window.innerHeight + 12,
      Math.random() > 0.35 ? '#ffd700' : '#ffffff',
      (Math.random() - 0.5) * 0.7,
      -Math.random() * 1.1 - 0.25,
      Math.random() * 2.4 + 1
    ));
  }

  for (let i = 0; i < 45; i++){
    particles.push(new Particle(
      Math.random() * window.innerWidth,
      Math.random() * window.innerHeight,
      PALETTE[(Math.random() * PALETTE.length) | 0],
      (Math.random() - 0.5) * 0.6,
      -Math.random() * 0.7 - 0.15,
      Math.random() * 2.4 + 1
    ));
  }

  function animate(){
    ctx.clearRect(0, 0, window.innerWidth, window.innerHeight);

    if (particles.length < 45 && Math.random() < 0.35) spawnAmbient();

    for (let i = particles.length - 1; i >= 0; i--){
      const p = particles[i];
      p.update();
      p.draw();
      if (p.alpha <= 0 || p.y > window.innerHeight + 60 || p.y < -80){
        particles.splice(i, 1);
      }
    }
    requestAnimationFrame(animate);
  }
  animate();

  window.addEventListener('mousemove', e => {
    if (Math.random() < 0.22){
      particles.push(new Particle(
        e.clientX, e.clientY,
        Math.random() > 0.5 ? '#ffea00' : '#ffc107',
        (Math.random() - 0.5) * 1.8,
        (Math.random() - 0.5) * 1.8 - 0.4,
        Math.random() * 2.6 + 1
      ));
    }
  }, { passive: true });

  /* ============================================================
     GENERADOR DEL CAMPO DE GIRASOLES (con tallo y hojas)
     ============================================================ */
  const flowersLayer = document.getElementById('flowersLayer');
  const fieldDefs    = document.getElementById('fieldDefs');

  const FLOWER_PALETTE = ['#ffd60a', '#ffcc00', '#ffe066', '#f5b800', '#ffb400'];
  const FOG        = '#5a3212';                 // bruma cálida del atardecer
  const BANDS      = 4;                         // bandas de profundidad (lejos → cerca)
  const FOG_AMOUNT = [0.62, 0.42, 0.22, 0.04];  // más bruma cuanto más lejos
  const STEM_LEN   = 340;                       // largo del tallo (unidades del viewBox)

  function hexToRgb(h){
    const n = parseInt(h.slice(1), 16);
    return [(n >> 16) & 255, (n >> 8) & 255, n & 255];
  }
  function mix(a, b, t){
    const A = hexToRgb(a), B = hexToRgb(b);
    return '#' + A.map((v, i) =>
      Math.round(v + (B[i] - v) * t).toString(16).padStart(2, '0')
    ).join('');
  }

  // Cabezas y hojas se definen una sola vez y cada flor las reutiliza con <use>
  function buildFieldDefs(){
    let out = `<path id="fpetal" d="M0 -14 C 7 -24 10 -42 0 -56 C -10 -42 -7 -24 0 -14 Z"/>`;

    for (let b = 0; b < BANDS; b++){
      const t = FOG_AMOUNT[b];
      const leaf     = mix('#4f9a2a', FOG, t);
      const leafDark = mix('#2f6a16', FOG, t);

      out += `<g id="fleaf${b}">
        <path d="M0 0 C 14 -17 44 -19 66 0 C 44 17 14 15 0 0 Z" fill="${leaf}"/>
        <path d="M2 0 Q 34 -3 62 0" fill="none" stroke="${leafDark}" stroke-width="1.6"/>
      </g>`;

      FLOWER_PALETTE.forEach((c, v) => {
        const front = mix(c, FOG, t);
        const mid   = mix(mix(c, '#e08a00', 0.5), FOG, t);
        const back  = mix('#c67a00', FOG, t);
        const core  = mix('#4a2c10', FOG, t * 0.5);
        const ring  = mix('#c99a2e', FOG, t);

        let petals = '';
        for (let i = 0; i < 16; i++)
          petals += `<use href="#fpetal" transform="rotate(${i * 22.5})" fill="${back}"/>`;
        for (let i = 0; i < 16; i++)
          petals += `<use href="#fpetal" transform="rotate(${i * 22.5 + 11.25}) scale(.93)" fill="${mid}"/>`;
        for (let i = 0; i < 14; i++)
          petals += `<use href="#fpetal" transform="rotate(${(i * 360 / 14 + 5).toFixed(2)}) scale(.8)" fill="${front}"/>`;

        out += `<g id="fhead${b}_${v}">${petals}
          <circle r="21" fill="${core}"/>
          <circle r="21" fill="url(#fieldSeeds)"/>
          <circle r="21" fill="none" stroke="${ring}" stroke-width="1.6" opacity=".8"/>
          <circle r="9"  fill="#000" opacity=".18"/>
        </g>`;
      });
    }
    fieldDefs.insertAdjacentHTML('beforeend', out);
  }

  function flowerSVG(v, b, lean){
    const t      = FOG_AMOUNT[b];
    const stem   = mix('#3f7a1e', FOG, t);
    const stemHi = mix('#6fb23a', FOG, t);
    const soil   = mix('#1c3a0c', FOG, t);
    const L      = STEM_LEN;

    return `<svg viewBox="-60 -60 120 ${60 + L}" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
      <path d="M0 12 C ${lean} 110 ${(-lean * 0.6).toFixed(1)} 230 0 ${L}"
            fill="none" stroke="${stem}" stroke-width="7" stroke-linecap="round"/>
      <path d="M-1.8 40 C ${lean - 1.8} 130 ${(-lean * 0.6 - 1.8).toFixed(1)} 230 -1.8 ${L - 12}"
            fill="none" stroke="${stemHi}" stroke-width="1.6" opacity=".5"/>
      <use href="#fleaf${b}" transform="translate(1,205) rotate(-28)"/>
      <use href="#fleaf${b}" transform="translate(-1,258) scale(-1,1) rotate(-30)"/>
      <ellipse cx="0" cy="${L}" rx="17" ry="4" fill="${soil}"/>
      <path d="M-15 ${L} Q-11 ${L - 26} -5 ${L} M-7 ${L} Q-1 ${L - 34} 5 ${L} M3 ${L} Q11 ${L - 24} 17 ${L}"
            fill="none" stroke="${stem}" stroke-width="3" stroke-linecap="round"/>
      <use href="#fhead${b}_${v}"/>
    </svg>`;
  }

  function buildField(){
    buildFieldDefs();

    const vw    = Math.max(window.innerWidth, 800);
    const total = window.innerWidth < 640 ? 130 : 210;   // ← cantidad de girasoles
    const Z_MIN = -3600, Z_MAX = -260;                    // profundidad del campo
    const GROUND = 440;                                   // altura del suelo respecto al horizonte
    const PATH   = 110;                                   // camino libre en el centro

    const frag = document.createDocumentFragment();

    for (let i = 0; i < total; i++){
      const zr   = Math.pow(Math.random(), 1.25);         // sesgo hacia el fondo
      const z    = Z_MIN + zr * (Z_MAX - Z_MIN);
      const band = Math.min(BANDS - 1, Math.floor(zr * BANDS));

      const halfW = (vw / 2) * (900 - z) / 900 * 1.15;    // ancho visible a esa profundidad
      const x = (Math.random() < 0.5 ? -1 : 1) *
                (PATH + Math.random() * Math.max(40, halfW - PATH));
      const y = GROUND + (Math.random() - 0.5) * 26;
      const s = 0.6 + Math.random() * 0.75;
      const r = (Math.random() - 0.5) * 8;
      const lean = Math.round((Math.random() - 0.5) * 16);
      const v = (Math.random() * FLOWER_PALETTE.length) | 0;

      const flower = document.createElement('div');
      flower.className = 'field-flower';
      flower.style.translate = `${x.toFixed(1)}px ${y.toFixed(1)}px ${z.toFixed(1)}px`;
      flower.style.scale     = s.toFixed(3);
      flower.style.rotate    = r.toFixed(2) + 'deg';
      flower.style.animationDuration = (3.4 + Math.random() * 2.6).toFixed(2) + 's';
      flower.style.animationDelay    = (-Math.random() * 6).toFixed(2) + 's';
      flower.innerHTML = flowerSVG(v, band, lean);

      frag.appendChild(flower);
    }

    flowersLayer.appendChild(frag);
  }
  buildField();

  /* ============================================================
     ZOOM / TRANSICIÓN DE ESCENAS
     ============================================================ */
  const fieldScene   = document.getElementById('fieldScene');
  const bouquetScene = document.getElementById('bouquetScene');
  const fieldUI      = document.getElementById('fieldUI');
  const blackout     = document.getElementById('transitionBlack');
  const btnEnter     = document.getElementById('btnEnter');

  let started = false;

  function startJourney(){
    if (started) return;
    started = true;

    // Ocultar UI del campo
    fieldUI.classList.add('fade-out');

    // Iniciar zoom
    requestAnimationFrame(() => {
      flowersLayer.classList.add('zoomed');
    });

    // Fundido a negro cerca del final del zoom
    setTimeout(() => {
      blackout.classList.add('active');
    }, 4900);

    // Cambiar de escena mientras está en negro
    setTimeout(() => {
      fieldScene.classList.remove('active');
      bouquetScene.classList.add('active');
    }, 5500);

    // Quitar el negro para revelar el ramo
    setTimeout(() => {
      blackout.classList.remove('active');
    }, 5750);

  }

  btnEnter.addEventListener('click', startJourney);

  /* ============================================================
     EXPLOSIÓN + LLUVIA DE BRILLOS
     ============================================================ */
  let rainUntil = 0;
  let rainTimer = null;

  function sparkleRain(){
    rainUntil = performance.now() + 3800;
    if (rainTimer) return;

    rainTimer = setInterval(() => {
      if (performance.now() > rainUntil){
        clearInterval(rainTimer);
        rainTimer = null;
        return;
      }
      for (let i = 0; i < 4; i++){
        particles.push(new Particle(
          Math.random() * window.innerWidth,
          -12,
          PALETTE[(Math.random() * PALETTE.length) | 0],
          (Math.random() - 0.5) * 0.8,
          Math.random() * 2.2 + 1.6,
          Math.random() * 2.8 + 1.4,
          {
            decay:   Math.random() * 0.003 + 0.002,
            gravity: 0.01,
            blur:    9,
            star:    Math.random() < 0.5
          }
        ));
      }
    }, 70);
  }

  function burstSparkles(){
    const cx = window.innerWidth  / 2;
    const cy = window.innerHeight * 0.42;

    for (let i = 0; i < 90; i++){
      const angle = Math.random() * Math.PI * 2;
      const speed = Math.random() * 7 + 2;
      particles.push(new Particle(
        cx, cy,
        PALETTE[(Math.random() * PALETTE.length) | 0],
        Math.cos(angle) * speed,
        Math.sin(angle) * speed - 1,
        Math.random() * 4 + 2,
        { star: Math.random() < 0.4 }
      ));
    }

    sparkleRain();
    showToast('✨ ¡Que tengas un día lleno de luz y alegría! ✨');
  }

  /* ============================================================
     TOAST
     ============================================================ */
  let toastTimer = null;
  function showToast(text){
    const toast = document.getElementById('toast');
    toast.textContent = text;
    toast.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => toast.classList.remove('show'), 3200);
  }

  /* ============================================================
     MODAL DE CARTA
     ============================================================ */
  const modal = document.getElementById('cardModal');

  function openCard(){ modal.classList.add('active'); }
  function closeCard(){ modal.classList.remove('active'); }

  modal.addEventListener('click', e => {
    if (e.target === modal) closeCard();
  });

  document.addEventListener('keydown', e => {
    if (e.key === 'Escape' && modal.classList.contains('active')) closeCard();
  });

  /* ============================================================
     EVENTOS
     ============================================================ */
  document.getElementById('btnCard').addEventListener('click', openCard);
  document.getElementById('btnCloseCard').addEventListener('click', closeCard);
  document.getElementById('btnSparkles').addEventListener('click', burstSparkles);
})();
</script>

</body></html>
