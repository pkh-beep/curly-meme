<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Electric CAD Web</title>

<style>
body{
  margin:0;
  font-family:Arial;
  overflow:hidden;
}

/* الشريط العلوي */
#toolbar{
  height:50px;
  background:#1e293b;
  color:white;
  display:flex;
  align-items:center;
  gap:10px;
  padding:5px;
}

button{
  padding:8px;
  border:none;
  background:#3b82f6;
  color:white;
  border-radius:6px;
  cursor:pointer;
}

/* مساحة الرسم */
#canvas{
  width:100vw;
  height:calc(100vh - 50px);
  background:#f8fafc;
  position:relative;
  overflow:hidden;
  cursor:crosshair;
}

/* الشبكة (Grid مثل CAD) */
#canvas::before{
  content:"";
  position:absolute;
  width:100%;
  height:100%;
  background-image:
    linear-gradient(#ddd 1px, transparent 1px),
    linear-gradient(90deg, #ddd 1px, transparent 1px);
  background-size:20px 20px;
  pointer-events:none;
}

/* العناصر */
.element{
  position:absolute;
  background:#22c55e;
  color:white;
  padding:8px;
  border-radius:6px;
  cursor:move;
  user-select:none;
}

/* الأسلاك */
.wire{
  position:absolute;
  height:2px;
  background:black;
  transform-origin:left;
}
</style>
</head>

<body>

<div id="toolbar">
  <button onclick="addElement('⚡ قاطع')">قاطع</button>
  <button onclick="addElement('💡 مصباح')">مصباح</button>
  <button onclick="addElement('🔘 مفتاح')">مفتاح</button>
  <button onclick="toggleWireMode()">🔌 توصيل</button>
  <button onclick="clearAll()">🗑 حذف الكل</button>
</div>

<div id="canvas"></div>

<script>
let canvas = document.getElementById("canvas");

let selected = null;
let wireMode = false;
let firstElement = null;

function addElement(name){
  let el = document.createElement("div");
  el.className = "element";
  el.innerText = name;

  el.style.left = "100px";
  el.style.top = "100px";

  // تحريك
  el.onmousedown = (e)=>{
    if(wireMode){
      handleWire(el);
      return;
    }
    selected = el;
  };

  canvas.appendChild(el);
}

function handleWire(el){
  if(!firstElement){
    firstElement = el;
  } else {
    connect(firstElement, el);
    firstElement = null;
  }
}

function connect(a,b){
  let line = document.createElement("div");
  line.className = "wire";

  let x1 = a.offsetLeft;
  let y1 = a.offsetTop;
  let x2 = b.offsetLeft;
  let y2 = b.offsetTop;

  let length = Math.hypot(x2-x1, y2-y1);
  let angle = Math.atan2(y2-y1, x2-x1) * 180 / Math.PI;

  line.style.width = length + "px";
  line.style.left = x1 + "px";
  line.style.top = y1 + "px";
  line.style.transform = `rotate(${angle}deg)`;

  canvas.appendChild(line);
}

function toggleWireMode(){
  wireMode = !wireMode;
  alert(wireMode ? "وضع التوصيل مفعّل" : "وضع التوصيل متوقف");
}

function clearAll(){
  canvas.innerHTML = "";
}

// تحريك العناصر
document.addEventListener("mousemove", (e)=>{
  if(selected){
    selected.style.left = e.clientX + "px";
    selected.style.top = (e.clientY - 60) + "px";
  }
});

document.addEventListener("mouseup", ()=>{
  selected = null;
});
</script>

</body>
</html>
