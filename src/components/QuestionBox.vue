<template>
  <div class="question-box-container">
    <b-jumbotron header="Quiz" lead="Bootstrap v4 Components for Vue.js 2">
      <template slot="lead">{{ currentQuestion.question }}</template>
      <hr class="my-4">
      <b-list-group class="mb-3">
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        class="mr-2"
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null ||  answered"
      >Sumbit</b-button>
      <b-button class="ml-2" @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: {},
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.correctIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    selectAnswer(index) {
      console.log(index);

      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answerClass = ''
      
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      } else if (this.answered && 
      this.selectedIndex === index && 
      this.correctIndex !== index) {
        answerClass = 'incorrect'
      }

      return answerClass
    }
  }
};
</script>

<style scoped>
.list-group-item:hover {
  background: #b6b6b6;
  cursor: pointer;
  color: white;
}

.selected {
  background-color: #3385ff;
  color: white;
}

.correct {
  background-color: #5ce63a;
  color: white;
  font-weight: bold;
}

.incorrect {
  background-color: #ff5050;
  /* border: solid 2px #ff5050; */
  color: white;
  font-weight: bold;
}
</style>
