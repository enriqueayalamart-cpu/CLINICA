# CLINICA
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Panel de Jóvenes e IMC</title>

<style>
body{
    margin:0;
    font-family: 'Segoe UI', sans-serif;
    background: linear-gradient(120deg,#74ebd5,#ACB6E5);
    min-height:100vh;
}

.container{
    width:95%;
    max-width:1200px;
    margin:auto;
    padding:20px;
}

.card{
    background:white;
    border-radius:15px;
    padding:20px;
    margin-bottom:30px;
    box-shadow:0 10px 25px rgba(0,0,0,.15);
}

h2{
    text-align:center;
    color:#333;
}

input{
    padding:8px;
    width:250px;
    border-radius:8px;
    border:1px solid #ccc;
    margin:5px;
}

button, input[type=submit]{
    padding:10px 18px;
    border:none;
    border-radius:10px;
    cursor:pointer;
    background:#4e73df;
    color:white;
    font-weight:bold;
}

button:hover, input[type=submit]:hover{
    background:#2e59d9;
    transform:scale(1.05);
}

table{
    width:100%;
    border-collapse:collapse;
    margin-top:15px;
}

th,td{
    border:1px solid #ddd;
    padding:8px;
    text-align:center;
}

th{
    background:#4e73df;
    color:white;
}

.msg.ok{background:#e8ffed;color:#037a1a;padding:10px;border-radius:8px;}
.msg.err{background:#ffeaea;color:#a10d0d;padding:10px;border-radius:8px;}

.grid{
    display:grid;
    grid-template-columns:1fr 1fr;
    gap:20px;
}

@media(max-width:800px){
    .grid{grid-template-columns:1fr;}
}
</style>

<script>
function calcularIMC(){
    let peso = document.getElementById("peso").value;
    let altura = document.getElementById("altura").value;

    if(peso>0 && altura>0){
        let imc = peso / (altura*altura);
        document.getElementById("imcPrevio").innerHTML = 
        "📊 IMC aproximado: <b>"+imc.toFixed(2)+"</b>";
    }else{
        document.getElementById("imcPrevio").innerHTML = "";
    }
}
</script>
</head>

<body>
<div class="container">

<?php
include("conexion.php");

/* =========================
        JÓVENES
=========================*/

$curp = $_POST['CURP'] ?? '';
$nombre = $_POST['Nombre'] ?? '';
$apellido = $_POST['Apellido'] ?? '';
$fecha = $_POST['Fecha_Nacimiento'] ?? '';
$direccion = $_POST['Direccion'] ?? '';

if(isset($_POST['guardar_joven'])){
    $sql="INSERT INTO jovenes VALUES('$curp','$nombre','$apellido','$fecha','$direccion')";
    if(mysqli_query($conexion,$sql)) echo "<div class='msg ok'>✅ Joven guardado.</div>";
    else echo "<div class='msg err'>❌ Error: ".mysqli_error($conexion)."</div>";
}

if(isset($_POST['eliminar_joven'])){
    mysqli_query($conexion,"DELETE FROM jovenes WHERE CURP='$curp'");
}

/* =========================
          IMC
=========================*/
if(isset($_POST['guardar_imc'])){
    $peso=$_POST['Peso'];
    $altura=$_POST['Altura'];
    $imc=round($peso/($altura*$altura),2);

    if($imc<18.5){$clas="Bajo peso";}
    elseif($imc<25){$clas="Normal";}
    elseif($imc<30){$clas="Sobrepeso";}
    else{$clas="Obesidad";}

    $sql="INSERT INTO indice_masa_corporal
    (CURP,Peso,Altura,IMC,Clasificacion,Fecha_Registro)
    VALUES('$curp','$peso','$altura','$imc','$clas',NOW())";

    if(mysqli_query($conexion,$sql))
        echo "<div class='msg ok'>💪 IMC guardado correctamente. Resultado: $imc ($clas)</div>";
    else
        echo "<div class='msg err'>❌ Error IMC: ".mysqli_error($conexion)."</div>";
}
?>

<div class="grid">

<!-- ================= JÓVENES ================= -->
<div class="card">
<h2>👦 Gestión de Jóvenes</h2>
<form method="post">
<input type="text" name="CURP" placeholder="CURP" required><br>
<input type="text" name="Nombre" placeholder="Nombre"><br>
<input type="text" name="Apellido" placeholder="Apellido"><br>
<input type="date" name="Fecha_Nacimiento"><br>
<input type="text" name="Direccion" placeholder="Dirección"><br><br>

<input type="submit" name="guardar_joven" value="Guardar">
<input type="submit" name="eliminar_joven" value="Eliminar">
</form>

<table>
<tr><th>CURP</th><th>Nombre</th><th>Apellido</th></tr>
<?php
$r=mysqli_query($conexion,"SELECT * FROM jovenes");
while($f=mysqli_fetch_assoc($r)){
echo "<tr><td>{$f['CURP']}</td><td>{$f['Nombre']}</td><td>{$f['Apellido']}</td></tr>";
}
?>
</table>
</div>

<!-- ================= IMC ================= -->
<div class="card">
<h2>⚖️ Registro de IMC</h2>
<form method="post">
<input type="text" name="CURP" placeholder="CURP del joven" required><br>
<input type="number" step="0.01" name="Peso" id="peso" placeholder="Peso (kg)" oninput="calcularIMC()" required><br>
<input type="number" step="0.01" name="Altura" id="altura" placeholder="Altura (m)" oninput="calcularIMC()" required><br>

<div id="imcPrevio" style="margin:10px;font-weight:bold;"></div>

<input type="submit" name="guardar_imc" value="Guardar IMC">
</form>

<table>
<tr><th>CURP</th><th>IMC</th><th>Clasificación</th><th>Fecha</th></tr>
<?php
$r2=mysqli_query($conexion,"SELECT * FROM indice_masa_corporal");
while($f=mysqli_fetch_assoc($r2)){
echo "<tr><td>{$f['CURP']}</td><td>{$f['IMC']}</td><td>{$f['Clasificacion']}</td><td>{$f['Fecha_Registro']}</td></tr>";
}
?>
</table>
</div>

</div>
</div>
</body>
</html>
