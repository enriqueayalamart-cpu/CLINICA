<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Panel General de Jóvenes e IMC</title>

<style>
body{
margin:0;
font-family:'Segoe UI',sans-serif;
background:linear-gradient(120deg,#43cea2,#185a9d);
}

.container{width:95%;max-width:1200px;margin:auto;padding:20px;}
h1{text-align:center;color:white;text-shadow:0 3px 5px rgba(0,0,0,.4);}

.card{
background:white;
border-radius:18px;
padding:20px;
margin-bottom:25px;
box-shadow:0 10px 25px rgba(0,0,0,.25);
}

h2{text-align:center;color:#185a9d;}

input{
padding:8px;
margin:4px;
width:240px;
border-radius:8px;
border:1px solid #ccc;
}

button{
background:#185a9d;
color:white;
font-weight:bold;
border:none;
padding:10px 16px;
border-radius:10px;
cursor:pointer;
}

button:hover{background:#0d3c66;transform:scale(1.05);}

table{
width:100%;
border-collapse:collapse;
margin-top:10px;
}

th,td{
border:1px solid #ddd;
padding:8px;
text-align:center;
}

th{background:#185a9d;color:white;}

.msg{padding:10px;border-radius:8px;margin-bottom:10px;}
.ok{background:#e7fff2;color:#0a7a43;}
.err{background:#ffeaea;color:#9b0d0d;}

.grid{display:grid;grid-template-columns:1fr 1fr;gap:20px;}
@media(max-width:900px){.grid{grid-template-columns:1fr;}}
</style>
</head>

<body>
<h1>📋 Sistema Completo Jóvenes & IMC</h1>
<div class="container">

<div id="mensaje"></div>

<div class="grid">

<!-- ================== JÓVENES ================== -->
<div class="card">
<h2>👦 Gestión de Jóvenes</h2>

<input id="j_curp" placeholder="CURP"><br>
<input id="j_nombre" placeholder="Nombre"><br>
<input id="j_apellido" placeholder="Apellido"><br>
<input type="date" id="j_fecha"><br>
<input id="j_direccion" placeholder="Dirección"><br><br>

<button onclick="insertarJoven()">Insertar</button>
<button onclick="buscarJoven()">Buscar</button>
<button onclick="actualizarJoven()">Actualizar</button>
<button onclick="eliminarJoven()">Eliminar</button>

<table id="tablaJovenes"></table>
</div>

<!-- ================== IMC ================== -->
<div class="card">
<h2>⚖️ Control de IMC</h2>

<input id="i_curp" placeholder="CURP del joven"><br>
<input type="number" step="0.01" id="i_peso" placeholder="Peso (kg)" oninput="imcPrevio()"><br>
<input type="number" step="0.01" id="i_altura" placeholder="Altura (m)" oninput="imcPrevio()"><br>

<div id="previo" style="font-weight:bold;margin:10px;"></div>

<button onclick="insertarIMC()">Insertar</button>
<button onclick="buscarIMC()">Buscar</button>
<button onclick="actualizarIMC()">Actualizar</button>
<button onclick="eliminarIMC()">Eliminar</button>

<table id="tablaIMC"></table>
</div>

</div>
</div>

<script>
let jovenes = JSON.parse(localStorage.getItem("jovenes")) || [];
let imcs = JSON.parse(localStorage.getItem("imcs")) || [];

function msg(texto,tipo){
mensaje.innerHTML = `<div class="msg ${tipo}">${texto}</div>`;
}

/* ============ JÓVENES ============ */

function insertarJoven(){
 if(!j_curp.value){msg("⚠️ Ingresa CURP","err");return;}
 if(jovenes.some(j=>j.curp===j_curp.value)){
  msg("⚠️ Esa CURP ya existe","err");return;
 }

 jovenes.push({
  curp:j_curp.value,
  nombre:j_nombre.value,
  apellido:j_apellido.value,
  fecha:j_fecha.value,
  direccion:j_direccion.value
 });

 localStorage.setItem("jovenes",JSON.stringify(jovenes));
 msg("✅ Joven insertado","ok");
 mostrarJovenes();
}

function buscarJoven(){
 let j = jovenes.find(j=>j.curp===j_curp.value);
 if(j){
  j_nombre.value=j.nombre;
  j_apellido.value=j.apellido;
  j_fecha.value=j.fecha;
  j_direccion.value=j.direccion;
  msg("🔍 Joven encontrado","ok");
 }else msg("❌ No existe","err");
}

function actualizarJoven(){
 let i = jovenes.findIndex(j=>j.curp===j_curp.value);
 if(i>-1){
  jovenes[i]={curp:j_curp.value,nombre:j_nombre.value,apellido:j_apellido.value,fecha:j_fecha.value,direccion:j_direccion.value};
  localStorage.setItem("jovenes",JSON.stringify(jovenes));
  msg("✏️ Joven actualizado","ok");
  mostrarJovenes();
 }else msg("❌ Primero búscalo","err");
}

function eliminarJoven(){
 jovenes=jovenes.filter(j=>j.curp!==j_curp.value);
 localStorage.setItem("jovenes",JSON.stringify(jovenes));
 msg("🗑️ Joven eliminado","ok");
 mostrarJovenes();
}

function mostrarJovenes(){
 let t="<tr><th>CURP</th><th>Nombre</th><th>Apellido</th><th>Fecha</th><th>Dirección</th></tr>";
 jovenes.forEach(j=>{
  t+=`<tr><td>${j.curp}</td><td>${j.nombre}</td><td>${j.apellido}</td><td>${j.fecha}</td><td>${j.direccion}</td></tr>`;
 });
 tablaJovenes.innerHTML=t;
}

/* ============ IMC ============ */

function imcPrevio(){
 let p=i_peso.value, a=i_altura.value;
 if(p>0 && a>0){
  previo.innerHTML="IMC aproximado: "+(p/(a*a)).toFixed(2);
 }else previo.innerHTML="";
}

function insertarIMC(){
 let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value), c=i_curp.value;
 if(!c||!p||!a){msg("⚠️ Completa datos","err");return;}

 let imc=(p/(a*a)).toFixed(2);
 let clas=imc<18.5?"Bajo peso":imc<25?"Normal":imc<30?"Sobrepeso":"Obesidad";

 imcs.push({curp:c,peso:p,altura:a,imc:imc,clas:clas,fecha:new Date().toLocaleString()});
 localStorage.setItem("imcs",JSON.stringify(imcs));
 msg("💪 IMC insertado","ok");
 mostrarIMC();
}

function buscarIMC(){
 let i=imcs.find(x=>x.curp===i_curp.value);
 if(i){
  i_peso.value=i.peso;
  i_altura.value=i.altura;
  msg("🔍 Registro IMC encontrado","ok");
 }else msg("❌ No existe","err");
}

function actualizarIMC(){
 let pos=imcs.findIndex(x=>x.curp===i_curp.value);
 if(pos>-1){
  let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value);
  let imc=(p/(a*a)).toFixed(2);
  let clas=imc<18.5?"Bajo peso":imc<25?"Normal":imc<30?"Sobrepeso":"Obesidad";

  imcs[pos]={curp:i_curp.value,peso:p,altura:a,imc:imc,clas:clas,fecha:new Date().toLocaleString()};
  localStorage.setItem("imcs",JSON.stringify(imcs));
  msg("✏️ IMC actualizado","ok");
  mostrarIMC();
 }else msg("❌ Primero búscalo","err");
}

function eliminarIMC(){
 imcs=imcs.filter(x=>x.curp!==i_curp.value);
 localStorage.setItem("imcs",JSON.stringify(imcs));
 msg("🗑️ IMC eliminado","ok");
 mostrarIMC();
}

function mostrarIMC(){
 let t="<tr><th>CURP</th><th>Peso</th><th>Altura</th><th>IMC</th><th>Clasificación</th><th>Fecha</th></tr>";
 imcs.forEach(i=>{
  t+=`<tr><td>${i.curp}</td><td>${i.peso}</td><td>${i.altura}</td><td>${i.imc}</td><td>${i.clas}</td><td>${i.fecha}</td></tr>`;
 });
 tablaIMC.innerHTML=t;
}

mostrarJovenes();
mostrarIMC();
</script>
</body>
</html>
