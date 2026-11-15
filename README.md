
<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>ORBIX</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#050b18;color:#fff;font-family:Arial;overflow:hidden}
#game{display:none;position:fixed;inset:0}
canvas{width:100%;height:100%;display:block;touch-action:none}
#home{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:radial-gradient(circle,#102b55,#030711)}
.panel{text-align:center;background:#0b1930;padding:28px;border:1px solid #168bff;border-radius:20px;width:85%;max-width:360px}
h1{font-size:48px;color:#32aaff;letter-spacing:5px;margin:5px}
input{width:100%;padding:13px;margin:15px 0;background:#071224;color:white;border:1px solid #168bff;border-radius:10px;text-align:center;font-size:17px}
button{background:#087ee8;color:white;border:0;padding:13px 20px;border-radius:12px;font-size:17px;font-weight:bold;cursor:pointer}
#hud{position:absolute;top:10px;left:10px;right:10px;display:flex;justify-content:space-between;pointer-events:none}
.hudbox{background:#06152bd9;padding:8px 12px;border:1px solid #168bff;border-radius:10px}
#controls{position:absolute;bottom:18px;left:18px;display:flex;gap:10px}
.control{width:65px;height:65px;border-radius:50%;background:#087ee8bb;border:2px solid #67c3ff;font-size:13px}
#stick{position:absolute;bottom:18px;right:20px;width:105px;height:105px;border-radius:50%;background:#17365c99;border:2px solid #318dce;touch-action:none}
#knob{position:absolute;width:43px;height:43px;border-radius:50%;background:#42b7ff;left:29px;top:29px}
#map{position:absolute;right:12px;top:55px;width:105px;height:70px;border:1px solid #39aaff;background:#06152b99}
#rotate{display:none;position:fixed;inset:0;background:#050b18;z-index:10;align-items:center;justify-content:center;text-align:center;font-size:22px}
@media(orientation:portrait){#rotate{display:flex}}
</style>
</head>
<body>
<div id="rotate">🔄 أدر هاتفك للوضع الأفقي لتلعب ORBIX</div>

<div id="home">
 <div class="panel">
  <h1>ORBIX</h1>
  <p>ENTER THE ORBIX UNIVERSE</p>
  <input id="playerName" placeholder="اكتب اسم اللاعب">
  <br>
  <button onclick="startGame()">ابدأ اللعب</button>
  <p style="color:#70bfff;font-size:12px">OFFLINE DEMO • الإصدار التجريبي</p>
 </div>
</div>

<div id="game">
 <canvas id="canvas"></canvas>
 <div id="hud">
  <div class="hudbox">ORBIX | <span id="score">0</span></div>
  <div class="hudbox" id="playerLabel">PLAYER</div>
 </div>
 <canvas id="map"></canvas>
 <div id="controls">
  <button class="control" onclick="splitPlayer()">تقسيم</button>
  <button class="control" onclick="ejectMass()">إطلاق</button>
 </div>
 <div id="stick"><div id="knob"></div></div>
</div>

<script>
const canvas=document.getElementById("canvas");
const ctx=canvas.getContext("2d");
const map=document.getElementById("map");
const mctx=map.getContext("2d");

let W,H,worldW=3600,worldH=2400;
let player,foods=[],bots=[],viruses=[],camera={x:0,y:0};
let keys={},joy={x:0,y:0},running=false;

function resize(){
 W=canvas.width=innerWidth*devicePixelRatio;
 H=canvas.height=innerHeight*devicePixelRatio;
 ctx.setTransform(1,0,0,1,0,0);
}
addEventListener("resize",resize);
resize();

function random(min,max){return Math.random()*(max-min)+min}

function createFood(){
 return {x:random(40,worldW-40),y:random(40,worldH-40),r:random(4,7),color:["#19d9ff","#36ff9b","#e5d54b","#b36cff"][Math.floor(random(0,4))]};
}
function createBot(){
 return {x:random(200,worldW-200),y:random(200,worldH-200),r:random(22,38),color:"#b14cff",vx:0,vy:0,name:"BOT"};
}
function createVirus(){
 return {x:random(150,worldW-150),y:random(150,worldH-150),r:36};
}

function startGame(){
 const name=document.getElementById("playerName").value.trim()||"PLAYER";
 player={x:worldW/2,y:worldH/2,r:25,mass:100,name:name,color:"#20aaff",parts:[]};
 foods=Array.from({length:350},createFood);
 bots=Array.from({length:18},createBot);
 viruses=Array.from({length:28},createVirus);
 document.getElementById("playerLabel").textContent=name;
 document.getElementById("home").style.display="none";
 document.getElementById("game").style.display="block";
 running=true;
 resize();
 loop();
}

function movePlayer(){
 let dx=joy.x,dy=joy.y;
 if(keys.ArrowUp||keys.KeyW)dy-=1;
 if(keys.ArrowDown||keys.KeyS)dy+=1;
 if(keys.ArrowLeft||keys.KeyA)dx-=1;
 if(keys.ArrowRight||keys.KeyD)dx+=1;
 let len=Math.hypot(dx,dy);
 if(len>0){
  dx/=len;dy/=len;
  let speed=Math.max(1.4,5.2- player.r/35);
  player.x+=dx*speed;
  player.y+=dy*speed;
 }
 player.x=Math.max(player.r,Math.min(worldW-player.r,player.x));
 player.y=Math.max(player.r,Math.min(worldH-player.r,player.y));
}

function splitPlayer(){
 if(player.mass<70)return;
 player.mass*=0.5;
 player.r=Math.sqrt(player.mass)*2.5;
 player.parts.push({x:player.x+player.r*2,y:player.y,r:player.r,mass:player.mass});
}
function ejectMass(){
 if(player.mass<40)return;
 player.mass-=5;
 let angle=Math.atan2(joy.y,joy.x);
 if(!joy.x&&!joy.y)angle=0;
 foods.push({x:player.x+Math.cos(angle)*player.r*2,y:player.y+Math.sin(angle)*player.r*2,r:6,color:"#38eaff"});
 player.r=Math.sqrt(player.mass)*2.5;
}

function update(){
 movePlayer();
 camera.x=player.x-W/2;
 camera.y=player.y-H/2;

 for(let f of foods){
  if(Math.hypot(f.x-player.x,f.y-player.y)<player.r){
   player.mass+=0.6;
   player.r=Math.sqrt(player.mass)*2.5;
   f.x=random(30,worldW-30);f.y=random(30,worldH-30);
  }
 }
 for(let b of bots){
  let dx=player.x-b.x,dy=player.y-b.y,d=Math.hypot(dx,dy)||1;
  b.x+=dx/d*0.35;b.y+=dy/d*0.35;
  if(d<player.r+b.r && player.r>b.r*1.15){
   player.mass+=b.r*0.4;
   player.r=Math.sqrt(player.mass)*2.5;
   b.x=random(100,worldW-100);b.y=random(100,worldH-100);
  }
 }
 for(let v of viruses){
  if(Math.hypot(v.x-player.x,v.y-player.y)<player.r+v.r && player.r>v.r*1.2){
   player.mass+=10;
   player.r=Math.sqrt(player.mass)*2.5;
   v.x=random(100,worldW-100);v.y=random(100,worldH-100);
  }
 }
 document.getElementById("score").textContent=Math.floor(player.mass);
}

function draw(){
 ctx.clearRect(0,0,W,H);
 ctx.fillStyle="#071326";ctx.fillRect(0,0,W,H);

 ctx.save();
 ctx.translate(-camera.x,-camera.y);

 ctx.strokeStyle="#142b48";ctx.lineWidth=1;
 for(let x=0;x<worldW;x+=80){
  ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,worldH);ctx.stroke();
 }
 for(let y=0;y<worldH;y+=80){
  ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(worldW,y);ctx.stroke();
 }

 ctx.strokeStyle="#188cff";ctx.lineWidth=8;
 ctx.strokeRect(0,0,worldW,worldH);

 for(let f of foods){
  ctx.beginPath();ctx.fillStyle=f.color;
  ctx.arc(f.x,f.y,f.r,0,Math.PI*2);ctx.fill();
 }
 for(let v of viruses){
  ctx.beginPath();ctx.fillStyle="#16c77a";
  ctx.arc(v.x,v.y,v.r,0,Math.PI*2);ctx.fill();
  ctx.strokeStyle="#72ffc2";ctx.lineWidth=3;ctx.stroke();
  ctx.fillStyle="#062c23";ctx.font="bold 12px Arial";ctx.textAlign="center";
  ctx.fillText("VIRUS",v.x,v.y+4);
 }
 for(let b of bots){
  drawBall(b.x,b.y,b.r,b.color,b.name);
 }
 for(let p of player.parts)drawBall(p.x,p.y,p.r,"#168bff","");
 drawBall(player.x,player.y,player.r,player.color,player.name);

 ctx.restore();
 drawMap();
}
function drawBall(x,y,r,color,name){
 ctx.beginPath();ctx.fillStyle=color;ctx.arc(x,y,r,0,Math.PI*2);ctx.fill();
 ctx.strokeStyle="#b6eaff";ctx.lineWidth=2;ctx.stroke();
 if(name){ctx.fillStyle="white";ctx.font="bold 13px Arial";ctx.textAlign="center";ctx.fillText(name,x,y+4);}
}
function drawMap(){
 mctx.clearRect(0,0,105,70);
 mctx.fillStyle="#071326";mctx.fillRect(0,0,105,70);
 mctx.fillStyle="#35baff";
 mctx.fillRect(player.x/worldW*105-2,player.y/worldH*70-2,5,5);
 mctx.fillStyle="#b14cff";
 for(let b of bots)mctx.fillRect(b.x/worldW*105,b.y/worldH*70,2,2);
}
function loop(){
 if(!running)return;
 update();draw();
 requestAnimationFrame(loop);
}

addEventListener("keydown",e=>{keys[e.code]=true});
addEventListener("keyup",e=>{keys[e.code]=false});

const stick=document.getElementById("stick");
const knob=document.getElementById("knob");
let touching=false;
function joystick(e){
 const rect=stick.getBoundingClientRect();
 let x=e.clientX-(rect.left+rect.width/2);
 let y=e.clientY-(rect.top+rect.height/2);
 let d=Math.hypot(x,y),max=35;
 if(d>max){x=x/d*max;y=y/d*max}
 joy.x=x/max;joy.y=y/max;
 knob.style.left=(29+x)+"px";
 knob.style.top=(29+y)+"px";
}
stick.addEventListener("pointerdown",e=>{touching=true;stick.setPointerCapture(e.pointerId);joystick(e)});
stick.addEventListener("pointermove",e=>{if(touching)joystick(e)});
stick.addEventListener("pointerup",()=>{touching=false;joy.x=joy.y=0;knob.style.left="29px";knob.style.top="29px"});
stick.addEventListener("pointercancel",()=>{touching=false;joy.x=joy.y=0});
</script>
</body>
</html>

