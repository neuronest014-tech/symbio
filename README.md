<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Project S</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;800&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:'Inter',sans-serif;background:#000;color:#fff;height:100vh;overflow:hidden}

/* Stage */
.stage{position:relative;height:100vh;width:100vw;display:flex;align-items:center;justify-content:center}

/* Orb presence */
.orb{
width:180px;height:180px;border-radius:50%;
background:radial-gradient(circle,#7aa2ff,#b44cff);
box-shadow:0 0 120px #7aa2ff88,0 0 240px #b44cff44;
animation:float 6s ease-in-out infinite;
position:absolute;
}

/* Input */
.interface{position:absolute;bottom:60px;text-align:center}
input{
width:420px;max-width:90vw;padding:18px 22px;border-radius:40px;border:1px solid #ffffff33;background:#0b0f1a;color:#fff;font-size:16px;outline:none
}

.hint{opacity:.45;font-size:14px;margin-top:10px}

/* Response */
.response{
position:absolute;top:120px;text-align:center;font-size:28px;font-weight:300;max-width:800px;opacity:0;transition:1s
}
.response.visible{opacity:1}

/* Nodes */
.node{width:70px;height:70px;border-radius:50%;background:radial-gradient(circle,#7aa2ff,#b44cff);position:absolute;filter:blur(1px);opacity:.8;animation:float 8s ease-in-out infinite}
.line{position:absolute;height:2px;background:#7aa2ff55;transform-origin:left}

/* CTA */
.cta{
position:absolute;bottom:140px;font-size:22px;font-weight:600;opacity:0;transition:1s
}
.cta.visible{opacity:1}

@keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-22px)}}
</style>
</head>
<body>

<div class="stage" id="stage">

<div class="response" id="response">Hello.</div>
<div class="orb" id="orb"></div>

<div class="cta" id="cta">Project S — The network begins with you</div>

<div class="interface">
<input id="input" placeholder="Say something to your future partner…">
<div class="hint">Press enter</div>
</div>

</div>

<script>
const input=document.getElementById('input');
const response=document.getElementById('response');
const stage=document.getElementById('stage');
const orb=document.getElementById('orb');
const cta=document.getElementById('cta');
let nodes=[];

function createNode(){
const n=document.createElement('div');
n.className='node';
n.style.left=Math.random()*90+'%';
n.style.top=Math.random()*90+'%';
stage.appendChild(n);
nodes.push(n);
connect();
}

function connect(){
document.querySelectorAll('.line').forEach(l=>l.remove());
nodes.forEach(a=>nodes.forEach(b=>{
if(a!==b){
const line=document.createElement('div');
line.className='line';
const ax=a.offsetLeft,ay=a.offsetTop,bx=b.offsetLeft,by=b.offsetTop;
const dx=bx-ax,dy=by-ay;
const dist=Math.sqrt(dx*dx+dy*dy);
line.style.width=dist+'px';
line.style.left=ax+35+'px';
line.style.top=ay+35+'px';
line.style.transform=`rotate(${Math.atan2(dy,dx)}rad)`;
stage.appendChild(line);
}
}))
}

input.addEventListener('keydown',e=>{
if(e.key==='Enter'&&input.value.trim()){
const msg=input.value;
input.value='';

response.textContent='I hear you.';
response.classList.add('visible');

orb.style.transform='scale(1.2)';
setTimeout(()=>orb.style.transform='scale(1)',500);

createNode();

if(nodes.length>2)cta.classList.add('visible');
}
});

/* Orb subtle follow */
window.addEventListener('mousemove',e=>{
const x=(e.clientX/window.innerWidth-.5)*30;
const y=(e.clientY/window.innerHeight-.5)*30;
orb.style.marginLeft=x+'px';
orb.style.marginTop=y+'px';
});
</script>
<script>
const canvas = document.getElementById("canvas");
const input = document.getElementById("input");

function randomPos(){
  return {
    x: Math.random()*(window.innerWidth-300),
    y: Math.random()*(window.innerHeight-200)
  }
}

function addNode(text,type){
  const node = document.createElement("div");
  node.className = "node "+type;
  node.innerText = text;

  const pos = randomPos();
  node.style.left = pos.x+"px";
  node.style.top = pos.y+"px";

  canvas.appendChild(node);
  return node;
}

function updateNode(node,text){
  node.innerText = text;
}

async function sendMessage(){
 const res = await fetch("http://localhost:3000/chat",{
  method:"POST",
  headers:{"Content-Type":"application/json"},
  body:JSON.stringify({message:text})
});

const ai = await res.json();
updateNode(thinking,ai);

}

input.addEventListener("keypress",e=>{
  if(e.key==="Enter") sendMessage();
});
</script>

</body>
</html>
