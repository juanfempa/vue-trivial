<script setup>
import { ref } from 'vue';

const triviaApi = 'https://opentdb.com/api.php?amount=1&type=multiple'
const acierto = ref('false');
const array = ref(['London', 'Berlin', 'Brussels', 'Paris']);
const contestada = ref(false);
const pregunta = ref('¿Aquí va la pregunta?');

const correctAnswer = ref('Paris');
const salidaClase = () => {
  return {
    error: contestada.value && !acierto.value,
    acierto: contestada.value && acierto.value,
    pendiente: !contestada.value,
  };
};
const shuffledArr = () => {
  contestada.value = false;
  for (let i = array.value.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array.value[i], array.value[j]] = [array.value[j], array.value[i]];
  }
};

const respuestaCorrecta = (respuesta) => {
  // console.log(respuesta, respuesta === correctAnswer.value);
  acierto.value = respuesta === correctAnswer.value;
  contestada.value = true;
};

const getPregunta = () => {
  fetch(triviaApi)
    .then((respuesta) => respuesta.json())
    .then((data) => {
      const p = data.results[0]
      p.incorrect_answers.push(p.correct_answer)
      array.value = p.incorrect_answers
      pregunta.value = p.question
      correctAnswer.value = p.correct_answer
      shuffledArr()
    })
}

getPregunta()
</script>

<template>
  <div class="card w-96 bg-base-100 shadow-xl">
    <div class="card-body">
      <h2 class="card-title" v-html="pregunta"></h2>
      <p>
      <ul>
        <li v-for="respuesta in array" :key="respuesta" @click="respuestaCorrecta(respuesta)">
          <div :class="salidaClase()" v-html="respuesta"></div>
        </li>
      </ul>
      </p>
      <div class="card-actions">
        <button class="btn btn-primary" @click="shuffledArr">Mezclar</button>
      </div>
    </div>
  </div>
  <div :class="{ error: true, acierto: true, pendiente: false }">
    hola
  </div>
  <div class="error acierto">
    hola
  </div>
</template>

<style>
.error {
  color: red;
}

.acierto {
  color: aquamarine;
}

.pendiente {
  color: blue;
}
</style>