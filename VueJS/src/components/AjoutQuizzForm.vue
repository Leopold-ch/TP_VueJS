<template>
    <div>
      <h1>Nouveau quizz</h1>
      <form @submit.prevent="createQuiz">
        <label for="quizName">Nom du quizz :</label>
        <input type="text" id="quizName" v-model="quizName" placeholder="Quizz n°42" required>
        <section>
          <button class="yes" type="submit">Créer</button>
          <button class="no" @click="goBack">Abandonner</button>
        </section>
      </form>
    </div>
</template>
  
  <script>
  export default {
    data() {
      return {
        quizName: ''
      };
    },
    methods: {
      goBack() {
        window.location.reload();
      },
      async createQuiz() {
        try {
          const response = await fetch('http://127.0.0.1:5000/quiz/api/v1.0/quiz', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ name: this.quizName })
          });
  
          if (!response.ok) {
            throw new Error('Failed to create new quiz');
          }
          this.$emit('quizCreated');
        } catch (error) {
          console.error('Erreur lors de la création du nouveau quiz:', error);
        }
      }
    }
  };
  </script>
  