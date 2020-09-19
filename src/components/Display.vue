<template>
  <div id="type-display">
    <span v-for="word in words" :ref="word.id" :key="word.id">
      <span v-for="char in word.chars" :ref="char.id" :key="char.id">{{ char.char }}</span>
    </span>
  </div>
</template>

<script>
export default {
  name: "Display",
  props: {
    textToType: String,
    highlight: {
      type: Boolean,
      default: false
    },
    underlinedChar: Boolean,
    invertedChar: Boolean,
    blinkChar: Boolean,
    wordUpdate: Object,
    charUpdate: Object
  },
  computed: {
    words() {
      return this.textToType.split(" ").map((word, widx) => {
        return {
          id: `word-${widx}`,
          chars: (word + " ").split("").map((char, chidx) => {
            return {
              char: char,
              id: `char-${widx}-${chidx}`,
              word: widx
            };
          })
        };
      });
    }
  },
  methods: {
    addClassToRef(refId, ...classes) {
      this.$refs[refId][0].classList.add(...classes);
    },
    removeClassFromRef(refId, ...classes) {
      this.$refs[refId][0].classList.remove(...classes);
    },
    changeWordStyles(removeWordCount, addWordCount, params, ...classes) {
      if (params.remove)
        this.removeClassFromRef(`word-${removeWordCount}`, ...classes);
      if (params.add) this.addClassToRef(`word-${addWordCount}`, ...classes);
    },
    changeCharStyles(
      removeWordCount,
      removeOldChar,
      addWordCount,
      addNewChar,
      params,
      ...classes
    ) {
      if (params.remove)
        this.removeClassFromRef(
          `char-${removeWordCount}-${removeOldChar}`,
          ...classes
        );
      if (params.add)
        this.addClassToRef(`char-${addWordCount}-${addNewChar}`, ...classes);
    }
  },
  watch: {
    wordUpdate: function() {
      const { wordCount, newWordCount, params } = this.wordUpdate;

      if (this.highlight) {
        this.changeWordStyles(
          wordCount,
          newWordCount,
          params,
          "highlight-word"
        );

        // this.changeCharStyles(
        //   wordCount - 1,
        //   oldChar,
        //   wordCount,
        //   newChar,
        //   params,
        //   "underline-char"
        // );
      }
    },
    charUpdate: function() {
      const {
        wordCount,
        oldChar,
        newWordCount,
        newChar,
        params
      } = this.charUpdate;

      let charStyle = [
        this.underlinedChar
          ? "underline-char"
          : this.invertedChar
          ? "inverted-char"
          : "",
        this.blinkChar ? "blink" : ""
      ];

      charStyle = charStyle.filter(className => className !== "");

      console.log('charStyle = ', charStyle);


    //   if (this.highlight) {
    //   if (charStyle[0] + charStyle[1] !== "") {
      if (charStyle.length > 0) {
        this.changeCharStyles(
          wordCount,
          oldChar,
          newWordCount,
          newChar,
          params,
          ...charStyle
        );
      }
    }
  }
};
</script>

<style scoped>
#type-display {
  width: 48%;
  /* border: 1px black solid; */

  font-size: 20px;
  font-family: "Verdana";
  text-align: justify;

  margin: 5px 0;
  padding: 10px;
}

span.highlight-word {
  transition: 0.5s;
}

.highlight-word {
  background-color: yellow;
  border-radius: 7px;
}

.underline-char {
  text-decoration: underline;
}

.blink.underline-char {
  animation: 1s underline-blink step-end infinite;
}

.inverted-char {
  color: white;
  background-color: black;
  border-radius: 7px;
}

.blink.inverted-char {
  animation: 1s char-blink step-end infinite;
}

@keyframes underline-blink {
  from,
  to {
    text-decoration: underline;
  }
  50% {
    text-decoration: none;
  }
}

@keyframes char-blink {
  from,
  to {
    color: white;
    background-color: black;
  }
  50% {
    color: black;
    background-color: transparent;
  }
}
</style>