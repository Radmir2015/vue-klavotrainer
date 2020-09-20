<template>
  <div class="container">
    <Display
      highlight
      invertedChar
      blinkChar
      :textToType="textToType"
      :wordUpdate="wordMessage"
      :charUpdate="charMessage"
    />
    <MainInput
      :textToType="textToType"
      @charChanged="charHandler"
      @wordWritten="wordHandler"
      @gameOver="gameOverHandler"
      @errorTyped="errorHandler"
    />
    <p>{{ ((charCount * 60 / seconds * 4) || 0).toFixed() }} зн/мин ({{ mistakes || 0 }} ошибок)</p>
    <div>
      <p v-if="stats.length > 0">
        Всего попыток - {{ stats.length }}, средняя скорость - {{ (stats.reduce((a, b) => a + b.speed, 0) / stats.length).toFixed() }} зн/мин, среднее кол-во ошибок - {{ (stats.reduce((a, b) => a + b.mistakes, 0) / stats.length).toFixed(1) }}
      </p>
      <p v-for="stat in [...stats].map((x, i) => ({...x, id: i})).reverse()" :key="stat.id">
        Попытка № {{ stat.id + 1 }}: скорость - {{ stat.speed }} зн/мин, кол-во ошибок - {{ stat.mistakes }}
      </p>
    </div>
  </div>
</template>

<script>
import MainInput from "./MainInput.vue";
import Display from "./Display.vue";

export default {
  name: "MainUI",
  components: {
    MainInput,
    Display
  },
  data: function() {
    return {
      wordMessage: {},
      charMessage: {},

      textToType: 'Далеко-далеко за словесными горами в стране гласных и согласных живут рыбные тексты.',

      charCount: 0,
      errorLastPosition: null,
      mistakes: 0,
      seconds: 0,

      firstChar: false,
      intervalId: null,

      stats: []
    };
  },
  methods: {
    charHandler(message) {
      this.charMessage = message;
      if (message.newChar > 0) {
        this.firstChar = true
        this.charCount++
      }
    },
    wordHandler(message) {
      this.wordMessage = message;
    },
    async gameOverHandler() {
      clearInterval(this.intervalId)

      this.stats.push({
        speed: +(this.charCount * 60 / this.seconds * 4).toFixed(),
        mistakes: this.mistakes,
      })

      this.seconds = 0
      this.charCount = 0
      this.mistakes = 0
      this.errorLastPosition = null
      this.firstChar = false

      this.textToType = await this.randomQuote()
    },
    errorHandler({ errorCharCount }) {
      if (errorCharCount !== this.errorLastPosition) {
        this.mistakes++
        this.errorLastPosition = errorCharCount
      }
    },
    async randomQuote() {
      const response = await fetch('https://api.quotable.io/random')
      const data = await response.json()
      return data.content
    }
  },
  watch: {
    firstChar() {
      if (this.firstChar) {
          this.seconds = 1
          this.intervalId = setInterval(() => {
            this.seconds++
          }, 250)
      } else {
        this.seconds = 0
      }
    }
  },
  async created() {
    this.textToType = await this.randomQuote()
  //   this.$on("gameOver", () => {
  //     this.seconds = 0
  //     this.charCount = 0
  //     this.firstChar = false
  //     clearInterval(this.intervalId)
  //   })
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
