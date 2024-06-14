<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Calculadora Estadística</title>
<style>
  /* Estilos base */
  body { font-family: 'Press Start 2P', cursive; background: linear-gradient(rgba(5,7,12,0.85), rgba(5,7,12,0.85)), url(https://img.freepik.com/foto-gratis/pizarra-inscrita-formulas-calculos-cientificos_1150-19413.jpg?w=740&t=st=1718382304~exp=1718382904~hmac=617ecf1c5620f9ecebb253cfcd36ff6b311863fc6030494fae16b706b887d1a3); color: #ffffff; margin: 0; padding: 0; 
    background-size: cover; background-attachment: fixed; background-position: center center; background-repeat: no-repeat; 
    
    }
  .calculadora { width: 350px; margin: auto; padding: 20px; background: #000; border: 4px solid #ffffff; margin-bottom: 40px; }
  .entrada { margin-bottom: 10px; }
  input, button { width: 100%; padding: 5px; margin-top: 5px; }
  button { background: #ff00ff; border: none; color: #fff; }
  button:hover { background: #ff66ff; }
  .resultado, .pasos { background: #333; padding: 10px; margin-top: 10px; display: none; }
  .visible { display: block; }
  /* Animaciones y estilo pixel art */
  @keyframes pixelate {
    0% { transform: scale(1); }
    50% { transform: scale(0.8); }
    100% { transform: scale(1); }
  }
  
  /* Estilos para el footer */
  footer {
    width: 90%;
    margin: 20px auto 0 auto; /* Añade espacio arriba y centra el footer */
    display: flex;
    justify-content: space-between;
    padding: 10px 20px;
    background: #000;
    color: #fff;
  }
</style>
</head>
<body>
<div class="calculadora">
  <!-- Contenido de la calculadora -->
  <div id="apartado1" class="entrada">
    <h2>Calculadora de Estimación por Intervalo</h2>
    Media Muestral: <input type="number" id="mediaMuestral" required><br>
    Valor de t o z: <input type="number" id="valorTz" required><br>
    Desviación: <input type="number" id="desviacion" required><br>
    Tamaño de la Muestra: <input type="number" id="tamanoMuestra" required><br>
    <button class="boton" onclick="calcularIntervalo()">Calcular</button>
    <div class="resultado" id="resultadoIntervalo"></div>
    <button class="toggle-pasos" onclick="toggleVisibility('pasosIntervalo', this)">Ver Pasos</button>
    <div class="pasos" id="pasosIntervalo"></div>
    <button onclick="vaciarCampos('apartado1')">Vaciar campos</button>
  </div>
  <div id="apartado2" class="entrada">
    <h2>Calculadora de Estimación Puntual</h2>
    Datos (separados por comas): <input type="text" id="datosMuestra"><br>
    <button class="boton" onclick="calcularEstimacion()">Calcular</button>
    <div class="resultado" id="resultadoEstimacion"></div>
    <button class="toggle-pasos" onclick="toggleVisibility('pasosEstimacion', this)">Ver Pasos</button>
    <div class="pasos" id="pasosEstimacion"></div>
    <button onclick="vaciarCampos('apartado2')">Vaciar campos</button>
  </div>
</div>
<footer>
  <div>Estudiantes: William Castro V - 30.193.872 y Glasmir Herrera V - 29.979.359</div>
  <div>Profesor: Ing. Oswaldo Centeno</div>
</footer>
<script>
  // Funciones para manejar los desplegables y vaciar campos
  function toggleVisibility(id, button) {
    var element = document.getElementById(id);
    var buttonText = button.textContent || button.innerText;
    element.classList.toggle('visible');
    button.textContent = buttonText === 'Ver Pasos' ? 'Ocultar Pasos' : 'Ver Pasos';
  }

  function vaciarCampos(apartado) {
    var inputs = document.getElementById(apartado).getElementsByTagName('input');
    for (var i = 0; i < inputs.length; i++) {
      inputs[i].value = '';
    }
    var resultado = document.getElementById('resultado' + apartado.charAt(apartado.length - 1));
    var pasos = document.getElementById('pasos' + apartado.charAt(apartado.length - 1));
    resultado.innerHTML = '';
    pasos.innerHTML = '';
    resultado.classList.remove('visible');
    pasos.classList.remove('visible');
    var buttonPasos = document.querySelector('#' + apartado + ' .toggle-pasos');
    if (buttonPasos) {
      buttonPasos.textContent = 'Ver Pasos';
    }
  }

  // Funciones de cálculo con pasos desplegables
  function calcularIntervalo() {
    var media = parseFloat(document.getElementById('mediaMuestral').value);
    var tz = parseFloat(document.getElementById('valorTz').value);
    var desv = parseFloat(document.getElementById('desviacion').value);
    var tamano = parseFloat(document.getElementById('tamanoMuestra').value);
    var raizTamano = Math.sqrt(tamano);
    var errorEstandar = desv / raizTamano;
    var margenError = tz * errorEstandar;
    var limiteInferior = media - margenError;
    var limiteSuperior = media + margenError;
    document.getElementById('resultadoIntervalo').innerHTML = 'Punto Inferior del rango: ' + limiteInferior.toFixed(2) + '<br>Punto Superior del rango: ' + limiteSuperior.toFixed(2);
    document.getElementById('resultadoIntervalo').classList.add('visible');
    document.getElementById('pasosIntervalo').classList.remove('visible');
    var pasosHTML = 'Paso 1: Raíz del tamaño de la muestra = ' + raizTamano.toFixed(2) + '<br>' +
                    'Paso 2: Desviación / Raíz del tamaño de la muestra = ' + errorEstandar.toFixed(2) + '<br>' +
                    'Paso 3: t o z * (Desviación / Raíz del tamaño de la muestra) = ' + margenError.toFixed(2) + '<br>' +
                    'Paso 4: Media - Margen de error = ' + limiteInferior.toFixed(2) + '<br>' +
                    'Paso 5: Media + Margen de error = ' + limiteSuperior.toFixed(2);
    document.getElementById('pasosIntervalo').innerHTML = pasosHTML;
  }

  function calcularEstimacion() {
    var datos = document.getElementById('datosMuestra').value.split(',').map(Number);
    var tamano = datos.length;
    var sumaDatos = datos.reduce((a, b) => a + b, 0);
    var mediaMuestral = sumaDatos / tamano;
    var varianza = datos.reduce((a, b) => a + Math.pow(b - mediaMuestral, 2), 0) / (tamano - 1);
    var desviacionEstandar = Math.sqrt(varianza);
    document.getElementById('resultadoEstimacion').innerHTML = 'Media Muestral: ' + mediaMuestral.toFixed(2) + '<br>Varianza: ' + varianza.toFixed(2) + '<br>Desviación Estándar: ' + desviacionEstandar.toFixed(2);
    document.getElementById('resultadoEstimacion').classList.add('visible');
    document.getElementById('pasosEstimacion').classList.remove('visible');
    var pasosHTML = 'Paso 1: Tamaño de la muestra = ' + tamano + '<br>' +
                    'Paso 2: Suma de los datos = ' + sumaDatos.toFixed(2) + '<br>' +
                    'Paso 3: Media Muestral (suma de los datos/Tamaño de la muestra) = ' + mediaMuestral.toFixed(2) + '<br>' +
                    'Paso 4: Varianza (restar la media a cada dato y elevarlo al cuadrado, luego sumar los resultados y dividir entre tamaño de la muestra menos 1) = ' + varianza.toFixed(2) + '<br>' +
                    'Paso 5: Desviación Estándar (sacar la raíz cuadrada de la varianza)= ' + desviacionEstandar.toFixed(2);
    document.getElementById('pasosEstimacion').innerHTML = pasosHTML;
  }
</script>
</body>
</html>
