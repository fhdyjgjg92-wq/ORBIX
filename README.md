
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<meta name="theme-color" content="#050b18">
<title>ORBIX 2.1</title>
<style>
*{box-sizing:border-box}
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#050b18;color:white;font-family:Arial,sans-serif;touch-action:none}
button{font:inherit;color:white;cursor:pointer}
#app{position:fixed;inset:0;overflow:hidden}
#game{position:absolute;inset:0;width:100%;height:100%}
#topbar{position:absolute;top:0;left:0;right:0;height:52px;display:flex;justify-content:space-between;align-items:center;padding:6px 13px;background:linear-gradient(#030916ed,transparent);z-index:4;pointer-events:none}
.logo{font-size:25px;font-weight:900;letter-spacing:4px;color:#27d9ff;text-shadow:0 0 18px #168bff}
#stats{text-align:left;font-size:12px;line-height:1.7}
#stats b{color:#27d9ff}
.panel{background:#07172be8;border:1px solid #27d9ff55;border-radius:12px;backdrop-filter:blur(8px)}
#leader{position:absolute;top:58px;right:9px;width:145px;padding:9px;z-index:4;pointer-events:none;font-size:11px}
#leader h3{color:#27d9ff;margin:0 0 6px;font-size:13px}
.leadrow{display:flex;justify-content:space-between;padding:3px 0}
#mapBox{position:absolute;right:9px;bottom:10px;width:125px;height:88px;padding:4px;z-index:4;pointer-events:none}
#mini{width:100%;height:100%;display:block}
#joystick{position:absolute;left:14px;bottom:15px;width:100px;height:100px;border-radius:50%;border:2px solid #67caff88;background:#3b91ff20;display:flex;align-items:center;justify-content:center;z-index:6;touch-action:none}
#stick{width:38px;height:38px;border-radius:50%;background:#27d9ff;box-shadow:0 0 18px #27d9ff;pointer-events:none}
#actions{position:absolute;left:128px;bottom:22px;display:flex;gap:9px;z-index:6;direction:ltr}
.action{width:61px;height:61px;border-radius:50%;border:2px solid #56cfff99;background:linear-gradient(145deg,#123d70,#07162d);font-size:11px;font-weight:bold}
.action:active{transform:scale(.94)}
#tools{position:absolute;top:59px;left:9px;display:flex;flex-direction:column;gap:7px;z-index:6}
.tool{padding:8px 10px;border:1px solid #27d9ff55;border-radius:9px;background:#07172be8;font-size:11px}
#notice{position:absolute;top:58px;left:50%;transform:translateX(-50%);z-index:10;padding:8px 13px;border:1px solid #27d9ff66;border-radius:20px;background:#07172bee;font-size:12px;display:none;white-space:nowrap;pointer-events:none}
#menu{position:absolute;inset:0;z-index:20;display:flex;align-items:center;justify-content:center;padding:12px;background:radial-gradient(ellipse,#0c2852ed,#030916f8)}
#menuCard{width:min(420px,94vw);max-height:93vh;overflow:auto;text-align:center;padding:20px;border:1px solid #27d9ff77;border-radius:20px;background:linear-gradient(145deg,#0b1d38,#050b18);box-shadow:0 0 40px #087dff33}
#menu h1{font-size:40px;letter-spacing:7px;color:#27d9ff;margin:0;text-shadow:0 0 22px #168bff}
.sub{font-size:11px;color:#a9c9e8;margin:5px 0 17px}
.menuBtn{width:100%;padding:11px;margin:5px 0;border-radius:10px;border:1px solid #2c9dff88;background:#0d2b50}
.menuBtn.primary{background:linear-gradient(90deg,#0877d8,#08b9d8);font-size:17px;font-weight:bold}
.menuBtn:disabled{opacity:.45}
#mapChoices{display:flex;gap:7px;margin:9px 0}
.mapChoice{flex:1;padding:9px 3px;border-radius:9px;border:1px solid #315a80;background:#10233d;font-size:11px}
.mapChoice.active{border-color:#27d9ff;box-shadow:0 0 12px #0aaaff66}
#rotate{display:none;position:fixed;inset:0;z-index:100;background:#050b18;align-items:center;justify-content:center;text-align:center;font-size:20px;padding:20px}
@media(orientation:portrait) and (max-width:900px){#rotate{display:flex}}
@media(max-height:430px){
 #topbar{height:40px}#leader{top:45px;width:120px}
 #joystick{width:82px;height:82px;left:9px;bottom:8px}
 #stick{width:32px;height:32px}#actions{left:100px;bottom:12px}
 .action{width:51px;height:51px}#mapBox{width:105px;height:72px}
 #tools{top:44px}
}
</style>
</head>
<body>
<div id="app">
<canvas id="game"></canvas>
<div id="topbar">
 <div class="logo">ORBIX</div>
 <div id="stats">الكتلة: <b id="mass">400</b><br>النقاط: <b id="score">0</b> · الأفضل: <b id="best">0</b></div>
</div>
<div id="leader" class="panel"><h3>🏆 المتصدرون</h3><div id="leaderRows"></div></div>
<div id="tools">
 <button class="tool" id="fullBtn">⛶ ملء الشاشة</button>
 <button class="tool" id="menuBtn">☰ القائمة</button>
</div>
<div id="mapBox" class="panel"><canvas id="mini"></canvas></div>
<div id="joystick"><div id="stick"></div></div>
<div id="actions">
 <button class="action" id="ejectBtn">إطلاق<br>●</button>
 <button class="action" id="splitBtn">تقسيم<br>Ⅱ</button>
</div>
<div id="notice"></div>
<div id="menu">
 <div id="menuCard">
  <h1>ORBIX</h1>
  <div class="sub">VERSION 2.1 · ARENA</div>
  <div style="font-size:13px">اختر ساحة المعركة</div>
  <div id="mapChoices">
   <button class="mapChoice active" data-map="neon">🌌 المجرة</button>
   <button class="mapChoice" data-map="ice">❄️ الجليد</button>
   <button class="mapChoice" data-map="toxic">☣️ السامة</button>
  </div>
  <button class="menuBtn primary" id="startBtn">▶ ابدأ اللعبة</button>
  <button class="menuBtn" id="restartBtn">↻ إعادة الجولة</button>
  <button class="menuBtn" disabled>👥 اللعب الجماعي — قيد التطوير</button>
  <button class="menuBtn" disabled>🛒 المتجر — قيد التطوير</button>
  <div style="font-size:10px;color:#7996b7;line-height:1.8;margin-top:8px">
   نسخة أوفلاين تجريبية. وجّه اللاعب بالعصا، اجمع الغذاء، تجنب الفيروسات، وتنافس مع الخصوم الآليين.
  </div>
 </div>
</div>
</div>
<div id="rotate">📱<br><br>أدر هاتفك إلى الوضع الأفقي للعب ORBIX</div>

<script>
"use strict";

/* أساس اللعبة */
const $=id=>document.getElementById(id);
const canvas=$("game"),ctx=canvas.getContext("2d");
const mini=$("mini"),mctx=mini.getContext("2d");
const WORLD={w:4400,h:3200};
const MAX_PIECES=16,MAX_VIRUSES=32;
const FOOD_TARGET=650,BOT_COUNT=18;
const MERGE_DELAY=12000,SHIELD_TIME=4000;
const maps={
 neon:{bg:"#07152c",grid:"rgba(48,140,255,.13)",edge:"#27d9ff",food:["#27d9ff","#ff5ca8","#a6ff65","#b78cff"]},
 ice:{bg:"#10253b",grid:"rgba(180,230,255,.13)",edge:"#a8edff",food:["#b5f3ff","#62d6ff","#e1ffff","#9ebcff"]},
 toxic:{bg:"#101d1a",grid:"rgba(100,255,130,.13)",edge:"#7dff8a",food:["#8dff70","#d3ff56","#45e6a0","#d0ffae"]}
};
let mapName=localStorage.getItem("orbixMap")||"neon";
if(!maps[mapName])mapName="neon";
let W=innerWidth,H=innerHeight,DPR=1;
let food=[],viruses=[],bots=[],pieces=[],pellets=[],pending=[];
let camera={x:0,y:0,zoom:1};
let running=false,score=0,best=Number(localStorage.getItem("orbixBest")||0);
let shieldUntil=0,lastTime=0,noticeTimer;
let input={x:0,y:0},keys={},pointerId=null;
let direction={x:1,y:0};

const rand=(a,b)=>a+Math.random()*(b-a);
const clamp=(v,a,b)=>Math.max(a,Math.min(b,v));
const dist=(a,b)=>Math.hypot(a.x-b.x,a.y-b.y);
const mass=p=>p.r*p.r;
const totalMass=()=>pieces.reduce((s,p)=>s+mass(p),0);

function resize(){
 DPR=Math.min(devicePixelRatio||1,2);
 W=innerWidth;H=innerHeight;
 canvas.width=W*DPR;canvas.height=H*DPR;
 canvas.style.width=W+"px";canvas.style.height=H+"px";
 ctx.setTransform(DPR,0,0,DPR,0,0);
 mini.width=Math.max(1,mini.clientWidth*DPR);
 mini.height=Math.max(1,mini.clientHeight*DPR);
 mctx.setTransform(DPR,0,0,DPR,0,0);
}
addEventListener("resize",resize);resize();

function toast(s){
 const el=$("notice");el.textContent=s;el.style.display="block";
 clearTimeout(noticeTimer);
 noticeTimer=setTimeout(()=>el.style.display="none",1900);
}
function newPlayer(){
 return {x:WORLD.w/2,y:WORLD.h/2,r:20,vx:0,vy:0,color:"#27d9ff",
 mergeAt:0,virusSafeUntil:0};
}
function randomFood(){
 const c=maps[mapName].food;
 return {x:rand(30,WORLD.w-30),y:rand(30,WORLD.h-30),r:rand(4,6),color:c[Math.floor(Math.random()*c.length)]};
}
function addVirus(x,y,r=43){
 if(viruses.length>=MAX_VIRUSES)return false;
 viruses.push({x,y,r,charge:0});
 return true;
}
function newBot(id){
 return {id,x:rand(100,WORLD.w-100),y:rand(100,WORLD.h-100),
 r:rand(20,34),color:["#ff4d8d","#a87aff","#ffb84d","#6dffb0","#ff765b"][id%5],
 tx:rand(100,WORLD.w-100),ty:rand(100,WORLD.h-100),think:0};
}
function resetWorld(){
 food=[];viruses=[];bots=[];pieces=[];pellets=[];pending=[];
 score=0;pieces=[newPlayer()];
 shieldUntil=performance.now()+SHIELD_TIME;
 for(let i=0;i<FOOD_TARGET;i++)food.push(randomFood());
 for(let i=0;i<24;i++)addVirus(rand(100,WORLD.w-100),rand(100,WORLD.h-100));
 for(let i=0;i<BOT_COUNT;i++)bots.push(newBot(i));
 camera={x:0,y:0,zoom:1};
 updateStats();
}

/* اختيار الخريطة */
function chooseMap(name){
 if(!maps[name])return;
 mapName=name;localStorage.setItem("orbixMap",name);
 document.querySelectorAll(".mapChoice").forEach(b=>b.classList.toggle("active",b.dataset.map===name));
}
document.querySelectorAll(".mapChoice").forEach(b=>b.addEventListener("click",()=>chooseMap(b.dataset.map)));
chooseMap(mapName);

/* تحكم الهاتف */
function joystickMove(e){
 const r=$("joystick").getBoundingClientRect();
 let dx=e.clientX-(r.left+r.width/2),dy=e.clientY-(r.top+r.height/2);
 const max=r.width*.34,len=Math.hypot(dx,dy);
 if(len>max){dx=dx/len*max;dy=dy/len*max}
 $("stick").style.transform=`translate(${dx}px,${dy}px)`;
 input={x:dx/max,y:dy/max};
 const l=Math.hypot(input.x,input.y);
 if(l>.1)direction={x:input.x/l,y:input.y/l};
}
function stopJoy(e){
 if(e&&pointerId!==null&&e.pointerId!==pointerId)return;
 pointerId=null;input={x:0,y:0};$("stick").style.transform="translate(0,0)";
}
$("joystick").addEventListener("pointerdown",e=>{
 pointerId=e.pointerId;$("joystick").setPointerCapture(pointerId);joystickMove(e);
});
$("joystick").addEventListener("pointermove",e=>{if(e.pointerId===pointerId)joystickMove(e)});
$("joystick").addEventListener("pointerup",stopJoy);
$("joystick").addEventListener("pointercancel",stopJoy);
function clearControls(){keys={};stopJoy()}
addEventListener("blur",clearControls);
document.addEventListener("visibilitychange",()=>{if(document.hidden)clearControls()});
addEventListener("keydown",e=>{
 keys[e.key.toLowerCase()]=true;
 if(["arrowup","arrowdown","arrowleft","arrowright"," "].includes(e.key.toLowerCase()))e.preventDefault();
});
addEventListener("keyup",e=>keys[e.key.toLowerCase()]=false);

function getDirection(){
 let x=input.x,y=input.y;
 if(keys.a||keys.arrowleft)x--;
 if(keys.d||keys.arrowright)x++;
 if(keys.w||keys.arrowup)y--;
 if(keys.s||keys.arrowdown)y++;
 const l=Math.hypot(x,y);
 if(l>1){x/=l;y/=l}
 if(Math.hypot(x,y)>.05){
  const n=Math.hypot(x,y);direction={x:x/n,y:y/n};
 }
 return {x,y};
}

/* حركة اللاعب والقطع */
function movePlayer(dt){
 const d=getDirection();
 for(const p of pieces){
  const speed=Math.max(1.4,4.3-p.r*.025);
  p.vx+=(d.x*speed-p.vx)*Math.min(1,dt*.012);
  p.vy+=(d.y*speed-p.vy)*Math.min(1,dt*.012);
  p.x=clamp(p.x+p.vx*dt*.06,p.r,WORLD.w-p.r);
  p.y=clamp(p.y+p.vy*dt*.06,p.r,WORLD.h-p.r);
 }
 // منع تداخل القطع مع بعضها
 for(let i=0;i<pieces.length;i++){
  for(let j=i+1;j<pieces.length;j++){
   const a=pieces[i],b=pieces[j],dx=b.x-a.x,dy=b.y-a.y,d=Math.hypot(dx,dy)||.001;
   const min=(a.r+b.r)*.72;
   if(d<min){
    const push=(min-d)*.04,nx=dx/d,ny=dy/d;
    a.x-=nx*push;a.y-=ny*push;b.x+=nx*push;b.y+=ny*push;
   }
  }
 }
}

/* التقسيم اليدوي */
function splitPlayer(){
 if(!running)return;
 if(pieces.length>=MAX_PIECES){toast("وصلت للحد الأقصى من القطع");return}
 const additions=[],now=performance.now();
 for(const p of [...pieces]){
  if(p.r<25||pieces.length+additions.length>=MAX_PIECES)continue;
  const r=p.r/Math.sqrt(2);
  p.r=r;p.mergeAt=now+MERGE_DELAY;
  additions.push({
   x:clamp(p.x+direction.x*r*1.5,r,WORLD.w-r),
   y:clamp(p.y+direction.y*r*1.5,r,WORLD.h-r),
   r,color:p.color,vx:direction.x*12,vy:direction.y*12,
   mergeAt:now+MERGE_DELAY,virusSafeUntil:now+1300
  });
 }
 pieces.push(...additions);
 if(additions.length)toast("تم تقسيم ORBIX");
}

/* إطلاق الحبيبات */
function ejectMass(){
 if(!running)return;
 let count=0;
 for(const p of pieces){
  if(p.r<23)continue;
  const oldMass=mass(p),cost=65;
  if(oldMass-cost<100)continue;
  p.r=Math.sqrt(oldMass-cost);
  pellets.push({
   x:p.x+direction.x*(p.r+12),y:p.y+direction.y*(p.r+12),
   vx:direction.x*9,vy:direction.y*9,r:6,life:9000
  });
  count++;
 }
 if(count)toast("تم إطلاق حبيبات الدعم");
}
$("splitBtn").addEventListener("click",splitPlayer);
$("ejectBtn").addEventListener("click",ejectMass);

/* الطعام */
function eatFood(){
 for(let i=food.length-1;i>=0;i--){
  const f=food[i];let eaten=false;
  for(const p of pieces){
   if(dist(p,f)<p.r+f.r){
    p.r=Math.sqrt(mass(p)+f.r*f.r);
    score++;eaten=true;break;
   }
  }
  if(eaten)food.splice(i,1);
 }
 while(food.length<FOOD_TARGET)food.push(randomFood());
}

/* الفيروس: انفجار بكتلة محفوظة وحماية مؤقتة */
function virusHit(p){
 const count=Math.min(8,Math.max(4,Math.ceil(p.r/22)));
 if(pieces.length+pending.length+count-1>MAX_PIECES){
  toast("عدد القطع ممتلئ، ابتعد عن الفيروس");
  p.virusSafeUntil=performance.now()+900;
  return false;
 }
 const now=performance.now(),r=p.r/Math.sqrt(count);
 const angle0=rand(0,Math.PI*2);
 const result=[];
 for(let i=0;i<count;i++){
  const a=angle0+i*Math.PI*2/count;
  const speed=rand(5,8);
  result.push({
   x:clamp(p.x+Math.cos(a)*(p.r+r)*.65,r,WORLD.w-r),
   y:clamp(p.y+Math.sin(a)*(p.r+r)*.65,r,WORLD.h-r),
   r,color:p.color,vx:Math.cos(a)*speed,vy:Math.sin(a)*speed,
   mergeAt:now+MERGE_DELAY,virusSafeUntil:now+1800
  });
 }
 pending.push(...result);
 return true;
}
function checkViruses(){
 if(performance.now()<shieldUntil)return;
 for(const p of [...pieces]){
  if(performance.now()<(p.virusSafeUntil||0))continue;
  for(const v of viruses){
   if(dist(p,v)<p.r+v.r*.72&&p.r>v.r*1.12){
    if(virusHit(p)){
     pieces=pieces.filter(q=>q!==p);
     break;
    }
   }
  }
 }
 if(pending.length){
  pieces.push(...pending);pending=[];
 }
}

/* البوتات: بحث عن الطعام وهروب وتنافس */
function updateBots(dt){
 for(const b of bots){
  b.think-=dt;
  if(b.think<=0){
   b.think=rand(350,850);
   let target=null,bestD=Infinity;
   for(const f of food){
    const d=dist(b,f);
    if(d<bestD){bestD=d;target=f}
   }
   if(target){b.tx=target.x;b.ty=target.y}
   // الهروب من الفيروس
   for(const v of viruses){
    if(dist(b,v)<190){
     b.tx=clamp(b.x+(b.x-v.x)*2,40,WORLD.w-40);
     b.ty=clamp(b.y+(b.y-v.y)*2,40,WORLD.h-40);
    }
   }
   // الهروب من اللاعب والخصوم الأكبر
   const threats=[...pieces,...bots.filter(q=>q!==b)];
   for(const t of threats){
    if(t.r>b.r*1.18&&dist(b,t)<230){
     b.tx=clamp(b.x+(b.x-t.x)*2,40,WORLD.w-40);
     b.ty=clamp(b.y+(b.y-t.y)*2,40,WORLD.h-40);
    }
   }
  }
  const dx=b.tx-b.x,dy=b.ty-b.y,l=Math.hypot(dx,dy)||1;
  const speed=Math.max(1.5,3.5-b.r*.018);
  b.x=clamp(b.x+dx/l*speed*dt*.06,b.r,WORLD.w-b.r);
  b.y=clamp(b.y+dy/l*speed*dt*.06,b.r,WORLD.h-b.r);
 }
 // البوتات تأكل الطعام
 for(const b of bots){
  for(let i=food.length-1;i>=0;i--){
   if(dist(b,food[i])<b.r+food[i].r){
    b.r=Math.sqrt(mass(b)+food[i].r*food[i].r);
    food.splice(i,1);
   }
  }
 }
 // البوتات تتنافس فيما بينها
 for(let i=0;i<bots.length;i++){
  for(let j=i+1;j<bots.length;j++){
   const a=bots[i],b=bots[j];
   if(dist(a,b)<Math.max(a.r,b.r)*.78){
    if(a.r>b.r*1.12){
     a.r=Math.sqrt(mass(a)+mass(b)*.7);bots[j]=newBot(b.id);
    }else if(b.r>a.r*1.12){
     b.r=Math.sqrt(mass(b)+mass(a)*.7);bots[i]=newBot(a.id);
    }
   }
  }
 }
}

/* مواجهة اللاعب مع البوتات */
function checkPlayerCollisions(){
 if(performance.now()<shieldUntil)return;
 for(let i=bots.length-1;i>=0;i--){
  const b=bots[i];
  for(let j=pieces.length-1;j>=0;j--){
   const p=pieces[j];
   if(dist(p,b)>=Math.max(p.r,b.r)*.78)continue;
   if(p.r>b.r*1.12){
    p.r=Math.sqrt(mass(p)+mass(b)*.7);
    score+=10;bots[i]=newBot(b.id);break;
   }
   if(b.r>p.r*1.12){
    pieces.splice(j,1);
    if(!pieces.length){respawn();return}
   }
  }
 }
}
function respawn(){
 pieces=[newPlayer()];
 shieldUntil=performance.now()+SHIELD_TIME;
 toast("عدت إلى الساحة — حماية مؤقتة");
}

/* اندماج صحيح باستخدام الكتلتين قبل التغيير */
function mergePieces(){
 const now=performance.now();
 for(let i=0;i<pieces.length;i++){
  for(let j=pieces.length-1;j>i;j--){
   const a=pieces[i],b=pieces[j];
   if(now<a.mergeAt||now<b.mergeAt)continue;
   if(dist(a,b)<(a.r+b.r)*.55){
    const ma=mass(a),mb=mass(b),sum=ma+mb;
    a.x=(a.x*ma+b.x*mb)/sum;
    a.y=(a.y*ma+b.y*mb)/sum;
    a.r=Math.sqrt(sum);
    a.vx=(a.vx*ma+b.vx*mb)/sum;
    a.vy=(a.vy*ma+b.vy*mb)/sum;
    a.mergeAt=0;
    pieces.splice(j,1);
   }
  }
 }
}

/* المقذوفات التي تطعم الفيروس */
function updatePellets(dt){
 for(let i=pellets.length-1;i>=0;i--){
  const q=pellets[i];
  q.x+=q.vx*dt*.06;q.y+=q.vy*dt*.06;
  q.vx*=.985;q.vy*=.985;q.life-=dt;
  if(q.life<=0||q.x<0||q.y<0||q.x>WORLD.w||q.y>WORLD.h){
   pellets.splice(i,1);continue;
  }
  for(const v of viruses){
   if(dist(q,v)<q.r+v.r){
    v.charge++;pellets.splice(i,1);
    if(v.charge>=7){
     v.charge=0;
     addVirus(clamp(v.x+rand(-100,100),60,WORLD.w-60),
              clamp(v.y+rand(-100,100),60,WORLD.h-60));
    }
    break;
   }
  }
 }
}

/* الكاميرا */
function updateCamera(){
 if(!pieces.length)return;
 const total=totalMass()||1;
 const x=pieces.reduce((s,p)=>s+p.x*mass(p),0)/total;
 const y=pieces.reduce((s,p)=>s+p.y*mass(p),0)/total;
 camera.zoom=clamp(1.12-Math.sqrt(total)*.0025,.52,1.08);
 camera.x=clamp(x-W/(2*camera.zoom),0,WORLD.w-W/camera.zoom);
 camera.y=clamp(y-H/(2*camera.zoom),0,WORLD.h-H/camera.zoom);
}

/* تحديث اللعبة */
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
 ctx.fillStyle=color;ctx.shadowColor=color;ctx.shadowBlur=glow;
 ctx.beginPath();ctx.arc(x,y,r,0,Math.PI*2);ctx.fill();ctx.shadowBlur=0;
}
function drawGrid(){
 const style=maps[mapName];
 ctx.fillStyle=style.bg;ctx.fillRect(0,0,WORLD.w,WORLD.h);
 ctx.strokeStyle=style.grid;ctx.lineWidth=1;
 for(let x=0;x<=WORLD.w;x+=70){ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,WORLD.h);ctx.stroke()}
 for(let y=0;y<=WORLD.h;y+=70){ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(WORLD.w,y);ctx.stroke()}
 ctx.strokeStyle=style.edge;ctx.lineWidth=9;ctx.strokeRect(0,0,WORLD.w,WORLD.h);
}
function drawWorld(){
 const z=camera.zoom||1;
 ctx.save();ctx.translate(-camera.x*z,-camera.y*z);ctx.scale(z,z);
 drawGrid();
 for(const f of food)circle(f.x,f.y,f.r,f.color,6);
 for(const q of pellets)circle(q.x,q.y,q.r,"#fff18a",7);
 for(const v of viruses){
  ctx.save();ctx.translate(v.x,v.y);
  ctx.fillStyle="#48ff78";ctx.shadowColor="#25ff69";ctx.shadowBlur=18;
  ctx.beginPath();
  for(let i=0;i<40;i++){
   const a=i*Math.PI*2/40,r=i%2===0?v.r:v.r*.78;
   const x=Math.cos(a)*r,y=Math.sin(a)*r;
   if(i===0)ctx.moveTo(x,y);else ctx.lineTo(x,y);
  }
  ctx.closePath();ctx.fill();ctx.shadowBlur=0;
  ctx.fillStyle="#073b20";ctx.font="bold 12px Arial";ctx.textAlign="center";
  ctx.fillText("VIRUS",0,4);ctx.restore();
 }
 for(const b of bots){
  circle(b.x,b.y,b.r,b.color,13);
  ctx.fillStyle="white";ctx.font="bold 10px Arial";ctx.textAlign="center";
  ctx.fillText("BOT",b.x,b.y+3);
 }
 const protectedNow=performance.now()<shieldUntil;
 for(const p of pieces){
  if(protectedNow||performance.now()<(p.virusSafeUntil||0)){
   ctx.strokeStyle="#fff";ctx.lineWidth=3;
   ctx.beginPath();ctx.arc(p.x,p.y,p.r+7,0,Math.PI*2);ctx.stroke();
  }
  circle(p.x,p.y,p.r,p.color,22);
  ctx.fillStyle="white";ctx.font=`bold ${Math.max(9,Math.min(18,p.r*.45))}px Arial`;
  ctx.textAlign="center";ctx.textBaseline="middle";ctx.fillText("ORBIX",p.x,p.y);
 }
 ctx.restore();
}
function drawMini(){
 const w=mini.clientWidth,h=mini.clientHeight;
 mctx.clearRect(0,0,w,h);
 mctx.fillStyle="#07152c";mctx.fillRect(0,0,w,h);
 const sx=w/WORLD.w,sy=h/WORLD.h;
 mctx.fillStyle="#27d9ff";
 for(const p of pieces){mctx.beginPath();mctx.arc(p.x*sx,p.y*sy,3,0,7);mctx.fill()}
 mctx.fillStyle="#ff6095";
 for(const b of bots){mctx.beginPath();mctx.arc(b.x*sx,b.y*sy,2,0,7);mctx.fill()}
 mctx.fillStyle="#7dff8a";
 for(const v of viruses){mctx.beginPath();mctx.arc(v.x*sx,v.y*sy,2,0,7);mctx.fill()}
 const z=camera.zoom||1;
 mctx.strokeStyle="white";mctx.lineWidth=1;
 mctx.strokeRect(camera.x*sx,camera.y*sy,W/z*sx,H/z*sy);
}
function drawLeaderboard(){
 const rows=[{name:"ORBIX",r:totalMass(),you:true},
  ...bots.map(b=>({name:"BOT "+(b.id+1),r:mass(b)}))]
  .sort((a,b)=>b.r-a.r).slice(0,5);
 $("leaderRows").innerHTML=rows.map((e,i)=>
  `<div class="leadrow"><span>${i+1}. ${e.you?"🔷 ":""}${e.name}</span><b>${Math.round(e.r)}</b></div>`).join("");
}
function updateStats(){
 $("mass").textContent=Math.round(totalMass());
 $("score").textContent=score;
 if(score>best){best=score;localStorage.setItem("orbixBest",String(best))}
 $("best").textContent=best;
 drawLeaderboard();
}
function render(){
 ctx.clearRect(0,0,W,H);
 drawWorld();drawMini();
}

/* حلقة التشغيل */
function loop(t){
 const dt=Math.min(40,t-lastTime||16);lastTime=t;
 update(dt);render();requestAnimationFrame(loop);
}

/* القائمة وملء الشاشة */
function startGame(){
 resetWorld();running=true;$("menu").style.display="none";
 toast("مرحباً بك في ساحة ORBIX");
}
function showMenu(){
 running=false;clearControls();$("menu").style.display="flex";
}
$("startBtn").addEventListener("click",startGame);
$("restartBtn").addEventListener("click",startGame);
$("menuBtn").addEventListener("click",showMenu);

$("fullBtn").addEventListener("click",async()=>{
 try{
  if(!document.fullscreenElement&&document.documentElement.requestFullscreen)
   await document.documentElement.requestFullscreen();
  if(screen.orientation?.lock){
   try{await screen.orientation.lock("landscape")}catch(e){}
  }
  toast("تم طلب وضع ملء الشاشة");
 }catch(e){toast("ملء الشاشة غير مدعوم في هذا المتصفح")}
});

/* بدء */
resetWorld();
requestAnimationFrame(loop);
</script>
</body>
</html>


