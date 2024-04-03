<template>
    <NewQuestionForm v-if="showNewQuestionForm" @questionCreated="createdQuestion" @cancel="cancelAddQuestion" :quizId="selectedQuiz.id" />
    <div v-else>
      <h1>Contenu du quizz : {{ quizName }}</h1>
      <p>Questions existantes :</p>
      <ul id="everyQuestions" >
        <li v-for="question in selectedQuiz.questions" :key="question.id">
          <p>{{ question.title }}</p>
          <button @click="deleteQuestion(question)" class="no">Supprimer</button>
          <button @click="editQuestionName(question)" class="rename">Renommer</button>
        </li>
      </ul>
      <section>
        <button @click="deleteQuiz">Supprimer le quizz</button>
        <button @click="editQuizName">Renommer le quizz</button>
        <button @click="showNewQuestionForm = true">Ajouter une question</button>
        <button @click="goBack">Retour</button>
      </section>
    </div>
</template>

<script>
import NewQuestionForm from './AjoutQuestionForm.vue';

export default {
  props: {
    selectedQuiz: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      quizName: '',
      showNewQuestionForm: false
    };
  },
  methods: {
    async getQuizName() {
      try {
        const response = await fetch(`http://127.0.0.1:5000/quiz/api/v1.0/quiz/${this.selectedQuiz.id}`);
        const data = await response.json();
        this.quizName = data.name;
      } catch (error) {
        console.error('Erreur de récupération du nom du quizz :', error);
      }
    },
    deleteQuiz() {
      if (confirm("Êtes-vous certain de supprimer ce quizz ?")) {
        this.$emit('deleteQuiz', this.selectedQuiz);
      }
    },
    deleteQuestion(question) {
      if (confirm("Êtes-vous certain de supprimer cette question ?")) {
        this.$emit('deleteQuestion', question);
      }
    },
    goBack() {
      this.$emit('goBack');
    },
    async editQuizName() {
      const newQuizName = prompt("Saisissez le nouveau nom du quizz :", this.quizName);
      if (newQuizName !== null) {
        try {
          const response = await fetch(`http://127.0.0.1:5000/quiz/api/v1.0/quiz/${this.selectedQuiz.id}`, {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify({ name: newQuizName })
          });

          if (!response.ok) {
            throw new Error('Failed to update quizz name');
          }

          this.quizName = newQuizName;
        } catch (error) {
          console.error('Erreur de modification du nom du quizz :', error);
        }
      }
    },
    cancelAddQuestion() {
      this.showNewQuestionForm = false;
    },
    createdQuestion() {
      this.showNewQuestionForm = false;
      this.$emit('questionCreatedToApp', this.selectedQuiz.url);
    },
    editQuestionName(question) {
  const newQuestionTitle = prompt("Saisissez la nouvelle question :", question.title);
  if (newQuestionTitle !== null) {
    fetch(`${question.url}`, {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ title: newQuestionTitle })
    })
    .then(response => {
      if (response.ok) {
        question.title = newQuestionTitle;
      } else {
        console.error('Failed to update question title');
      }
    })
    .catch(error => {
      console.error('Error updating question title :', error);
    });
  }
}

  },
  components: {
    NewQuestionForm
  },
  mounted() {
    this.getQuizName();
  }
};
</script>


<style>

  button{
    margin: 10px;
  }

</style>