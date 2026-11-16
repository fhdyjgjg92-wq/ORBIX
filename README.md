
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover">
<meta name="theme-color" content="#050b18">
<title>ORBIX 2.5 — Performance Edition</title>
<style>
:root{--cyan:#27d9ff;--panel:rgba(5,17,38,.94)}
*{box-sizing:border-box}
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#050b18;color:white;font-family:Arial,sans-serif;touch-action:none}
button{font:inherit;color:white;cursor:pointer;touch-action:manipulation;border:1px solid #36bfff88;background:linear-gradient(145deg,#124477,#07162d);border-radius:11px;padding:9px 12px}
button:active{transform:scale(.96)}
button:disabled{opacity:.4}
#app{position:fixed;inset:0;overflow:hidden}
#game{position:absolute;inset:0;width:100%;height:100%;display:block}
#topbar{position:absolute;z-index:4;top:0;left:0;right:0;height:48px;padding:5px 12px;display:flex;align-items:center;justify-content:space-between;background:linear-gradient(#030916ed,transparent);pointer-events:none}
.logo{font-weight:1000;font-size:25px;letter-spacing:4px;color:var(--cyan);text-shadow:0 0 16px #168bff}
#stats{text-align:left;font-size:11px;line-height:1.6}
#stats b{color:var(--cyan)}
.panel{background:var(--panel);border:1px solid #27d9ff55;border-radius:12px;backdrop-filter:blur(7px)}
#leader{position:absolute;z-index:4;top:53px;right:9px;width:145px;padding:8px;font-size:11px;pointer-events:none}
#leader h3{margin:0 0 5px;color:var(--cyan);font-size:13px}
.leadrow{display:flex;justify-content:space-between;padding:2px 0}
#mapBox{position:absolute;z-index:4;right:9px;bottom:9px;width:118px;height:80px;padding:4px;pointer-events:none}
#mini{width:100%;height:100%;display:block}
#tools{position:absolute;z-index:6;top:54px;left:9px;display:flex;flex-direction:column;gap:6px}
#tools button{font-size:11px;padding:8px 10px;background:#07172be8}
#joystick{position:absolute;z-index:6;left:12px;bottom:12px;width:94px;height:94px;border:2px solid #67caff88;background:#3b91ff22;border-radius:50%;display:flex;align-items:center;justify-content:center;touch-action:none}
#stick{width:36px;height:36px;border-radius:50%;background:var(--cyan);box-shadow:0 0 16px var(--cyan);pointer-events:none}
#actions{position:absolute;z-index:6;left:117px;bottom:19px;display:flex;gap:8px;direction:ltr}
.action{width:59px;height:59px;padding:3px;border-radius:50%;font-size:11px;font-weight:bold}
#notice{position:absolute;z-index:12;top:57px;left:50%;transform:translateX(-50%);display:none;padding:8px 13px;border:1px solid #27d9ff88;border-radius:18px;background:#07172bee;font-size:12px;white-space:nowrap;pointer-events:none}
#menu{position:absolute;z-index:20;inset:0;display:flex;align-items:center;justify-content:center;padding:10px;background:radial-gradient(ellipse,#0c2852ed,#030916f8)}
#menuCard{width:min(430px,94vw);max-height:94vh;overflow:auto;text-align:center;padding:18px;border:1px solid #27d9ff77;border-radius:20px;background:linear-gradient(145deg,#0b1d38,#050b18);box-shadow:0 0 40px #087dff33;touch-action:pan-y}
#menu h1{font-size:42px;letter-spacing:7px;color:var(--cyan);margin:0;text-shadow:0 0 22px #168bff}
.sub{font-size:11px;color:#a9c9e8;margin:5px 0 14px}
.menuBtn{width:100%;padding:11px;margin:5px 0}
.primary{background:linear-gradient(90deg,#0877d8,#08b9d8);font-weight:bold;font-size:17px}
#mapChoices{display:flex;gap:7px;margin:9px 0}
.mapChoice{flex:1;padding:9px 3px;font-size:11px;background:#10233d}
.mapChoice.active{border-color:var(--cyan);box-shadow:0 0 12px #0af6}
#rotate{display:none;position:fixed;z-index:100;inset:0;background:#050b18;align-items:center;justify-content:center;text-align:center;font-size:20px;padding:24px}
@media(orientation:portrait) and (max-width:900px){#rotate{display:flex}}
@media(max-height:430px){
#topbar{height:38px}
#leader{top:43px;width:118px;padding:6px}
#joystick{width:76px;height:76px;left:8px;bottom:7px}
#stick{width:30px;height:30px}
#actions{left:94px;bottom:10px}
.action{width:49px;height:49px}
#mapBox{width:100px;height:66px}
#tools{top:42px}
#menuCard{padding:10px}
}
</style>
</head>
<body>
<div id="app">
<canvas id="game"></canvas>

<header id="topbar">
<div class="logo">ORBIX</div>
<div id="stats">
الكتلة: <b id="mass">400</b><br>
النقاط: <b id="score">0</b> · الأفضل: <b id="best">0</b>
</div>
</header>

<div id="leader" class="panel">
<h3>🏆 المتصدرون</h3>
<div id="leaderRows"></div>
</div>

<div id="tools">
<button id="fullBtn">⛶ ملء الشاشة</button>
<button id="menuBtn">☰ القائمة</button>
</div>

<div id="mapBox" class="panel"><canvas id="mini"></canvas></div>
<div id="joystick"><div id="stick"></div></div>

<div id="actions">
<button class="action" id="ejectBtn">دعم<br>●</button>
<button class="action" id="splitBtn">تقسيم<br>Ⅱ</button>
</div>

<div id="notice"></div>

<div id="menu">
<div id="menuCard">
<h1>ORBIX</h1>
<div class="sub">VERSION 2.5 · PERFORMANCE EDITION</div>
<div style="font-size:13px">اختر ساحة المعركة</div>

<div id="mapChoices">
<button class="mapChoice active" data-map="neon">🌌 المجرة</button>
<button class="mapChoice" data-map="ice">❄️ الجليد</button>
<button class="mapChoice" data-map="toxic">☣️ السامة</button>
</div>

<button class="menuBtn primary" id="startBtn">▶ ابدأ اللعبة</button>
<button class="menuBtn" id="restartBtn">↻ إعادة الجولة</button>
<button class="menuBtn" disabled>👥 اللعب الجماعي الحقيقي — قريباً</button>

<div style="font-size:10px;color:#7996b7;line-height:1.8;margin-top:8px">
طور الأوفلاين ضد خصوم آليين. اللعب الجماعي يحتاج خادماً واتصالاً شبكياً.
</div>
</div>
</div>
</div>

<div id="rotate">📱<br><br>أدر هاتفك إلى الوضع الأفقي للعب ORBIX</div>

<script>
'use strict';
(()=>{
const $=id=>document.getElementById(id);
const canvas=$('game'),ctx=canvas.getContext('2d',{alpha:false});
const mini=$('mini'),mctx=mini.getContext('2d');

const WORLD={w:4400,h:3200};
const MAX_PIECES=16,MAX_VIRUSES=32,MAX_BOTS=24;
const FOOD_TARGET=520,BOT_COUNT=15;
const MERGE_DELAY=12000;
const MAX_PELLETS=35,PELLET_COST=40;
const SPLIT_COOLDOWN=650,EJECT_COOLDOWN=260;

const maps={
 neon:{bg:'#07152c',grid:'rgba(48,140,255,.14)',edge:'#27d9ff',food:['#27d9ff','#ff5ca8','#a6ff65','#b78cff']},
 ice:{bg:'#10253b',grid:'rgba(180,230,255,.14)',edge:'#a8edff',food:['#b5f3ff','#62d6ff','#e1ffff','#9ebcff']},
 toxic:{bg:'#101d1a',grid:'rgba(100,255,130,.14)',edge:'#7dff8a',food:['#8dff70','#d3ff56','#45e6a0','#d0ffae']}
};

const rand=(a,b)=>a+Math.random()*(b-a);
const clamp=(v,a,b)=>Math.max(a,Math.min(b,v));
const dist=(a,b)=>Math.hypot(a.x-b.x,a.y-b.y);
const mass=p=>p.r*p.r;
const now=()=>performance.now();

function safeGet(k,d){
 try{
  const v=localStorage.getItem(k);
  return v===null?d:v;
 }catch(e){return d}
}
function safeSet(k,v){
 try{localStorage.setItem(k,String(v))}
 catch(e){}
}

let mapName=safeGet('orbixMap','neon');
if(!maps[mapName])mapName='neon';

let W=innerWidth,H=innerHeight,DPR=1;
let food=[],viruses=[],bots=[],pieces=[],pellets=[];
let camera={x:0,y:0,zoom:1};
let running=false,score=0,best=Math.max(0,Number(safeGet('orbixBest','0'))||0);
let shieldUntil=0,lastTime=0,noticeTimer=0;
let leaderTimer=0,lastSplit=0,lastEject=0;
let nextBotId=0;
let input={x:0,y:0},keys={},pointerId=null;
let direction={x:1,y:0};
let accumulator=0;
let pausedByVisibility=false;

const totalMass=()=>pieces.reduce((s,p)=>s+mass(p),0);

function toast(s){
 const el=$('notice');
 el.textContent=s;
 el.style.display='block';
 clearTimeout(noticeTimer);
 noticeTimer=setTimeout(()=>el.style.display='none',1700);
}

function resize(){
 DPR=Math.min(window.devicePixelRatio||1,2);
 W=innerWidth;H=innerHeight;
 canvas.width=Math.max(1,Math.round(W*DPR));
 canvas.height=Math.max(1,Math.round(H*DPR));
 canvas.style.width=W+'px';
 canvas.style.height=H+'px';
 ctx.setTransform(DPR,0,0,DPR,0,0);

 const r=mini.getBoundingClientRect();
 mini.width=Math.max(1,Math.round(r.width*DPR));
 mini.height=Math.max(1,Math.round(r.height*DPR));
 mctx.setTransform(DPR,0,0,DPR,0,0);

 if(!running)render();
}
addEventListener('resize',resize);
addEventListener('orientationchange',()=>setTimeout(resize,150));

function randomFood(){
 const c=maps[mapName].food;
 return {
  x:rand(25,WORLD.w-25),
  y:rand(25,WORLD.h-25),
  r:rand(4,6),
  color:c[(Math.random()*c.length)|0]
 };
}

function newPlayer(){
 return {
  x:WORLD.w/2,y:WORLD.h/2,r:20,
  vx:0,vy:0,kx:0,ky:0,
  mergeAt:0,virusSafeUntil:0,
  color:'#27d9ff'
 };
}

function newBot(id=nextBotId++,x,y,r){
 return {
  id,
  x:x??rand(100,WORLD.w-100),
  y:y??rand(100,WORLD.h-100),
  r:r??rand(20,34),
  color:['#ff4d8d','#a87aff','#ffb84d','#6dffb0','#ff765b'][id%5],
  tx:rand(100,WORLD.w-100),
  ty:rand(100,WORLD.h-100),
  think:rand(180,420),
  skill:rand(.85,1.2),
  vx:0,vy:0,
  virusSafeUntil:0
 };
}

function addVirus(x,y,r=43){
 if(viruses.length>=MAX_VIRUSES)return false;
 viruses.push({x,y,r,charge:0,dx:1,dy:0});
 return true;
}

function resetWorld(){
 food=Array.from({length:FOOD_TARGET},randomFood);
 viruses=[];
 bots=[];
 pieces=[newPlayer()];
 pellets=[];
 score=0;
 nextBotId=0;
 shieldUntil=now()+3000;
 lastSplit=0;
 lastEject=0;

 for(let i=0;i<24;i++)
  addVirus(rand(100,WORLD.w-100),rand(100,WORLD.h-100));

 for(let i=0;i<BOT_COUNT;i++)
  bots.push(newBot());

 camera={x:0,y:0,zoom:1};
 leaderTimer=0;
 updateStats(true);
}

function startGame(){
 resetWorld();
 running=true;
 pausedByVisibility=false;
 $('menu').style.display='none';
 clearControls();
 lastTime=0;
 accumulator=0;
 toast('بدأت الجولة! اجمع الطعام واحذر الفيروسات');

 try{
  if(screen.orientation?.lock)
   screen.orientation.lock('landscape').catch(()=>{});
 }catch(e){}

 requestAnimationFrame(loop);
}

function showMenu(){
 running=false;
 clearControls();
 $('menu').style.display='flex';
 render();
}

function chooseMap(n){
 if(!maps[n])return;
 mapName=n;
 safeSet('orbixMap',n);

 document.querySelectorAll('.mapChoice').forEach(b=>
  b.classList.toggle('active',b.dataset.map===n)
 );

 if(running){
  food=Array.from({length:FOOD_TARGET},randomFood);
  toast('تم تغيير أجواء الخريطة');
 }
}

document.querySelectorAll('.mapChoice').forEach(b=>
 b.addEventListener('click',()=>chooseMap(b.dataset.map))
);
chooseMap(mapName);

/* التحكم */

function clearControls(){
 keys={};
 stopJoy();
}

function stopJoy(e){
 if(e&&pointerId!==null&&e.pointerId!==pointerId)return;
 pointerId=null;
 input={x:0,y:0};
 $('stick').style.transform='translate(0,0)';
}

function joystickMove(e){
 const r=$('joystick').getBoundingClientRect();
 const max=r.width*.34;
 let dx=e.clientX-(r.left+r.width/2);
 let dy=e.clientY-(r.top+r.height/2);
 const l=Math.hypot(dx,dy);

 if(l>max){
  dx=dx/l*max;
  dy=dy/l*max;
 }

 $('stick').style.transform=`translate(${dx}px,${dy}px)`;
 input={x:dx/max,y:dy/max};

 const n=Math.hypot(input.x,input.y);
 if(n>.08)direction={x:input.x/n,y:input.y/n};
}

$('joystick').addEventListener('pointerdown',e=>{
 if(!running)return;
 e.preventDefault();
 pointerId=e.pointerId;
 $('joystick').setPointerCapture(pointerId);
 joystickMove(e);
});

$('joystick').addEventListener('pointermove',e=>{
 if(e.pointerId===pointerId)joystickMove(e);
});
$('joystick').addEventListener('pointerup',stopJoy);
$('joystick').addEventListener('pointercancel',stopJoy);
$('joystick').addEventListener('lostpointercapture',stopJoy);

addEventListener('blur',clearControls);

document.addEventListener('visibilitychange',()=>{
 if(document.hidden){
  clearControls();
  if(running)pausedByVisibility=true;
 }else if(running&&pausedByVisibility){
  pausedByVisibility=false;
  lastTime=0;
  requestAnimationFrame(loop);
 }
});

addEventListener('keydown',e=>{
 const k=e.key.toLowerCase();
 keys[k]=true;

 if(['arrowup','arrowdown','arrowleft','arrowright',' '].includes(k))
  e.preventDefault();

 if(k===' '&&!e.repeat)splitPlayer();
 if(k==='e'&&!e.repeat)ejectMass();
});

addEventListener('keyup',e=>{
 keys[e.key.toLowerCase()]=false;
});

function getDirection(){
 let x=input.x,y=input.y;

 if(keys.a||keys.arrowleft)x--;
 if(keys.d||keys.arrowright)x++;
 if(keys.w||keys.arrowup)y--;
 if(keys.s||keys.arrowdown)y++;

 const l=Math.hypot(x,y);
 if(l>1){x/=l;y/=l}

 const n=Math.hypot(x,y);
 if(n>.08)direction={x:x/n,y:y/n};

 return{x,y};
}

/* حركة اللاعب */

function movePlayer(dt){
 const d=getDirection();
 const step=dt/16.666;

 for(const p of pieces){
  const speed=Math.max(1.5,4.3-p.r*.023);
  p.vx+=(d.x*speed-p.vx)*Math.min(1,.22*step);
  p.vy+=(d.y*speed-p.vy)*Math.min(1,.22*step);

  p.x=clamp(p.x+(p.vx+p.kx)*step,p.r,WORLD.w-p.r);
  p.y=clamp(p.y+(p.vy+p.ky)*step,p.r,WORLD.h-p.r);

  p.kx*=Math.pow(.86,step);
  p.ky*=Math.pow(.86,step);
 }

 for(let i=0;i<pieces.length;i++){
  for(let j=i+1;j<pieces.length;j++){
   const a=pieces[i],b=pieces[j];
   const dx=b.x-a.x,dy=b.y-a.y;
   const l=Math.hypot(dx,dy)||.001;
   const min=(a.r+b.r)*.72;

   if(l<min){
    const push=(min-l)*.035;
    const nx=dx/l,ny=dy/l;

    a.x=clamp(a.x-nx*push,a.r,WORLD.w-a.r);
    a.y=clamp(a.y-ny*push,a.r,WORLD.h-a.r);
    b.x=clamp(b.x+nx*push,b.r,WORLD.w-b.r);
    b.y=clamp(b.y+ny*push,b.r,WORLD.h-b.r);
   }
  }
 }
}

/* التقسيم */

function splitPlayer(){
 if(!running)return;

 const t=now();
 if(t-lastSplit<SPLIT_COOLDOWN){
  toast('انتظر قليلاً قبل التقسيم');
  return;
 }

 if(pieces.length>=MAX_PIECES){
  toast('وصلت إلى الحد الأقصى للقطع');
  return;
 }

 const candidates=pieces.filter(p=>p.r>=20);
 if(!candidates.length){
  toast('تحتاج كتلة أكبر للتقسيم');
  return;
 }

 let added=0;

 for(const p of [...candidates]){
  if(pieces.length>=MAX_PIECES)break;
  if(!pieces.includes(p))continue;

  const oldMass=mass(p);
  const newR=Math.sqrt(oldMass/2);
  p.r=newR;
  p.mergeAt=t+MERGE_DELAY;

  pieces.push({
   x:clamp(p.x+direction.x*newR*.9,newR,WORLD.w-newR),
   y:clamp(p.y+direction.y*newR*.9,newR,WORLD.h-newR),
   r:newR,
   vx:p.vx,vy:p.vy,
   kx:direction.x*11,
   ky:direction.y*11,
   mergeAt:t+MERGE_DELAY,
   virusSafeUntil:t+1200,
   color:p.color
  });

  added++;
 }

 if(added){
  lastSplit=t;
  toast('⚡ انقسام ناجح!');
 }
}

/* الدعم */

function ejectMass(){
 if(!running)return;

 const t=now();
 if(t-lastEject<EJECT_COOLDOWN){
  toast('انتظر لحظة قبل إطلاق الدعم');
  return;
 }

 if(pellets.length>=MAX_PELLETS){
  toast('وصلت إلى الحد الأقصى للحبيبات');
  return;
 }

 let fired=0;

 for(const p of pieces){
  if(pellets.length>=MAX_PELLETS)break;

  const oldMass=mass(p);
  if(p.r<18||oldMass-PELLET_COST<100)continue;

  p.r=Math.sqrt(oldMass-PELLET_COST);

  pellets.push({
   x:clamp(p.x+direction.x*(p.r+9),8,WORLD.w-8),
   y:clamp(p.y+direction.y*(p.r+9),8,WORLD.h-8),
   vx:direction.x*12,
   vy:direction.y*12,
   r:6,
   value:PELLET_COST,
   life:8500,
   born:t,
   owner:'player'
  });

  fired++;
 }

 if(fired){
  lastEject=t;
  toast('🚀 أطلقت حبيبات الدعم');
 }else{
  toast('كتلتك لا تكفي لإطلاق الدعم');
 }
}

$('splitBtn').addEventListener('click',splitPlayer);
$('ejectBtn').addEventListener('click',ejectMass);

function grow(p,value){
 p.r=Math.sqrt(Math.max(1,mass(p)+value));
}

function eatFood(){
 for(let i=food.length-1;i>=0;i--){
  const f=food[i];
  let eaten=false;

  for(const p of pieces){
   if(dist(p,f)<p.r+f.r){
    grow(p,f.r*f.r);
    score++;
    eaten=true;
    break;
   }
  }

  if(!eaten){
   for(const b of bots){
    if(dist(b,f)<b.r+f.r){
     grow(b,f.r*f.r);
     eaten=true;
     break;
    }
   }
  }

  if(eaten)food.splice(i,1);
 }

 while(food.length<FOOD_TARGET)
  food.push(randomFood());
}

/* الحبيبات والفيروسات */

function updatePellets(dt){
 const step=dt/16.666;
 const t=now();

 for(let i=pellets.length-1;i>=0;i--){
  const q=pellets[i];

  q.x+=q.vx*step;
  q.y+=q.vy*step;

  q.vx*=Math.pow(.965,step);
  q.vy*=Math.pow(.965,step);
  q.life-=dt;

  if(q.life<=0||q.x<0||q.y<0||q.x>WORLD.w||q.y>WORLD.h){
   pellets.splice(i,1);
   continue;
  }

  let hit=false;

  for(const v of viruses){
   if(dist(q,v)<q.r+v.r){
    v.charge++;

    const speed=Math.hypot(q.vx,q.vy)||1;
    v.dx=q.vx/speed;
    v.dy=q.vy/speed;

    pellets.splice(i,1);
    hit=true;

    if(v.charge>=7){
     v.charge=0;
     addVirus(
      clamp(v.x+v.dx*105,60,WORLD.w-60),
      clamp(v.y+v.dy*105,60,WORLD.h-60)
     );
    }
    break;
   }
  }

  if(hit||t-q.born<450)continue;

  if(q.owner==='player'&&t-q.born<900)continue;

  let eaten=false;

  for(const p of pieces){
   if(dist(p,q)<p.r+q.r){
    grow(p,q.value);
    eaten=true;
    break;
   }
  }

  if(!eaten){
   for(const b of bots){
    if(dist(b,q)<b.r+q.r){
     grow(b,q.value);
     eaten=true;
     break;
    }
   }
  }

  if(eaten)pellets.splice(i,1);
 }
}

function canEat(big,small){
 return big.r>small.r*1.16&&
 dist(big,small)<big.r-small.r*.18;
}

/* الذكاء الاصطناعي */

function botThink(b){
 let threat=null,threatD=Infinity;
 let prey=null,preyD=Infinity;

 for(const p of pieces){
  const d=dist(b,p);

  if(p.r>b.r*1.16&&d<360&&d<threatD){
   threat=p;
   threatD=d;
  }else if(b.r>p.r*1.2&&d<470&&d<preyD){
   prey=p;
   preyD=d;
  }
 }

 for(const other of bots){
  if(other===b)continue;

  const d=dist(b,other);

  if(other.r>b.r*1.16&&d<290&&d<threatD){
   threat=other;
   threatD=d;
  }else if(b.r>other.r*1.2&&d<380&&d<preyD){
   prey=other;
   preyD=d;
  }
 }

 if(threat){
  b.tx=clamp(b.x+(b.x-threat.x)*2.2,40,WORLD.w-40);
  b.ty=clamp(b.y+(b.y-threat.y)*2.2,40,WORLD.h-40);
  return;
 }

 if(prey){
  b.tx=prey.x;
  b.ty=prey.y;
  return;
 }

 let target=null,bestD=Infinity;

 for(const f of food){
  const d=(f.x-b.x)**2+(f.y-b.y)**2;
  if(d<bestD){bestD=d;target=f}
 }

 if(target){
  b.tx=target.x;
  b.ty=target.y;
 }else{
  b.tx=rand(60,WORLD.w-60);
  b.ty=rand(60,WORLD.h-60);
 }
}

function updateBots(dt){
 const step=dt/16.666;
 const t=now();

 for(const b of bots){
  b.think-=dt;

  if(b.think<=0){
   b.think=rand(260,520)/b.skill;
   botThink(b);
  }

  for(const v of viruses){
   const d=dist(b,v);

   if(d<b.r+v.r+35&&b.r<v.r*1.2){
    b.tx=clamp(b.x+(b.x-v.x)*2,40,WORLD.w-40);
    b.ty=clamp(b.y+(b.y-v.y)*2,40,WORLD.h-40);
   }
  }

  const dx=b.tx-b.x,dy=b.ty-b.y;
  const l=Math.hypot(dx,dy)||1;
  const speed=Math.max(1.5,3.8-b.r*.019)*b.skill;

  b.vx+=(dx/l*speed-b.vx)*Math.min(1,.12*step);
  b.vy+=(dy/l*speed-b.vy)*Math.min(1,.12*step);

  b.x=clamp(b.x+b.vx*step,b.r,WORLD.w-b.r);
  b.y=clamp(b.y+b.vy*step,b.r,WORLD.h-b.r);
 }

 const dead=new Set();

 for(let i=0;i<bots.length;i++){
  const a=bots[i];
  if(dead.has(a))continue;

  for(let j=i+1;j<bots.length;j++){
   const b=bots[j];
   if(dead.has(b))continue;

   if(canEat(a,b)){
    grow(a,mass(b)*.85);
    dead.add(b);
   }else if(canEat(b,a)){
    grow(b,mass(a)*.85);
    dead.add(a);
    break;
   }
  }
 }

 if(dead.size)bots=bots.filter(b=>!dead.has(b));

 while(bots.length<BOT_COUNT)bots.push(newBot());
 if(bots.length>MAX_BOTS)bots.length=MAX_BOTS;
}

/* انفجار الخصوم */

function virusHitBot(b,index,t){
 const bMass=mass(b);
 const count=3;

 if(b.r<48||bots.length+count-1>MAX_BOTS){
  b.virusSafeUntil=t+1500;
  b.vx+=rand(-5,5);
  b.vy+=rand(-5,5);
  return;
 }

 const newR=Math.sqrt(bMass/count);
 bots.splice(index,1);

 for(let i=0;i<count;i++){
  const a=i*Math.PI*2/count+rand(-.2,.2);

  const child=newBot(
   undefined,
   clamp(b.x+Math.cos(a)*(b.r+newR)*.5,newR,WORLD.w-newR),
   clamp(b.y+Math.sin(a)*(b.r+newR)*.5,newR,WORLD.h-newR),
   newR
  );

  child.tx=clamp(child.x+Math.cos(a)*180,40,WORLD.w-40);
  child.ty=clamp(child.y+Math.sin(a)*180,40,WORLD.h-40);
  child.virusSafeUntil=t+1800;
  child.vx=Math.cos(a)*7;
  child.vy=Math.sin(a)*7;

  bots.push(child);
 }
}

function checkBotViruses(){
 const t=now();

 for(let i=bots.length-1;i>=0;i--){
  const b=bots[i];

  if(t<(b.virusSafeUntil||0))continue;

  for(const v of viruses){
   if(dist(b,v)<b.r+v.r*.62&&b.r>v.r*1.08){
    virusHitBot(b,i,t);
    break;
   }
  }
 }

 while(bots.length>MAX_BOTS)bots.pop();
 while(bots.length<BOT_COUNT)bots.push(newBot());
}

/* انفجار اللاعب */

function explodePiece(p,t){
 const idx=pieces.indexOf(p);
 if(idx<0)return false;

 const slots=MAX_PIECES-pieces.length+1;
 if(slots<2)return false;

 const count=Math.min(
  8,
  Math.max(4,Math.ceil(p.r/22)),
  slots
 );

 if(count<2)return false;

 const r=p.r/Math.sqrt(count);
 const a0=rand(0,Math.PI*2);
 const parts=[];

 for(let i=0;i<count;i++){
  const a=a0+i*Math.PI*2/count;

  parts.push({
   x:clamp(p.x+Math.cos(a)*(p.r+r)*.65,r,WORLD.w-r),
   y:clamp(p.y+Math.sin(a)*(p.r+r)*.65,r,WORLD.h-r),
   r,
   vx:p.vx||0,
   vy:p.vy||0,
   kx:Math.cos(a)*10,
   ky:Math.sin(a)*10,
   mergeAt:t+MERGE_DELAY,
   virusSafeUntil:t+1800,
   color:p.color
  });
 }

 pieces.splice(idx,1,...parts);
 return true;
}

function checkViruses(){
 const t=now();
 if(t<shieldUntil)return;

 for(const p of [...pieces]){
  if(!pieces.includes(p)||t<(p.virusSafeUntil||0))continue;

  for(const v of viruses){
   if(dist(p,v)<p.r+v.r*.62&&p.r>v.r*1.08){
    if(explodePiece(p,t)){
     toast('☣️ انفجر جسمك إلى قطع!');
    }else{
     p.virusSafeUntil=t+1200;
     toast('القطع ممتلئة — ابتعد عن الفيروس');
    }
    break;
   }
  }
 }

 checkBotViruses();
}

/* صراع اللاعب والخصوم */

function checkPlayerCollisions(){
 if(now()<shieldUntil)return;

 for(let bi=bots.length-1;bi>=0;bi--){
  const b=bots[bi];

  for(let pi=pieces.length-1;pi>=0;pi--){
   const p=pieces[pi];

   if(canEat(p,b)){
    grow(p,mass(b)*.85);
    score+=10;
    bots.splice(bi,1);
    bots.push(newBot());
    break;
   }

   if(canEat(b,p)){
    grow(b,mass(p)*.85);
    pieces.splice(pi,1);
   }
  }

  if(!pieces.length){
   respawn();
   return;
  }
 }
}

function mergePieces(){
 const t=now();

 for(let i=0;i<pieces.length;i++){
  for(let j=pieces.length-1;j>i;j--){
   const a=pieces[i],b=pieces[j];

   if(t<a.mergeAt||t<b.mergeAt)continue;

   if(dist(a,b)<(a.r+b.r)*.48){
    const ma=mass(a),mb=mass(b);
    const sum=ma+mb;

    a.x=(a.x*ma+b.x*mb)/sum;
    a.y=(a.y*ma+b.y*mb)/sum;
    a.r=Math.sqrt(sum);
    a.vx=(a.vx*ma+b.vx*mb)/sum;
    a.vy=(a.vy*ma+b.vy*mb)/sum;

    pieces.splice(j,1);
   }
  }
 }
}

function respawn(){
 pieces=[newPlayer()];
 shieldUntil=now()+3000;
 toast('عدت للساحة — حماية مؤقتة');
}

/* الكاميرا */

function updateCamera(){
 if(!pieces.length)return;

 const total=totalMass()||1;

 const x=pieces.reduce((s,p)=>s+p.x*mass(p),0)/total;
 const y=pieces.reduce((s,p)=>s+p.y*mass(p),0)/total;

 camera.zoom=clamp(1.12-Math.sqrt(total)*.0025,.48,1.08);

 const vw=W/camera.zoom;
 const vh=H/camera.zoom;

 const targetX=clamp(x-vw/2,0,Math.max(0,WORLD.w-vw));
 const targetY=clamp(y-vh/2,0,Math.max(0,WORLD.h-vh));

 camera.x+=(targetX-camera.x)*.16;
 camera.y+=(targetY-camera.y)*.16;
}

/* الإحصائيات */

function updateStats(force=false){
 $('mass').textContent=Math.round(totalMass());
 $('score').textContent=score;

 if(score>best){
  best=score;
  safeSet('orbixBest',best);
 }

 $('best').textContent=best;

 if(force||now()-leaderTimer>500){
  drawLeaderboard();
  leaderTimer=now();
 }
}

function update(dt){
 if(!running)return;

 movePlayer(dt);
 updateBots(dt);
 eatFood();
 updatePellets(dt);
 checkViruses();
 checkPlayerCollisions();
 mergePieces();
 updateCamera();
 updateStats();
}

/* الرسم */

function circle(x,y,r,color,glow=0){
 ctx.fillStyle=color;
 ctx.shadowColor=color;
 ctx.shadowBlur=glow;
 ctx.beginPath();
 ctx.arc(x,y,r,0,Math.PI*2);
 ctx.fill();
 ctx.shadowBlur=0;
}

function drawGrid(){
 const m=maps[mapName];

 ctx.fillStyle=m.bg;
 ctx.fillRect(0,0,WORLD.w,WORLD.h);

 ctx.strokeStyle=m.grid;
 ctx.lineWidth=1;

 const left=Math.max(0,Math.floor(camera.x/70)*70);
 const right=Math.min(WORLD.w,camera.x+W/camera.zoom+70);
 const top=Math.max(0,Math.floor(camera.y/70)*70);
 const bottom=Math.min(WORLD.h,camera.y+H/camera.zoom+70);

 for(let x=left;x<=right;x+=70){
  ctx.beginPath();
  ctx.moveTo(x,top);
  ctx.lineTo(x,bottom);
  ctx.stroke();
 }

 for(let y=top;y<=bottom;y+=70){
  ctx.beginPath();
  ctx.moveTo(left,y);
  ctx.lineTo(right,y);
  ctx.stroke();
 }

 ctx.strokeStyle=m.edge;
 ctx.lineWidth=9;
 ctx.strokeRect(0,0,WORLD.w,WORLD.h);
}

function drawWorld(){
 const z=camera.zoom||1;

 ctx.save();
 ctx.translate(-camera.x*z,-camera.y*z);
 ctx.scale(z,z);

 drawGrid();

 for(const f of food)
  circle(f.x,f.y,f.r,f.color,4);

 for(const q of pellets)
  circle(q.x,q.y,q.r,'#fff18a',5);

 for(const v of viruses){
  ctx.save();
  ctx.translate(v.x,v.y);

  ctx.fillStyle='#48ff78';
  ctx.shadowColor='#25ff69';
  ctx.shadowBlur=16;
  ctx.beginPath();

  for(let i=0;i<40;i++){
   const a=i*Math.PI*2/40;
   const r=i%2===0?v.r:v.r*.78;
   const x=Math.cos(a)*r;
   const y=Math.sin(a)*r;

   if(i===0)ctx.moveTo(x,y);
   else ctx.lineTo(x,y);
  }

  ctx.closePath();
  ctx.fill();
  ctx.shadowBlur=0;
  ctx.fillStyle='#073b20';
  ctx.font='bold 11px Arial';
  ctx.textAlign='center';
  ctx.fillText('VIRUS',0,4);
  ctx.restore();
 }

 for(const b of bots){
  circle(b.x,b.y,b.r,b.color,10);

  ctx.fillStyle='#fff';
  ctx.font='bold 10px Arial';
  ctx.textAlign='center';
  ctx.textBaseline='middle';
  ctx.fillText('BOT '+(b.id+1),b.x,b.y);
 }

 const t=now();

 for(const p of pieces){
  if(t<shieldUntil||t<(p.virusSafeUntil||0)){
   ctx.strokeStyle='#fff';
   ctx.lineWidth=3;
   ctx.beginPath();
   ctx.arc(p.x,p.y,p.r+7,0,Math.PI*2);
   ctx.stroke();
  }

  circle(p.x,p.y,p.r,p.color,18);

  ctx.fillStyle='#fff';
  ctx.font=`bold ${Math.max(9,Math.min(18,p.r*.42))}px Arial`;
  ctx.textAlign='center';
  ctx.textBaseline='middle';
  ctx.fillText('ORBIX',p.x,p.y);
 }

 ctx.restore();
}

function drawMini(){
 const w=mini.clientWidth,h=mini.clientHeight;
 if(!w||!h)return;

 mctx.clearRect(0,0,w,h);
 mctx.fillStyle='#07152c';
 mctx.fillRect(0,0,w,h);

 const sx=w/WORLD.w,sy=h/WORLD.h;

 mctx.fillStyle='#27d9ff';

 for(const p of pieces){
  mctx.beginPath();
  mctx.arc(p.x*sx,p.y*sy,3,0,Math.PI*2);
  mctx.fill();
 }

 mctx.fillStyle='#ff6095';

 for(const b of bots){
  mctx.beginPath();
  mctx.arc(b.x*sx,b.y*sy,2,0,Math.PI*2);
  mctx.fill();
 }

 mctx.fillStyle='#7dff8a';

 for(const v of viruses){
  mctx.beginPath();
  mctx.arc(v.x*sx,v.y*sy,2,0,Math.PI*2);
  mctx.fill();
 }

 const z=camera.zoom||1;
 mctx.strokeStyle='#fff';
 mctx.lineWidth=1;
 mctx.strokeRect(camera.x*sx,camera.y*sy,W/z*sx,H/z*sy);
}

function drawLeaderboard(){
 const rows=[
  {name:'ORBIX',r:totalMass(),you:true},
  ...bots.map(b=>({name:'BOT '+(b.id+1),r:mass(b)}))
 ].sort((a,b)=>b.r-a.r).slice(0,5);

 $('leaderRows').innerHTML=rows.map((e,i)=>
  `<div class="leadrow"><span>${i+1}. ${e.you?'🔷 ':''}${e.name}</span><b>${Math.round(e.r)}</b></div>`
 ).join('');
}

function render(){
 ctx.setTransform(DPR,0,0,DPR,0,0);
 ctx.clearRect(0,0,W,H);

 if(pieces.length)drawWorld();
 else{
  ctx.fillStyle=maps[mapName].bg;
  ctx.fillRect(0,0,W,H);
 }

 drawMini();
}

/* حلقة اللعبة */

function loop(t){
 if(!running||pausedByVisibility)return;

 const dt=Math.min(40,lastTime?Math.max(0,t-lastTime):16.666);
 lastTime=t;

 // تحديث ثابت لتقليل اختلاف الحركة بين الأجهزة
 accumulator+=dt;
 const fixedStep=16.666;

 let steps=0;
 while(accumulator>=fixedStep&&steps<3){
  update(fixedStep);
  accumulator-=fixedStep;
  steps++;
 }

 if(steps>=3)accumulator=0;

 render();

 if(running&&!pausedByVisibility)
  requestAnimationFrame(loop);
}

/* الأزرار */

$('startBtn').addEventListener('click',startGame);
$('restartBtn').addEventListener('click',startGame);
$('menuBtn').addEventListener('click',showMenu);

$('fullBtn').addEventListener('click',async()=>{
 try{
  if(!document.fullscreenElement&&document.documentElement.requestFullscreen)
   await document.documentElement.requestFullscreen();

  try{
   if(screen.orientation?.lock)
    await screen.orientation.lock('landscape');
  }catch(e){}

  toast('تم طلب ملء الشاشة والوضع الأفقي');
 }catch(e){
  toast('قد لا يدعم المتصفح ملء الشاشة');
 }
});

resetWorld();
resize();
render();

})();
</script>
</body>
</html>



