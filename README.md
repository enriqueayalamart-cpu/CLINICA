
<html lang="es">
<head>
<meta charset="UTF-8">
<title>CLÍNICA - Gestión de Jóvenes e IMC</title>

<style>
body{
 font-family:'Segoe UI',sans-serif;
 background:linear-gradient(120deg,#e0f2ff,#f6fffa);
 padding:20px;
}
h2{color:#0b5394;}
.contenedor{
 background:white;
 padding:20px;
 border-radius:15px;
 box-shadow:0 8px 20px rgba(0,0,0,.1);
 margin-bottom:30px;
 max-width:1000px;
 margin:auto;
}
input,button{
 padding:8px;
 margin:5px;
 border-radius:8px;
 border:1px solid #ccc;
}
button{
 background:#0b5394;
 color:white;
 font-weight:bold;
 cursor:pointer;
}
button:hover{background:#073763;}
table{
 width:100%;
 border-collapse:collapse;
 margin-top:15px;
}
th{
 background:#d0e7ff;
 padding:8px;
}
td{
 border:1px solid #ccc;
 padding:7px;
 text-align:center;
}
tr:hover{background:#f2f8ff;}
.ok{color:green;font-weight:bold;}
.err{color:red;font-weight:bold;}
#recomendaciones{
 background:#f0f9ff;
 border-left:6px solid #0b5394;
 padding:12px;
 margin-top:10px;
 white-space:pre-line;
 border-radius:10px;
}
</style>
</head>

<body>

<div class="contenedor">
<h2>👦 Gestión de Jóvenes</h2>

CURP <input id="j_curp">
Nombre <input id="j_nombre">
Apellido <input id="j_apellido">
Fecha <input type="date" id="j_fecha">
Dirección <input id="j_direccion"><br>

<button onclick="insertarJoven()">Guardar</button>
<button onclick="cargarJoven()">Modificar</button>
<button onclick="actualizarJoven()">Actualizar</button>
<button onclick="eliminarJoven()">Eliminar</button>

<div id="msgJ"></div>

<table id="tablaJ">
<tr>
<th>CURP</th><th>Nombre</th><th>Apellido</th><th>Fecha</th><th>Dirección</th>
</tr>
</table>
</div>

<div class="contenedor">
<h2>⚖️ Registro de IMC</h2>

CURP <input id="i_curp">
Peso (kg) <input type="number" step="0.1" id="i_peso">
Altura (m) <input type="number" step="0.01" id="i_altura"><br>

<button onclick="insertarIMC()">Calcular IMC</button>

<div id="recomendaciones"></div>
<div id="msgI"></div>

<table id="tablaIMC">
<tr>
<th>CURP</th><th>Peso</th><th>Altura</th><th>IMC</th><th>Clasificación</th><th>Fecha</th>
</tr>
</table>
</div>

<script>
function msg(t, tipo, div){
 document.getElementById(div).innerHTML =
 "<p class='"+tipo+"'>"+t+"</p>";
}

/* ---------------- JÓVENES ---------------- */

function insertarJoven(){
 if(!j_curp.value||!j_nombre.value){msg("⚠️ Datos incompletos","err","msgJ");return;}
 let f=tablaJ.insertRow();
 f.insertCell(0).innerText=j_curp.value;
 f.insertCell(1).innerText=j_nombre.value;
 f.insertCell(2).innerText=j_apellido.value;
 f.insertCell(3).innerText=j_fecha.value;
 f.insertCell(4).innerText=j_direccion.value;
 msg("✅ Joven agregado","ok","msgJ");
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
   msg("✏️ Listo para editar","ok","msgJ");
   return;
  }
 }
 msg("⚠️ CURP no encontrada","err","msgJ");
}

function actualizarJoven(){
 let i=tablaJ.dataset.fila;
 if(!i){msg("⚠️ Primero busca con Modificar","err","msgJ");return;}
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
   limpiarJ();
   return;
  }
 }
 msg("⚠️ No encontrado","err","msgJ");
}

function limpiarJ(){
 j_curp.value="";j_nombre.value="";j_apellido.value="";
 j_fecha.value="";j_direccion.value="";
}

/* ---------------- IMC ---------------- */

function insertarIMC(){
 if(!i_curp.value||!i_peso.value||!i_altura.value){
  msg("⚠️ Completa todos los datos","err","msgI");return;
 }

 let p=parseFloat(i_peso.value), a=parseFloat(i_altura.value);
 let imc=(p/(a*a)).toFixed(2);

 let clas="", reco="";
 if(imc<18.5){
   clas="Bajo peso";
   reco="🔹 Subir peso de forma saludable.\n• Comer más veces al día.\n• Alimentos nutritivos.\n• Ejercicio de fuerza suave.\n• Dormir bien.";
 }
 else if(imc<25){
   clas="Normal";
   reco="🟢 Mantener peso saludable.\n• Alimentación balanceada.\n• Ejercicio regular.\n• Buena hidratación.";
 }
 else if(imc<30){
   clas="Sobrepeso";
   reco="🟡 Bajar peso saludablemente.\n• Reducir comida chatarra.\n• Más frutas y verduras.\n• Actividad física diaria.";
 }
 else{
   clas="Obesidad";
   reco="🔴 Mejorar hábitos.\n• Comer balanceado.\n• Actividad moderada.\n• Menos azúcares.\n• Apoyo médico.";
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

 msg("💪 IMC registrado","ok","msgI");
}
</script>

</body>
</html>
