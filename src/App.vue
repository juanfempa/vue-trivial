<script setup>
// const OPENAI_API_KEY = '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'

import { ref } from 'vue';

// [
//   'Animals',
//   'Any Category',
//   'Art',
//   'Celebrities',
//   'Entertainment: Board Games',
//   'Entertainment: Books',
//   'Entertainment: Cartoon & Animations',
//   'Entertainment: Comics',
//   'Entertainment: Film',
//   'Entertainment: Japanese Anime & Manga',
//   'Entertainment: Music',
//   'Entertainment: Musicals & Theatres',
//   'Entertainment: Television',
//   'Entertainment: Video Games',
//   'General Knowledge',
//   'Geography',
//   'History',
//   'Mythology',
//   'Politics',
//   'Science & Nature',
//   'Science: Computers',
//   'Science: Gadgets',
//   'Science: Mathematics',
//   'Sports',
//   'Vehicles',
// ].map((categoria) => {
//   console.log(categoria.split(' ')[0].replace(':', '').toLowerCase())
// })

const triviaApi = 'https://opentdb.com/api.php?amount=1&type=multiple'
const acierto = ref('false');
const categoria = ref('any');
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
      categoria.value = p.category.split(' ')[0].replace(':', '').toLowerCase()
      correctAnswer.value = p.correct_answer
      shuffledArr()
    })
}

getPregunta()
</script>

<template>
  <div class="flex justify-center">
    <div class="card m-8 w-11/12 bg-base-100 shadow-xl">
      <div class="card-body">
        <h2 class="card-title" v-html="pregunta"></h2>
        <h3>Categoría: {{ categoria + '.jpg' }}</h3>
        <ul>
          <li v-for="respuesta in array" :key="respuesta" @click="respuestaCorrecta(respuesta)">
            <div class="cursor-pointer" :class="salidaClase()" v-html="respuesta"></div>
          </li>
        </ul>

        <div class="card-actions justify-around">
          <button class="btn btn-primary" @click="shuffledArr">Mezclar</button>
          <button class="btn btn-success" @click="getPregunta">Nueva</button>
        </div>
      </div>
    </div>
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