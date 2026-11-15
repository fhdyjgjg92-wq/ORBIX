 

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>ORBIX 2.0</title>
<style>
*{box-sizing:border-box}
html,body{
 margin:0;width:100%;height:100%;overflow:hidden;
 background:#030916;color:white;
 font-family:Arial,sans-serif;touch-action:none
}
canvas{display:block;width:100%;height:100%}
#hud{
 position:absolute;top:12px;left:12px;right:12px;
 display:flex;justify-content:space-between;pointer-events:none;
 font-weight:bold;text-shadow:0 0 10px #008cff
}
#brand{font-size:22px;color:#32aaff;letter-spacing:3px}
#score{font-size:14px;color:white}
#controls{
 position:absolute;bottom:24px;left:20px;right:20px;
 display:flex;justify-content:space-between;align-items:end;
 pointer-events:none
}
#joy{
 width:105px;height:105px;border-radius:50%;
 background:#168bff22;border:2px solid #38aaff88;
 position:relative;pointer-events:auto;touch-action:none
}
#knob{
 width:39px;height:39px;border-radius:50%;
 background:#32aaff;border:2px solid white;
 box-shadow:0 0 15px #008cff;
 position:absolute;left:31px;top:31px
}
.actions{display:flex;gap:12px;pointer-events:auto}
.action{
 width:65px;height:65px;border-radius:50%;
 background:linear-gradient(145deg,#0758a8,#061b3b);
 border:2px solid #38aaff;
 color:white;font-weight:bold;font-size:12px;
 box-shadow:0 0 15px #007bff55;
 touch-action:none
}
.action:active{background:#087cff;transform:scale(.94)}
#hint{
 position:absolute;top:55px;left:12px;
 color:#8abde9;font-size:11px;pointer-events:none
}
@media(max-height:450px){
 #controls{bottom:8px}
 #joy{width:85px;height:85px}
 #knob{left:24px;top:24px}
 .action{width:55px;height:55px}
}
</style>
</head>
<body>

<canvas id="game"></canvas>

<div id="hud">
 <div id="brand">ORBIX</div>
 <div id="score">MASS: 20</div>
</div>
<div id="hint">GROW • COMPETE • SURVIVE</div>

<div id="controls">
 <div id="joy"><div id="knob"></div></div>
 <div class="actions">
  <button class="action" id="eject">EJECT</button>
  <button class="action" id="split">SPLIT</button>
 </div>
</div>

<script>
const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");
const scoreUI=document.getElementById("score");

let W,H,DPR;
function resize(){
 DPR=Math.min(devicePixelRatio||1,2);
 W=innerWidth;H=innerHeight;
 canvas.width=W*DPR;
 canvas.height=H*DPR;
 ctx.setTransform(DPR,0,0,DPR,0,0);
}
addEventListener("resize",resize);
resize();

const world={w:4200,h:3000};
let cells=[];
let food=[];
let viruses=[];
let pellets=[];
let bots=[];
let direction={x:0,y:0};
let camera={x:2100,y:1500,zoom:1};

const player={id:1,color:"#168cff"};

function makeCell(x,y,mass,color,owner){
 return {x,y,mass,r:Math.sqrt(mass)*3.5,
 vx:0,vy:0,color,owner};
}

cells.push(makeCell(2100,1500,20,player.color,1));

function spawnFood(n){
 for(let i=0;i<n;i++){
  food.push({
   x:Math.random()*world.w,
   y:Math.random()*world.h,
   r:4+Math.random()*3,
   color:["#35eaff","#39ffb0","#f4d35e","#a77bff"][i%4]
  });
 }
}
function spawnVirus(n){
 for(let i=0;i<n;i++){
  viruses.push({
   x:200+Math.random()*(world.w-400),
   y:200+Math.random()*(world.h-400),
   r:38,color:"#22d76b"
  });
 }
}
function spawnBots(n){
 for(let i=0;i<n;i++){
  bots.push(makeCell(
   Math.random()*world.w,
   Math.random()*world.h,
   15+Math.random()*35,
   ["#ed4e8a","#a15cff","#ff9e36"][i%3],
   100+i
  ));
 }
}
spawnFood(650);
spawnVirus(28);
spawnBots(15);

function totalMass(){
 return cells.reduce((a,c)=>a+c.mass,0);
}

function split(){
 if(cells.length>=16)return;
 let targets=[...cells].filter(c=>c.mass>=30);
 for(const c of targets){
  if(cells.length>=16)break;
  const angle=Math.atan2(direction.y,direction.x);
  if(!direction.x&&!direction.y)continue;
  let m=c.mass/2;
  c.mass=m;
  c.r=Math.sqrt(m)*3.5;
  let child=makeCell(
   c.x+Math.cos(angle)*(c.r+15),
   c.y+Math.sin(angle)*(c.r+15),
   m,c.color,1
  );
  child.vx=Math.cos(angle)*15;
  child.vy=Math.sin(angle)*15;
  cells.push(child);
 }
}

function eject(){
 for(const c of cells){
  if(c.mass<18)continue;
  let angle=Math.atan2(direction.y,direction.x);
  if(!direction.x&&!direction.y)angle=0;
  c.mass-=5;
  c.r=Math.sqrt(c.mass)*3.5;
  pellets.push({
   x:c.x+Math.cos(angle)*(c.r+10),
   y:c.y+Math.sin(angle)*(c.r+10),
   vx:Math.cos(angle)*11,
   vy:Math.sin(angle)*11,
   r:7,color:c.color
  });
 }
}
document.getElementById("split").addEventListener("pointerdown",e=>{
 e.preventDefault();split();
});
document.getElementById("eject").addEventListener("pointerdown",e=>{
 e.preventDefault();eject();
});

// Touch joystick
const joy=document.getElementById("joy");
const knob=document.getElementById("knob");
let joyPointer=null;

joy.addEventListener("pointerdown",e=>{
 joyPointer=e.pointerId;
 joy.setPointerCapture(e.pointerId);
 moveJoy(e);
});
joy.addEventListener("pointermove",e=>{
 if(e.pointerId===joyPointer)moveJoy(e);
});
function moveJoy(e){
 const rect=joy.getBoundingClientRect();
 let x=e.clientX-(rect.left+rect.width/2);
 let y=e.clientY-(rect.top+rect.height/2);
 const max=32;
 const len=Math.hypot(x,y);
 if(len>max){x=x/len*max;y=y/len*max}
 direction.x=x/max;
 direction.y=y/max;
 knob.style.left=(31+x)+"px";
 knob.style.top=(31+y)+"px";
}
function resetJoy(e){
 if(e.pointerId!==joyPointer)return;
 joyPointer=null;
 direction={x:0,y:0};
 knob.style.left="31px";
 knob.style.top="31px";
}
joy.addEventListener("pointerup",resetJoy);
joy.addEventListener("pointercancel",resetJoy);

function update(){
 for(const c of cells){
  let speed=Math.max(1.5,5.8-c.r*.025);
  c.vx+=direction.x*speed*.12;
  c.vy+=direction.y*speed*.12;
  c.vx*=.90;c.vy*=.90;
  c.x+=c.vx;c.y+=c.vy;
  c.x=Math.max(c.r,Math.min(world.w-c.r,c.x));
  c.y=Math.max(c.r,Math.min(world.h-c.r,c.y));
  c.r=Math.sqrt(c.mass)*3.5;
 }
 // Eat food
 for(const c of cells){
  for(let i=food.length-1;i>=0;i--){
   const f=food[i];
   if(Math.hypot(c.x-f.x,c.y-f.y)<c.r+f.r){
    c.mass+=.7;c.r=Math.sqrt(c.mass)*3.5;
    food.splice(i,1);
   }
  }
 }
 // Eat pellets
 for(const c of cells){
  for(let i=pellets.length-1;i>=0;i--){
   let p=pellets[i];
   if(Math.hypot(c.x-p.x,c.y-p.y)<c.r+p.r){
    c.mass+=5;c.r=Math.sqrt(c.mass)*3.5;
    pellets.splice(i,1);
   }
  }
 }
 // Move pellets
 pellets.forEach(p=>{
  p.x+=p.vx;p.y+=p.vy;
  p.vx*=.96;p.vy*=.96;
 });
 pellets=pellets.filter(p=>
  Math.hypot(p.vx,p.vy)>.5
 );
 // Viruses: large cells explode
 for(const c of [...cells]){
  for(const v of viruses){
   if(c.mass>45&&Math.hypot(c.x-v.x,c.y-v.y)<c.r+v.r*.65){
    const index=cells.indexOf(c);
    if(index<0)continue;
    cells.splice(index,1);
    let pieces=Math.min(8,Math.max(3,Math.floor(c.mass/20)));
    let m=c.mass/pieces;
    for(let i=0;i<pieces;i++){
     let a=i*Math.PI*2/pieces;
     let child=makeCell(c.x,c.y,m,c.color,1);
     child.vx=Math.cos(a)*12;
     child.vy=Math.sin(a)*12;
     cells.push(child);
    }
    break;
   }
  }
 }
 // Bot movement and eating
 for(const b of bots){
  let target=food.reduce((best,f)=>{
   let d=Math.hypot(f.x-b.x,f.y-b.y);
   return d<best.d?{f,d}:best;
  },{f:null,d:Infinity}).f;
  if(target){
   let dx=target.x-b.x,dy=target.y-b.y;
   let d=Math.hypot(dx,dy)||1;
   b.x+=dx/d*1.2;b.y+=dy/d*1.2;
  }
  for(let i=food.length-1;i>=0;i--){
   if(Math.hypot(b.x-food[i].x,b.y-food[i].y)<b.r){
    b.mass+=.7;b.r=Math.sqrt(b.mass)*3.5;
    food.splice(i,1);
   }
  }
 }
 // Player eats smaller bots
 for(const c of cells){
  for(let i=bots.length-1;i>=0;i--){
   const b=bots[i];
   if(c.mass>b.mass*1.15&&
   Math.hypot(c.x-b.x,c.y-b.y)<c.r-b.r*.2){
    c.mass+=b.mass*.8;c.r=Math.sqrt(c.mass)*3.5;
    bots.splice(i,1);
    setTimeout(()=>bots.push(makeCell(
     Math.random()*world.w,Math.random()*world.h,
     20,"#ed4e8a",200+Math.random()*1000
    )),2500);
   }
  }
 }
 // Camera
 let main=cells[0];
 if(main){
  camera.x+=(main.x-camera.x)*.08;
  camera.y+=(main.y-camera.y)*.08;
  let targetZoom=Math.max(.42,Math.min(1,32/(main.r+20)));
  camera.zoom+=(targetZoom-camera.zoom)*.04;
 }
 if(food.length<500)spawnFood(150);
 scoreUI.textContent="MASS: "+Math.floor(totalMass());
}

function drawGrid(){
 ctx.fillStyle="#061327";
 ctx.fillRect(0,0,W,H);
 ctx.strokeStyle="#123050";
 ctx.lineWidth=1;
 let step=100;
 let left=camera.x-W/(2*camera.zoom);
 let top=camera.y-H/(2*camera.zoom);
 let startX=Math.floor(left/step)*step;
 let startY=Math.floor(top/step)*step;
 for(let x=startX;x<left+W/camera.zoom+step;x+=step){
  ctx.beginPath();ctx.moveTo(x,top);
  ctx.lineTo(x,top+H/camera.zoom);ctx.stroke();
 }
 for(let y=startY;y<top+H/camera.zoom+step;y+=step){
  ctx.beginPath();ctx.moveTo(left,y);
  ctx.lineTo(left+W/camera.zoom,y);ctx.stroke();
 }
}
function circle(x,y,r,color,stroke){
 ctx.beginPath();ctx.arc(x,y,r,0,Math.PI*2);
 ctx.fillStyle=color;ctx.fill();
 if(stroke){ctx.strokeStyle=stroke;ctx.lineWidth=3;ctx.stroke()}
}
function render(){
 ctx.clearRect(0,0,W,H);
 ctx.save();
 ctx.translate(W/2,H/2);
 ctx.scale(camera.zoom,camera.zoom);
 ctx.translate(-camera.x,-camera.y);
 drawGrid();

 // World boundary
 ctx.strokeStyle="#168cff";
 ctx.lineWidth=10;
 ctx.strokeRect(0,0,world.w,world.h);

 // Food
 for(const f of food)circle(f.x,f.y,f.r,f.color);

 // Ejected mass
 for(const p of pellets)circle(p.x,p.y,p.r,p.color);

 // Viruses
 for(const v of viruses){
  ctx.beginPath();
  for(let i=0;i<32;i++){
   let a=i*Math.PI*2/32;
   let r=i%2===0?v.r:v.r*.82;
   let x=v.x+Math.cos(a)*r;
   let y=v.y+Math.sin(a)*r;
   if(i===0)ctx.moveTo(x,y);else ctx.lineTo(x,y);
  }
  ctx.closePath();
  ctx.fillStyle="#13bd58";
  ctx.fill();
  ctx.strokeStyle="#71ff9b";
  ctx.lineWidth=4;
  ctx.stroke();
 }

 // Bots
 for(const b of bots){
  circle(b.x,b.y,b.r,b.color,"#ffffff88");
 }
 // Player cells
 for(const c of cells){
  circle(c.x,c.y,c.r,c.color,"#a7e6ff");
  ctx.fillStyle="white";
  ctx.textAlign="center";
  ctx.font="bold 14px Arial";
  ctx.fillText("ORBIX",c.x,c.y+5);
 }
 ctx.restore();
}
function loop(){
 update();
 render();
 requestAnimationFrame(loop);
}
loop();
</script>
</body>
</html>
