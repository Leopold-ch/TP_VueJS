<template>
    <div>
      <h1>Ajouter une nouvelle question</h1>
      <form @submit.prevent="createQuestion">
        <label for="questionTitle">Titre de la question :</label>
        <input type="text" id="questionTitle" v-model="questionTitle" placeholder="Quelle est la distance d'un marathon ?" required>
        <section>
          <button class="yes" type="submit">Valider</button>
          <button class="no" @click="goBack">Abandonner</button>
        </section>
      </form>
    </div>
</template>
  
  <script>
  export default {
    data() {
      return {
        questionTitle: ''
      };
    },
    methods: {
      goBack() {
        this.$emit('goBack');
      },
      async createQuestion() {
        try {
          const response = await fetch('http://127.0.0.1:5000/quiz/api/v1.0/question', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ quiz_id: this.quizId, title: this.questionTitle })
          });
          if (!response.ok) {
            throw new Error('Failed to create new question');
          }
          this.$emit('questionCreated');
          this.questionTitle = '';
        } catch (error) {
          console.error("Erreur de création d'une nouvelle question :", error);
        }
      }
    },
    props: {
      quizId: {
        type: Number,
        required: true
      }
    }
  };
  </script>
  