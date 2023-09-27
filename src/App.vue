<template>
  <div class="container">

    <div class="header-container" v-if="juegoAhorcado === 1">
      <header>
        <h2 class="title"> THE HANGED _ GAME </h2>
        
        
      </header>
      <button  @click="juegoAhorcado = 2">Jugar</button>
    </div>
  
    <div class="container_dificultad" v-else-if="juegoAhorcado === 2">
      <h3 class="title2">select difficulty</h3>
      <div class="container_boton">
        <button class="btn0" @click="seleccionarDificultad('Fácil')">Facil</button>
        <button class="btn0" @click="seleccionarDificultad('NORMAL')">Normal</button>
        <button class="btn0" @click="seleccionarDificultad('Difícil')">Difícil</button>
      </div>
    </div>

    <div class="container1" v-else-if="juegoAhorcado === 3">
      <h1>Seleccione la categoría que desea</h1>
      <div class="categorias">
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://designstripe-secure.imgix.net/scene-snapshots/645b2c47-9bde-40e4-8d0e-67911850ed65/1652273861842/gif?auto=compress&gif-q=60&fit=clip&h=400&s=4564819263e1ed4381b536b550149b8f?bust=1695087306883"
              alt=""> </div>
          <div class="category"> ANIMALES </div>
          <button class="btn3" @click="seleccionarCategoria('ANIMALES')">SELECT</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://designstripe-secure.imgix.net/scene-snapshots/8ac59bcd-b18a-49ba-b20b-8d02a5f46dc0/1649185969776/gif?auto=compress&gif-q=60&fit=clip&h=400&s=76181709a2ccedaee569beb5d97cc823?bust=1695087213737"
              alt=""></div>
          <div class="category"> PAISES</div>
          <button class="btn3" @click="seleccionarCategoria('PAISES')">SELECT</button>
        </div>
        
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://designstripe-secure.imgix.net/scene-snapshots/fd9e166d-13c1-4b50-ae3c-3c081b359687/1650982724999/gif?auto=compress&gif-q=60&fit=clip&h=400&s=bd4b9b25106eabdcdefb3e4c671bd23f?bust=1695087490929" alt=""></div>
          <div class="category">COLORES</div>
          <button class="btn3" @click="seleccionarCategoria('COLORES')">SELECT</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://designstripe-secure.imgix.net/scene-snapshots/41bdbf77-67f5-457e-915d-5a3987f82097/1664819226576/gif?auto=compress&gif-q=60&fit=clip&h=400&s=bbd4cca3d951903d8ff85017b53aec92?bust=1695087593003"
              alt=""></div>
          <div class="category">COSAS</div>
          <button class="btn3" @click="seleccionarCategoria('COSAS')">SELECT</button>
        </div>
       
      </div>
      <button class="btn4" @click="juegoAhorcado = 4">comenzar Juego</button>
    </div>

    <div class="container2" v-else-if="juegoAhorcado === 4">
      <div class="info">
        <h3>Nivel: {{ dificultadSelec }}</h3>
        <h3>Categoría: {{ categoriaSelec }}</h3>  
      </div>
      <img class="img2" :src="imagenError" alt="">
      <div v-if="juegoGanado">
        <p class="mensaje-ganado">¡Ganaste el juego!</p>
      </div>
      <p v-if="juegoPerdido" class="mensaje-perdido">Has perdido el juego</p>
      <h2 class="palabra_oculta">{{ mostrarPalabraOculta() }}</h2>
      <div class="container2_1">
        <button class="btn_letras" v-for="letter in alfabeto" :key="letter" @click="clickLetra(letter)"
          :disabled="usoLetras.includes(letter) || !puedeSeleccionarLetra(letter) || juegoGanado">{{ letter }}
        </button>
        <button class="btn5" @click="reiniciarJuego">🔄</button>
        
      </div>
      <button class="btn5" @click="regresarSeleccionDificultad">Volver</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const juegoAhorcado = ref(1);
