
<html lang="es">
<head>
<meta charset="UTF-8">
<title>CLÍNICA - Gestión de Jóvenes e IMC</title>

<style>
body{
 font-family:'Segoe UI',sans-serif;
 background:linear-gradient(120deg,#dff1ff,#f4fff9);
 padding:20px;
}
h2{text-align:center;color:#0b5394;}
.contenedor{
 background:white;
 padding:25px;
 border-radius:18px;
 box-shadow:0 10px 25px rgba(0,0,0,.15);
 margin-bottom:35px;
 max-width:1200px;
 margin:auto;
}
.formulario{
 display:grid;
 grid-template-columns: repeat(auto-fit,minmax(220px,1fr));
 gap:12px;
}
input{
 padding:10px;
 border-radius:10px;
 border:1px solid #bbb;
 font-size:15px;
}
button{
 padding:10px;
 border-radius:10px;
 border:none;
 background:#0b5394;
 color:white;
 font-size:15px;
 font-weight:bold;
 cursor:pointer;
}
button:hover{background:#073763;}
.botones{
 grid-column:1/-1;
 text-align:center;
 margin-top:10px;
}
table{
 width:100%;
 border-collapse:collapse;
 margin-top:25px;
 font-size:15px;
}
th{
 background:#cfe7ff;
 padding:12px;
}
td{
 border:1px solid #ccc;
 padding:10px;
}
tr:hover{background:#f1f8ff;}
.ok{color:green;font-weight:bold;text-align:center;}
.err{color:red;font-weight:bold;text-align:center;}
#recomendaciones{
 background:#eef8ff;
 border-left:8px solid #0b5394;
 padding:15px;
 margin-top:15px;
 white-space:pre-line;
 border-radius:12px;
 font-size:16px;
}
</style>
</head>

<body>

<div class="contenedor">
<h2>👦 Gestión de Jóvenes</h2>

<div class="formulario">
<input placeholder="CURP" id="j_curp">
<input placeholder="Nombre" id="j_nombre">
<input placeholder="Apellido" id="j_apellido">
<input type="date" id="j_fecha">
<input placeholder="Dirección" id="j_direccion">

<div class="botones">
<button onclick="insertarJoven()">Guardar</button>
<button onclick="cargarJoven()">Modificar</button>
<button onclick="actualizarJoven()">Actualizar</button>
<button onclick="eliminarJoven()">Eliminar</button>
</div>
</div>

<div id="msgJ"></div>

<table id="tablaJ">
<tr>
<th>CURP</th><th>Nombre</th><th>Apellido</th><th>Fecha</th><th>Dirección</th>
</tr>
</table>
</div>


<div class="contenedor">
<h2>⚖️ Registro de IMC</h2>

<div class="formulario">
<input placeholder="CURP" id="i_curp">
<input type="number" step="0.1" placeholder="Peso (kg)" id="i_peso">
<input type="number" step="0.01" placeholder="Altura (m)" id="i_altura">

<div class="botones">
<button onclick="insertarIMC()">Calcular IMC</button>
</div>
</div>

<div id="recomendaciones"></div>
<div id="msgI"></div>

<table id="tablaIMC">
<tr>
<th>CURP</th><th>Peso</th><th>Altura</th><th>IMC</th><th>Clasificación</th><th>Fecha</th>
</tr>
</table>
</div>

<script>
function msg(t,tipo,div){
 document.getElementById(div).innerHTML="<p class='"+tipo+"'>"+t+"</p>";
}

/* -------- JÓVENES -------- */

function insertarJoven(){
 if(!j_curp.value||!j_nombre.value){
  msg("⚠️ Completa mínimo CURP y Nombre","err","msgJ");return;
 }
 let f=tablaJ.insertRow();
 f.insertCell(0).innerText=j_curp.value;
 f.insertCell(1).innerText=j_nombre.value;
 f.insertCell(2).innerText=j_apellido.value;
 f.insertCell(3).innerText=j_fecha.value;
 f.insertCell(4).innerText=j_direccion.value;
 msg("✅ Joven guardado en la tabla","ok","msgJ");
 limpiarJ();
}

function cargarJoven(){
 for(let i=1;i<tablaJ.rows.length;i++){
  if(tablaJ.rows[i].cells[0].innerText==j_curp.value){
   j_nombre.value=tablaJ.rows[i].cells[1].innerText;
   j_apellido.value=tablaJ.rows[i].cells[2].innerText;
   j_fecha.value=tablaJ.rows[i].cells[3].innerText;
   j_direccion.value=tablaJ.rows[i].cells[4].innerText;
   tablaJ.dataset.fila=i;
   msg("✏️ Datos cargados","ok","msgJ");
   return;
  }
 }
 msg("⚠️ CURP no encontrada","err","msgJ");
}

function actualizarJoven(){
 let i=tablaJ.dataset.fila;
 if(!i){msg("⚠️ Primero presiona Modificar","err","msgJ");return;}
 tablaJ.rows[i].cells[1].innerText=j_nombre.value;
 tablaJ.rows[i].cells[2].innerText=j_apellido.value;
 tablaJ.rows[i].cells[3].innerText=j_fecha.value;
 tablaJ.rows[i].cells[4].innerText=j_direccion.value;
 tablaJ.dataset.fila="";
 msg("✅ Registro actualizado","ok","msgJ");
 limpiarJ();
}

function eliminarJoven(){
 for(let i=1;i<tablaJ.rows.length;i++){
  if(tablaJ.rows[i].cells[0].innerText==j_curp.value){
   tablaJ.deleteRow(i);
   msg("🗑️ Joven eliminado","ok","msgJ");
   limpiarJ(); return;
  }
 }
 msg("⚠️ CURP no encontrada","err","msgJ");
}

function limpiarJ(){
 j_curp.value="";j_nombre.value="";j_apellido.value="";
 j_fecha.value="";j_direccion.value="";
}

/* -------- IMC -------- */

function insertarIMC(){
 if(!i_curp.value||!i_peso.value||!i_altura.value){
  msg("⚠️ Completa todos los datos","err","msgI");return;
 }

 let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value);
 let imc=(p/(a*a)).toFixed(2);

 let clas="", reco="";
 if(imc<18.5){
  clas="Bajo peso";
  reco="🔹 Subir peso saludablemente.\n• Comer más veces.\n• Alimentos nutritivos.\n• Ejercicio de fuerza suave.";
 }else if(imc<25){
  clas="Normal";
  reco="🟢 Peso saludable.\n• Mantén dieta balanceada.\n• Ejercicio regular.\n• Dormir bien.";
 }else if(imc<30){
  clas="Sobrepeso";
  reco="🟡 Bajar peso saludablemente.\n• Más frutas y verduras.\n• Menos comida chatarra.\n• Actividad física diaria.";
 }else{
  clas="Obesidad";
  reco="🔴 Mejorar hábitos.\n• Comer balanceado.\n• Actividad moderada.\n• Reducir azúcares.\n• Apoyo médico.";
 }

 let min=(18.5*(a*a)).toFixed(1);
 let max=(24.9*(a*a)).toFixed(1);

 recomendaciones.innerText=
 "📊 IMC: "+imc+" ("+clas+")\n"+
 "⚖️ Peso ideal aproximado: "+min+" kg – "+max+" kg\n"+
 "📌 Recomendaciones:\n"+reco;

 let f=tablaIMC.insertRow();
 f.insertCell(0).innerText=i_curp.value;
 f.insertCell(1).innerText=p;
 f.insertCell(2).innerText=a;
 f.insertCell(3).innerText=imc;
 f.insertCell(4).innerText=clas;
 f.insertCell(5).innerText=new Date().toLocaleString();

 msg("💪 IMC registrado en la tabla","ok","msgI");

 i_curp.value=""; i_peso.value=""; i_altura.value="";
}
</script>

</body>
</html>
