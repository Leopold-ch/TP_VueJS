<template>
  <h1>Gestionnaire de quizz</h1>
  <p>Quizz existants :</p>
  <ul>
    <li v-for="quiz in quizzes" :key="quiz.id">
      <a @click="viewQuizDetail(quiz)">{{ quiz.name }}</a>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    quizzes: {
      type: Array,
      required: true
    }
  },
  methods: {
    viewQuizDetail(quiz) {
      this.$emit('viewQuizDetail', quiz);
    }
  }
};
</script>