const usoLetras = ref([]);
const alfabeto = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
const dificultadSelec = ref('');
const categoriaSelec = ref('');
const dificultadError = ref(0);
const imagenError = ref('');

let errorCount = ref(0);
let palabraSecreta = ref('');
let juegoPerdido = ref(false);
let juegoGanado = ref(false);
let letrasRestantes = ref(0);

const nombreCategoria = {
  ANIMALES: ["Jaguar","Tucán","Guacamaya","Anaconda","Caimán","Tortuga de río","Puma","Ocelote","Colibrí","Tigrillo","Iguana","Venado","PezDorado","Boa","Cóndor"],
  PAISES: [ "Estados Unidos",
  "Canadá",
  "México",
  "Brasil",
  "Argentina",
  "España",
  "Francia",
  "Alemania",
  "Italia",
  "Japón",
  "China",
  "India",
  "Australia",
  "Sudáfrica",
  "Rusia"],
  NOMBRES: ["Juan",
  "María",
  "Carlos",
  "Ana",
  "Luis",
  "Laura",
  "Pedro",
  "Isabel",
  "Andrés",
  "Sofía",
  "Miguel",
  "Elena",
  "Diego",
  "Valentina",
  "Daniel"],
  COLORES: [  "Rojo",
  "Azul",
  "Verde",
  "Amarillo",
  "Naranja",
  "Morado",
  "Rosa",
  "Negro",
  "Blanco",
  "Gris",
  "Marrón",
  "Cian",
  "Violeta",
  "Dorado",
  "Plateado"],
  COSAS: [ "Silla",
  "Mesa",
  "Computadora",
  "Teléfono",
  "Libro",
  "Pelota",
  "Guitarra",
  "Reloj",
  "Cuchillo",
  "Taza",
  "Lámpara",
  "Cámara",
  "Llave",
  "Plato",
  "Botella"],
};

const imagenesErrores = ref({
  'Fácil': [
    'https://i.postimg.cc/WpZxWbM2/img10.png',
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png', 
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/3NwPkK06/img6.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/qMzP0vT6/img8.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],

  'NORMAL': [
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],
  'Difícil': [
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],
});

function clickLetra(letter) {
  if (!usoLetras.value.includes(letter)) {
    usoLetras.value.push(letter);
    if (palabraSecreta.value.includes(letter)) {
      if (letrasAdivinadas()) {
        juegoGanado = true;
      }
    } else {
      letrasRestantes.value--;
      errorCount.value++;
      if (errorCount.value < dificultadError.value) {
        mostrarImagenError();
      } else {
        juegoPerdido.value = true;
      }
    }
  }
}

function letrasAdivinadas() {
  for (const letra of palabraSecreta.value) {
    if (!usoLetras.value.includes(letra)) {
      return false;
    }
  }
  return true;
}

function seleccionarDificultad(dificultad) {
  dificultadSelec.value = dificultad;
  if (dificultad === 'Fácil') {
    letrasRestantes.value = 10;
    dificultadError.value = 10;
  } else if (dificultad === 'NORMAL') {
    letrasRestantes.value = 7;
    dificultadError.value = 7;
  } else if (dificultad === 'Difícil') {
    letrasRestantes.value = 5;
    dificultadError.value = 5;
  }
  juegoAhorcado.value++; 
}

function puedeSeleccionarLetra(letter) {
  return letrasRestantes.value > 0 && !usoLetras.value.includes(letter);
}

function seleccionarCategoria(categoria) {
  categoriaSelec.value = categoria;
  palabraSecreta = ref(generarPalabraAleatoria(nombreCategoria[categoria]));
  palabraAdivinada.value = generarCadenaConGuiones(palabraSecreta.value);
}


function generarPalabraAleatoria(palabras) {
  const randomIndex = Math.floor(Math.random() * palabras.length);
  return palabras[randomIndex].toUpperCase();
}

