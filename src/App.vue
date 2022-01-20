<template>
  <div>
    <ScoreBoard :player="this.userScore" :cpu="this.cpuScore" />
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" :key="index">
        <input
          type="radio"
          name="options"
          :value="answer"
          :disabled="this.active"
          v-model="this.chosen_answer"
        />

        <label v-html="answer"></label><br />
      </template>

      <button
        class="send"
        type="button"
        @click="this.submitAnswer()"
        v-if="!this.active"
      >
        Confirmar
      </button>

      <section class="result" v-if="this.active">
        <h4 v-if="this.chosen_answer == this.correctAnswer">
          &#9989; Parabéns! A resposta está correta.
        </h4>

        <h4 v-else>
          &#10060; Que pena, a resposta está errada. A resposta correta é "{{
            this.correctAnswer
          }}".
        </h4>

        <button class="send" type="button" @click="newQuestions()">
          Próxima pergunta
        </button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoard from "./components/ScoreBoard.vue";

export default {
  name: "App",
  components: {
    ScoreBoard,
  },

  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosen_answer: undefined,
      active: false,
      userScore: 0,
      cpuScore: 0,
    };
  },

  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(
        Math.round(Math.random() * answers.length),
        0,
        this.correctAnswer
      );
      return answers;
    },
  },
  methods: {
    submitAnswer: function () {
      if (!this.chosen_answer) {
        alert("Pick one of the options!");
      } else {
        this.active = true;
        if (this.chosen_answer === this.correctAnswer) {
          console.log("Você acertou!");
          this.userScore++;
        } else {
          console.log("Você errou...");
          this.cpuScore++;
        }
      }
    },

    newQuestions: function () {
      this.active = false;
      this.chosen_answer = undefined;
      this.question = undefined;

      this.axios
        .get("https://opentdb.com/api.php?amount=10&category=12")
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
          console.log(response.data.results);
        });
    },
  },

  created() {
    this.newQuestions();
    console.log("Created() executado!");
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;

  input[type="radio"] {
    margin: 12px 4px;
  }

  input[type="radio" i] {
    background-color: initial;
    cursor: default;
    appearance: auto;
    box-sizing: border-box;
    margin: 3px 3px 0px 5px;
    padding: initial;
    border: initial;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
