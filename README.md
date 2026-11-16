

<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no,viewport-fit=cover">
<meta name="theme-color" content="#050b18">
<title>ORBIX — Arena</title>
<style>
:root{--cyan:#27d9ff;--blue:#176dff;--bg:#050b18;--panel:rgba(7,17,39,.88);--gold:#ffd34e}
*{box-sizing:border-box}
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:var(--bg);color:#fff;font-family:Arial,sans-serif;touch-action:none}
button{font:inherit;color:#fff;border:1px solid rgba(75,190,255,.55);background:linear-gradient(145deg,#123c79,#07162f);border-radius:12px;padding:10px 13px;cursor:pointer;box-shadow:0 0 14px #087cff33}
button:active{transform:scale(.97)}
#app{position:fixed;inset:0;background:radial-gradient(ellipse at center,#102b58 0,#071329 48%,#030711 100%)}
#topbar{position:absolute;z-index:5;top:0;left:0;right:0;height:54px;display:flex;align-items:center;justify-content:space-between;padding:5px 12px;background:linear-gradient(#030817ef,#071329aa);border-bottom:1px solid #278cff55}
.logo{font-weight:1000;letter-spacing:2px;color:#56e5ff;font-size:clamp(20px,3vw,29px);text-shadow:0 0 14px #00aaff}
#stats{font-size:12px;color:#d9efff;line-height:1.5;text-align:left}
#game{position:absolute;inset:54px 0 0;width:100%;height:calc(100% - 54px);display:block;touch-action:none}
#menu{position:absolute;z-index:8;inset:54px 0 0;display:flex;align-items:center;justify-content:center;background:linear-gradient(120deg,#030916d9,#071b36c9);padding:14px}
.menuBox{width:min(900px,96vw);max-height:90%;overflow:auto;text-align:center;border:1px solid #258dff88;border-radius:22px;padding:clamp(14px,3vw,28px);background:linear-gradient(145deg,#081a37ee,#030a18f2);box-shadow:0 0 45px #007bff33}
.menuBox h1{font-size:clamp(38px,8vw,76px);margin:0;color:#e9faff;text-shadow:0 0 22px #009dff}
.tag{color:#9fcfff;margin:4px 0 16px;font-size:13px;letter-spacing:2px}
.menuActions{display:flex;gap:10px;justify-content:center;flex-wrap:wrap;margin:14px auto}
.primary{background:linear-gradient(135deg,#ffe66c,#ff9d16);color:#15101a;border:2px solid #fff2a5;font-weight:900;font-size:clamp(16px,2.5vw,22px);padding:12px 32px;min-width:170px}
.mode{min-width:145px}
#mapChoices{display:flex;gap:8px;justify-content:center;flex-wrap:wrap;margin:12px 0}
.mapChoice{min-width:105px}
.mapChoice.selected{outline:2px solid var(--gold);box-shadow:0 0 18px #ffd34e66}
.smallLinks{display:flex;justify-content:center;gap:8px;flex-wrap:wrap;margin-top:12px}
#hud{position:absolute;z-index:4;inset:60px 10px 10px;pointer-events:none;display:none}
#leader{position:absolute;top:6px;right:6px;width:145px;padding:9px;background:var(--panel);border:1px solid #238bff66;border-radius:12px;font-size:11px;line-height:1.7}
#leader b{color:var(--gold)}
#mini{position:absolute;left:8px;top:6px;width:112px;height:82px;border:1px solid #35caff99;border-radius:9px;background:#06152bd9}
#joystick{position:absolute;pointer-events:auto;bottom:12px;left:12px;width:94px;height:94px;border-radius:50%;border:2px solid #8bdcff99;background:#65cfff22;display:flex;align-items:center;justify-content:center;touch-action:none}
#stick{width:36px;height:36px;border-radius:50%;background:#31dfff;box-shadow:0 0 15px #00bfff;pointer-events:none}
#actions{position:absolute;pointer-events:auto;bottom:15px;right:12px;display:flex;gap:8px;align-items:end}
.actionBtn{width:62px;height:62px;border-radius:50%;padding:4px;font-weight:bold;font-size:11px;background:radial-gradient(circle,#145aa5,#06152d);border:2px solid #4bcfff}
#back{position:absolute;pointer-events:auto;top:98px;left:10px;padding:7px 10px;font-size:12px}
#toast{position:absolute;z-index:12;left:50%;top:62px;transform:translateX(-50%);background:#071a34ee;border:1px solid #37caff;border-radius:10px;padding:8px 14px;font-size:12px;display:none;white-space:nowrap}
#rotate{display:none;position:absolute;z-index:20;inset:0;background:#050b18;align-items:center;justify-content:center;text-align:center;padding:30px;font-size:20px}
@media(orientation:portrait) and (max-width:700px){#rotate{display:flex}#app{visibility:hidden}}
@media(max-height:430px){#topbar{height:43px}#game{top:43px;height:calc(100% - 43px)}#menu{top:43px}#hud{inset:48px 7px 7px}#joystick{width:76px;height:76px}#stick{width:29px;height:29px}.actionBtn{width:52px;height:52px}}
</style>
</head>
<body>
<div id="rotate">↻<br>لأفضل تجربة، أدر هاتفك إلى الوضع الأفقي</div>
<div id="app">
<header id="topbar"><div class="logo">ORBIX</div><div id="stats">النقاط: <span id="score">0</span>　 الحجم: <span id="mass">20</span></div><button id="fullBtn">ملء الشاشة</button></header>
<canvas id="game"></canvas>
<section id="menu">
 <div class="menuBox">
  <h1>ORBIX</h1><div class="tag">PLAY • GROW • DOMINATE</div>
  <div>اختر ساحة المعركة</div>
  <div id="mapChoices">
   <button class="mapChoice selected" data-map="neon">🌌 المجرة</button>
   <button class="mapChoice" data-map="ice">❄️ الجليد</button>
   <button class="mapChoice" data-map="toxic">☢️ السامة</button>
  </div>
  <div class="menuActions">
   <button class="primary" id="offlineBtn">▶ ابدأ أوفلاين</button>
   <button class="mode" id="onlineBtn">🌐 اللعب الجماعي</button>
  </div>
  <div class="smallLinks">
   <button id="shopBtn">المتجر</button><button id="skinsBtn">الأشكال</button><button id="rankBtn">الترتيب</button><button id="settingsBtn">الإعدادات</button>
  </div>
  <p style="color:#91bce8;font-size:12px;line-height:1.7">طور الأوفلاين يعمل الآن ضد خصوم آليين. اللعب الجماعي الحقيقي يتطلب ربط اللعبة بخادم إنترنت.</p>
 </div>
</section>
<div id="hud">
 <div id="leader"><b>🏆 المتصدرون</b><div id="leaderList">1. أنت — ORBIX</div></div>
 <canvas id="mini" width="224" height="164"></canvas>
 <button id="back">القائمة ☰</button>
 <div id="joystick"><div id="stick"></div></div>
 <div id="actions"><button class="actionBtn" id="splitBtn">تقسيم<br>⛶</button><button class="actionBtn" id="ejectBtn">دعم<br>●</button></div>
</div>
<div id="toast"></div>
</div>
<script>
(()=>{
const canvas=document.getElementById('game'),ctx=canvas.getContext('2d'),mini=document.getElementById('mini'),mctx=mini.getContext('2d');
const $=id=>document.getElementById(id);
const menu=$('menu'),hud=$('hud'),scoreEl=$('score'),massEl=$('mass');
let W=0,H=0,scale=1,playing=false,selectedMap='neon',food=[],bots=[],pellets=[],camera={x:0,y:0},pieces=[],score=0,frame=0;
const maps={
 neon:{name:'المجرة',bg:'#07152e',grid:'#174477',edge:'#27d9ff',food:['#ffe15a','#ff68c7','#65f5a8','#66cfff']},
 ice:{name:'الجليد',bg:'#071d31',grid:'#28617a',edge:'#7ceaff',food:['#d8fbff','#71cfff','#b5a6ff','#9affed']},
 toxic:{name:'السامة',bg:'#10200f',grid:'#31552a',edge:'#9dff38',food:['#caff4d','#63ff85','#e5ff6b','#b8ffbd']}
};
const world={w:3200,h:2400};
const rand=(a,b)=>a+Math.random()*(b-a);
function resize(){const dpr=Math.min(devicePixelRatio||1,2);W=innerWidth;H=innerHeight-54;canvas.width=Math.floor(W*dpr);canvas.height=Math.floor(H*dpr);canvas.style.width=W+'px';canvas.style.height=H+'px';ctx.setTransform(dpr,0,0,dpr,0,0);scale=Math.min(W/900,H/560);if(!playing)drawMenuBg()}
addEventListener('resize',resize);
function drawMenuBg(){ctx.fillStyle='#061329';ctx.fillRect(0,0,W,H);ctx.strokeStyle='#1b4773';ctx.globalAlpha=.35;for(let x=0;x<W;x+=36){ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,H);ctx.stroke()}for(let y=0;y<H;y+=36){ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(W,y);ctx.stroke()}ctx.globalAlpha=1}
function newFood(){const c=maps[selectedMap].food;return{x:rand(20,world.w-20),y:rand(20,world.h-20),r:rand(3,6),c:c[Math.floor(Math.random()*c.length)]}}
function makeBot(i){return{x:rand(100,world.w-100),y:rand(100,world.h-100),r:rand(18,35),c:['#ff526f','#a66bff','#ffb52e','#48e0a1','#ff70cf'][i%5],name:['NOVA','SHADOW','FALCON','PIXEL','TITAN'][i%5],a:rand(0,6.28),turn:0}}
const player={x:1600,y:1200,r:20,c:'#32dfff',name:'أنت',vx:0,vy:0};
const input={x:0,y:0};
function resetGame(){food=Array.from({length:420},newFood);bots=Array.from({length:12},(_,i)=>makeBot(i));pellets=[];pieces=[{x:player.x,y:player.y,r:20,vx:0,vy:0}];score=0;player.x=world.w/2;player.y=world.h/2;player.r=20}
function start(){resetGame();playing=true;menu.style.display='none';hud.style.display='block';toast('بدأت الجولة! اجمع الحبيبات وانمُ');loop()}
function toast(t){$('toast').textContent=t;$('toast').style.display='block';clearTimeout(toast.timer);toast.timer=setTimeout(()=>$('toast').style.display='none',2200)}
function selectMap(name){selectedMap=name;document.querySelectorAll('.mapChoice').forEach(b=>b.classList.toggle('selected',b.dataset.map===name));if(playing){food=Array.from({length:420},newFood);toast('تم تغيير الخريطة')}}
document.querySelectorAll('.mapChoice').forEach(b=>b.onclick=()=>selectMap(b.dataset.map));
$('offlineBtn').onclick=start;
$('onlineBtn').onclick=()=>toast('الأونلاين الحقيقي يحتاج إعداد خادم وربط اللاعبين — لم يتم تفعيله بعد');
$('back').onclick=()=>{playing=false;menu.style.display='flex';hud.style.display='none';drawMenuBg()};
['shopBtn','skinsBtn','rankBtn','settingsBtn'].forEach((id,i)=>$(id).onclick=()=>toast(['المتجر قيد التطوير','الأشكال قيد التطوير','لوحة الترتيب المحلية أثناء الجولة','الإعدادات قيد التطوير'][i]));
$('fullBtn').onclick=()=>{if(document.documentElement.requestFullscreen)document.documentElement.requestFullscreen().catch(()=>toast('ملء الشاشة غير مدعوم في هذا المتصفح'))};
function eatCheck(p){for(let i=food.length-1;i>=0;i--){let f=food[i];if(Math.hypot(p.x-f.x,p.y-f.y)<p.r+f.r){food.splice(i,1);score++;p.r=Math.min(95,p.r+.16);food.push(newFood())}}}
function update(){
 frame++;const speed=Math.max(1.7,4.2-pieces[0].r*.018);
 for(const p of pieces){let dx=input.x,dy=input.y,l=Math.hypot(dx,dy);if(l>1){dx/=l;dy/=l}p.x+=dx*speed+p.vx;p.y+=dy*speed+p.vy;p.vx*=.91;p.vy*=.91;p.x=Math.max(p.r,Math.min(world.w-p.r,p.x));p.y=Math.max(p.r,Math.min(world.h-p.r,p.y));eatCheck(p)}
 for(const b of bots){if(Math.random()<.025)b.a+=rand(-1.4,1.4);b.x+=Math.cos(b.a)*1.2;b.y+=Math.sin(b.a)*1.2;if(b.x<80||b.x>world.w-80||b.y<80||b.y>world.h-80)b.a+=Math.PI*.7;b.x=Math.max(40,Math.min(world.w-40,b.x));b.y=Math.max(40,Math.min(world.h-40,b.y));}
 for(let i=pellets.length-1;i>=0;i--){let q=pellets[i];q.x+=q.vx;q.y+=q.vy;q.vx*=.96;q.vy*=.96;if(frame%4===0&&Math.hypot(q.x-pieces[0].x,q.y-pieces[0].y)<pieces[0].r+q.r){pellets.splice(i,1);score++}}
 camera.x=Math.max(0,Math.min(world.w-W,pieces[0].x-W/2));camera.y=Math.max(0,Math.min(world.h-H,pieces[0].y-H/2));
 scoreEl.textContent=score;massEl.textContent=Math.round(pieces.reduce((s,p)=>s+p.r,0));
 $('leaderList').innerHTML=['أنت — '+score,...bots.slice(0,4).map((b,i)=>(i+2)+'. '+b.name)].join('<br>');
}
function orb(x,y,r,c,label){ctx.save();ctx.shadowColor=c;ctx.shadowBlur=18;let g=ctx.createRadialGradient(x-r*.35,y-r*.4,2,x,y,r);g.addColorStop(0,'#ffffff');g.addColorStop(.18,c);g.addColorStop(1,'#07152c');ctx.fillStyle=g;ctx.beginPath();ctx.arc(x,y,r,0,Math.PI*2);ctx.fill();ctx.shadowBlur=0;ctx.strokeStyle='#ffffff88';ctx.lineWidth=1.5;ctx.stroke();ctx.fillStyle='#fff';ctx.font='bold 11px Arial';ctx.textAlign='center';ctx.fillText(label,x,y+4);ctx.restore()}
function draw(){
 const m=maps[selectedMap];ctx.fillStyle=m.bg;ctx.fillRect(0,0,W,H);ctx.save();ctx.translate(-camera.x,-camera.y);
 ctx.strokeStyle=m.grid;ctx.globalAlpha=.45;ctx.lineWidth=1;for(let x=0;x<=world.w;x+=60){ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,world.h);ctx.stroke()}for(let y=0;y<=world.h;y+=60){ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(world.w,y);ctx.stroke()}ctx.globalAlpha=1;
 ctx.strokeStyle=m.edge;ctx.lineWidth=8;ctx.strokeRect(0,0,world.w,world.h);
 for(const f of food){ctx.fillStyle=f.c;ctx.shadowColor=f.c;ctx.shadowBlur=9;ctx.beginPath();ctx.arc(f.x,f.y,f.r,0,Math.PI*2);ctx.fill()}ctx.shadowBlur=0;
 for(const q of pellets)orb(q.x,q.y,q.r,'#ffe26b','');
 bots.forEach(b=>orb(b.x,b.y,b.r,b.c,b.name));
 pieces.forEach((p,i)=>orb(p.x,p.y,p.r,player.c,i?'ORBIX':'أنت'));
 ctx.restore();drawMini();
}
function drawMini(){mctx.clearRect(0,0,224,164);mctx.fillStyle='#061329';mctx.fillRect(0,0,224,164);mctx.strokeStyle=maps[selectedMap].edge;mctx.strokeRect(1,1,222,162);bots.forEach(b=>{mctx.fillStyle=b.c;mctx.fillRect(b.x/world.w*224-2,b.y/world.h*164-2,4,4)});mctx.fillStyle='#fff';mctx.beginPath();mctx.arc(pieces[0].x/world.w*224,pieces[0].y/world.h*164,4,0,7);mctx.fill()}
function loop(){if(!playing)return;update();draw();requestAnimationFrame(loop)}
let active=null;
function moveStick(e){const r=$('joystick').getBoundingClientRect(),dx=e.clientX-(r.left+r.width/2),dy=e.clientY-(r.top+r.height/2),max=r.width*.34,d=Math.hypot(dx,dy),k=d>max?max/d:1;const x=dx*k,y=dy*k;$('stick').style.transform=`translate(${x}px,${y}px)`;input.x=x/max;input.y=y/max}
$('joystick').onpointerdown=e=>{active=e.pointerId;$('joystick').setPointerCapture(active);moveStick(e)};
$('joystick').onpointermove=e=>{if(e.pointerId===active)moveStick(e)};
function stop(e){if(e.pointerId!==active)return;active=null;input.x=input.y=0;$('stick').style.transform='translate(0,0)'}
$('joystick').onpointerup=stop;$('joystick').onpointercancel=stop;
$('splitBtn').onclick=()=>{if(!playing||pieces.length>=8)return;const p=pieces[0];if(p.r<28){toast('اجمع المزيد من الحبيبات أولاً');return}p.r*=.72;pieces.push({x:p.x+35,y:p.y,r:p.r*.72,vx:input.x*10||7,vy:input.y*10||0});toast('تم تقسيم الكرة')};
$('ejectBtn').onclick=()=>{if(!playing)return;const p=pieces[0];if(p.r<12){toast('حجمك لا يكفي للدعم');return}p.r-=1.5;pellets.push({x:p.x+input.x*25,y:p.y+input.y*25,r:5,vx:input.x*5,vy:input.y*5});};
addEventListener('keydown',e=>{if(e.key===' '){e.preventDefault();$('splitBtn').click()}if(e.key==='w'||e.key==='W')$('ejectBtn').click();if(e.key==='ArrowUp')input.y=-1;if(e.key==='ArrowDown')input.y=1;if(e.key==='ArrowLeft')input.x=-1;if(e.key==='ArrowRight')input.x=1});
addEventListener('keyup',e=>{if(['ArrowUp','ArrowDown'].includes(e.key))input.y=0;if(['ArrowLeft','ArrowRight'].includes(e.key))input.x=0});
resize();drawMenuBg();
})();
</script>
</body>
</html>