function reiniciarJuego() {
  usoLetras.value = [];
  juegoPerdido.value = false;
  juegoGanado = false;
  letrasRestantes.value = (dificultadSelec.value === 'Fácil') ? 10 : (dificultadSelec.value === 'NORMAL') ? 7 : 5;
  palabraSecreta.value = generarPalabraAleatoria(nombreCategoria[categoriaSelec.value]);
  reiniciarImagenes();
}

function reiniciarImagenes() {
  errorCount.value = 0;
  imagenError.value = '';
}

function mostrarPalabraOculta() {
  if (juegoPerdido.value) {
    return palabraSecreta.value;
  }

  let palabraMostrada = '';
  for (const letra of palabraSecreta.value) {
    if (usoLetras.value.includes(letra)) {
      palabraMostrada += letra;
    } else {
      palabraMostrada += '_';
    }
    palabraMostrada += ' ';
  }
  return palabraMostrada.trim();
}

function regresarSeleccionDificultad() {
  juegoAhorcado.value = 2;
  reiniciarJuego()
}

function mostrarImagenError() {
  const errorIndex = errorCount.value - 1;
  if (errorIndex >= 0 && errorIndex < imagenesErrores.value[dificultadSelec.value].length) {
    imagenError.value = imagenesErrores.value[dificultadSelec.value][errorIndex];
  }
}

</script>

<style scoped>
.container {
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.title{
  font-size: 120px;
}
.title2{
  font-size: 60px;
}


.container1 {
  text-align: center;
}

.categorias {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.card {
  flex: 0 1 calc(10% - 15px);
  background: white;
  padding: .5em;
  border-radius: 6px;
  box-shadow: 0 4px 6px rgb(0, 0, 0);
  text-align: center;
}

.card-image {
  background-color: rgb(194, 23, 23);
  width: 100%;
  height: 300px;
  border-radius: 6px 6px 0 0;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.category {
  font-size: 1.7em;
  font-weight: 600;
  color: rgb(0, 0, 0);
  padding: 10px 7px 0;
}


.header-container,
.container_dificultad {
  max-width: 800px;
  padding: 20px;
  text-align: center;
}

.mensaje-ganado,
.mensaje-perdido {
  font-size: 25px;
}

.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.container_boton {
  display: flex;
  gap: 10px;
  width: 100%;
  margin-top: 20px;
}

button {
 height: 3em;
 width: 8em;
 border: none;
 border-radius: 10em;
 background: #016DD9;
 font-size: 17px;
 color: #ffffff;
 font-family: inherit;
 font-weight: 500;
}

button:hover {
 animation: shake3856 0.3s linear infinite both;
}

@keyframes shake3856 {
 0% {
  -webkit-transform: translate(0);
  transform: translate(0);
 }

 20% {
  -webkit-transform: translate(-2px, 2px);
  transform: translate(-2px, 2px);
 }

 40% {
  -webkit-transform: translate(-2px, -2px);
  transform: translate(-2px, -2px);
 }

 60% {
  -webkit-transform: translate(2px, 2px);
  transform: translate(2px, 2px);
 }

 80% {
  -webkit-transform: translate(2px, -2px);
  transform: translate(2px, -2px);
 }

 100% {
  -webkit-transform: translate(0);
  transform: translate(0);
 }
}
.btn1,
.btn2 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  margin-top: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn4 {
  background-color: #007bff;
  color: #fff;
  cursor: pointer;
  margin-top: 80px;
}

.btn5 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: 1px solid black;
  cursor: pointer;
}

.btn3 {
  padding: 10px 20px;
  background-color: #4e4d4d;
  width: 200px;
  border-radius: 10px;
  color: #fff;
  margin: 20px;
  cursor: pointer;
}

.btn3:hover {
  background:#007bff;
  color: #000000;
  font-weight: 900;
}



.container2 {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}


.container2_1 {
  display: grid;
  grid-template-columns: repeat(9, 1fr);
  gap: 10px;
  margin-bottom: 20px;
}</style>
