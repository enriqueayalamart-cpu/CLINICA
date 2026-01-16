
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Clínica - Control de Jóvenes e IMC</title>

<style>
body{
margin:0;
font-family:'Segoe UI',sans-serif;
background:linear-gradient(120deg,#0f2027,#203a43,#2c5364);
}

h1{
text-align:center;
color:white;
padding:15px;
}

.container{width:95%;max-width:1200px;margin:auto;}
.card{
background:white;
border-radius:18px;
padding:20px;
margin-bottom:25px;
box-shadow:0 10px 25px rgba(0,0,0,.35);
}

h2{text-align:center;color:#2c5364;}

input{
padding:8px;
margin:4px;
width:240px;
border-radius:8px;
border:1px solid #ccc;
}

button{
background:#2c5364;
color:white;
font-weight:bold;
border:none;
padding:10px 16px;
border-radius:10px;
cursor:pointer;
}

button:hover{background:#0f2027;transform:scale(1.05);}

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

th{background:#2c5364;color:white;}

.msg{padding:10px;border-radius:8px;margin-bottom:10px;}
.ok{background:#e7fff2;color:#0a7a43;}
.err{background:#ffeaea;color:#9b0d0d;}

.grid{display:grid;grid-template-columns:1fr 1fr;gap:20px;}
@media(max-width:900px){.grid{grid-template-columns:1fr;}}
</style>
</head>

<body>

<h1>🏥 CLÍNICA - Sistema sin Base de Datos</h1>
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

<table id="tablaJovenes">
<tr>
<th>CURP</th><th>Nombre</th><th>Apellido</th><th>Fecha</th><th>Dirección</th>
</tr>
</table>
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

<table id="tablaIMC">
<tr>
<th>CURP</th><th>Peso</th><th>Altura</th><th>IMC</th><th>Clasificación</th><th>Fecha</th>
</tr>
</table>
</div>

</div>
</div>

<script>
function msg(texto,tipo){
 mensaje.innerHTML = `<div class="msg ${tipo}">${texto}</div>`;
}

/* ============ JÓVENES (TABLA) ============ */

function insertarJoven(){
 if(!j_curp.value){ msg("⚠️ Ingresa CURP","err"); return; }

 let tabla = document.getElementById("tablaJovenes");

 // evitar CURP repetida
 for(let i=1;i<tabla.rows.length;i++){
   if(tabla.rows[i].cells[0].innerText === j_curp.value){
     msg("⚠️ Esa CURP ya existe","err");
     return;
   }
 }

 let fila = tabla.insertRow();
 fila.insertCell(0).innerText = j_curp.value;
 fila.insertCell(1).innerText = j_nombre.value;
 fila.insertCell(2).innerText = j_apellido.value;
 fila.insertCell(3).innerText = j_fecha.value;
 fila.insertCell(4).innerText = j_direccion.value;

 msg("✅ Joven insertado en la tabla","ok");
}

function buscarJoven(){
 let tabla = tablaJovenes;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText === j_curp.value){
   j_nombre.value = tabla.rows[i].cells[1].innerText;
   j_apellido.value = tabla.rows[i].cells[2].innerText;
   j_fecha.value = tabla.rows[i].cells[3].innerText;
   j_direccion.value = tabla.rows[i].cells[4].innerText;
   msg("🔍 Joven encontrado","ok");
   return;
  }
 }
 msg("❌ No existe ese joven","err");
}

function actualizarJoven(){
 let tabla = tablaJovenes;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText === j_curp.value){
   tabla.rows[i].cells[1].innerText = j_nombre.value;
   tabla.rows[i].cells[2].innerText = j_apellido.value;
   tabla.rows[i].cells[3].innerText = j_fecha.value;
   tabla.rows[i].cells[4].innerText = j_direccion.value;
   msg("✏️ Joven actualizado","ok");
   return;
  }
 }
 msg("❌ Primero búscalo","err");
}

function eliminarJoven(){
 let tabla = tablaJovenes;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText === j_curp.value){
   tabla.deleteRow(i);
   msg("🗑️ Joven eliminado","ok");
   return;
  }
 }
 msg("❌ No existe ese joven","err");
}

/* ============ IMC (TABLA) ============ */

function imcPrevio(){
 let p=i_peso.value, a=i_altura.value;
 if(p>0 && a>0){
  previo.innerHTML="IMC aproximado: "+(p/(a*a)).toFixed(2);
 } else previo.innerHTML="";
}

function insertarIMC(){
 if(!i_curp.value || !i_peso.value || !i_altura.value){
  msg("⚠️ Completa todos los datos","err"); return;
 }

 let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value);
 let imc=(p/(a*a)).toFixed(2);
 let clas=imc<18.5?"Bajo peso":imc<25?"Normal":imc<30?"Sobrepeso":"Obesidad";

 let fila = tablaIMC.insertRow();
 fila.insertCell(0).innerText=i_curp.value;
 fila.insertCell(1).innerText=p;
 fila.insertCell(2).innerText=a;
 fila.insertCell(3).innerText=imc;
 fila.insertCell(4).innerText=clas;
 fila.insertCell(5).innerText=new Date().toLocaleString();

 msg("💪 IMC insertado en la tabla","ok");
}

function buscarIMC(){
 let tabla=tablaIMC;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText===i_curp.value){
   i_peso.value=tabla.rows[i].cells[1].innerText;
   i_altura.value=tabla.rows[i].cells[2].innerText;
   msg("🔍 Registro IMC encontrado","ok");
   return;
  }
 }
 msg("❌ No existe registro IMC","err");
}

function actualizarIMC(){
 let tabla=tablaIMC;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText===i_curp.value){
   let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value);
   let imc=(p/(a*a)).toFixed(2);
   let clas=imc<18.5?"Bajo peso":imc<25?"Normal":imc<30?"Sobrepeso":"Obesidad";

   tabla.rows[i].cells[1].innerText=p;
   tabla.rows[i].cells[2].innerText=a;
   tabla.rows[i].cells[3].innerText=imc;
   tabla.rows[i].cells[4].innerText=clas;
   tabla.rows[i].cells[5].innerText=new Date().toLocaleString();

   msg("✏️ IMC actualizado","ok");
   return;
  }
 }
 msg("❌ Primero búscalo","err");
}

function eliminarIMC(){
 let tabla=tablaIMC;
 for(let i=1;i<tabla.rows.length;i++){
  if(tabla.rows[i].cells[0].innerText===i_curp.value){
   tabla.deleteRow(i);
   msg("🗑️ IMC eliminado","ok");
   return;
  }
 }
 msg("❌ No existe registro IMC","err");
}
</script>
</body>
</html>
