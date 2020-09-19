<template>
  <div class="container">
    <!-- <p id="type-display">{{ textToType }}</p> -->
    <div id="type-display">
      <span v-for="word in words" :ref="word.id" :key="word.id">
        <span v-for="char in word.chars" :ref="char.id" :key="char.id">{{ char.char }}</span>
      </span>
    </div>
    <input
      type="text"
      name="input-field"
      id="main-input-field"
      placeholder="Start typing here..."
      autocomplete="off"
      :class="hasErrorStyles"
      @paste.prevent
      @input="onInput"
      @focus="onFocus"
      @blur="onBlur"
      v-model="typed"
    />
  </div>
</template>

<script>
export default {
  name: "MainInput",
  props: {
    textToType: String,
    highlight: {
      type: Boolean,
      default: false
    }
  },
  data: function() {
    return {
      slicedTextToType: this.textToType,
      typed: "",
      wordCount: 0,
      charCount: 0
    };
  },
  computed: {
    hasErrorStyles: function() {
      return {
        error: this.hasError()
      };
    },
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
    hasError() {
      return this.slicedTextToType.indexOf(this.typed) !== 0;
    },
    equalLength() {
      return this.slicedTextToType.length === this.typed.length;
    },
    addClassToRef(refId, ...classes) {
      this.$refs[refId][0].classList.add(...classes);
    },
    removeClassFromRef(refId, ...classes) {
      this.$refs[refId][0].classList.remove(...classes);
    },
    onGameFinish() {
      alert("Game finished!");

      //   this.$refs[`word-${this.wordCount}`][0].classList.remove(
      //     "highlight-word"
      //   );
      //   this.$refs[
      //     `char-${this.wordCount}-${this.charCount}`
      //   ][0].classList.remove("underline-char");
      this.removeClassFromRef(`word-${this.wordCount}`, "highlight-word");
      this.removeClassFromRef(
        `char-${this.wordCount}-${this.charCount}`,
        "underline-char"
      );

      this.wordCount = 0;
      this.charCount = 0;
      this.typed = "";
      this.slicedTextToType = this.textToType;
    },
    onFocus() {
      console.log(this.highlight, !!this.highlight);
      if (this.highlight) {
        // this.$refs[`word-${this.wordCount}`][0].classList.add(
        //   "highlight-word"
        // );
        // this.$refs[
        //   `char-${this.wordCount}-${this.charCount}`
        // ][0].classList.add("underline-char");
        this.addClassToRef(`word-${this.wordCount}`, "highlight-word");
        this.addClassToRef(
          `char-${this.wordCount}-${this.charCount}`,
          "underline-char"
        );
      }
    },
    onBlur() {
      if (this.highlight) {
        // this.$refs[`word-${this.wordCount}`][0].classList.remove(
        //   "highlight-word"
        // );
        this.removeClassFromRef(`word-${this.wordCount}`, "highlight-word");
      }
    },
    onInput(e) {
      //   if (e.inputType == "insertText") {
      // this.typed += e.data ? e.data : '';
      //   }
      if (!this.hasError()) {
        //   console.log(e.inputType);
        // if (e.inputType == "")
        // if (e.inputType == "insertText") this.charCount++;
        // this.charCount = this.typed.length;

        if (e.data === " ") {
          this.wordCount++;

          //   console.log("123123123", this.slicedTextToType);

          this.slicedTextToType = this.slicedTextToType
            .split(" ")
            .slice(1)
            .join(" ");
          // this.slicedTextToType = this.slicedTextToType.slice(this.slicedTextToType.split('').find(i => i === ' ') || 0);
          this.typed = "";

          //   console.log("231231232", this.slicedTextToType);

          if (this.highlight) {
            // this.$refs[`word-${this.wordCount}`][0].classList.add(
            //   "highlight-word"
            // );
            // this.$refs[`word-${this.wordCount - 1}`][0].classList.remove(
            //   "highlight-word"
            // );
            this.addClassToRef(`word-${this.wordCount}`, "highlight-word");
            this.removeClassFromRef(`word-${this.wordCount - 1}`, "highlight-word");

            // console.log(1232312, this.wordCount, this.charCount, this.$refs);

            // this.$refs[
            //   `char-${this.wordCount - 1}-${this.charCount}`
            // ][0].classList.remove("underline-char");
            // this.charCount = this.typed.length;
            // this.$refs[`char-${this.wordCount}-${0}`][0].classList.add(
                //   "underline-char"
            // );
            this.removeClassFromRef(`char-${this.wordCount - 1}-${this.charCount}`, "underline-char");
            this.charCount = this.typed.length;
            this.addClassToRef(`char-${this.wordCount}-${0}`, "underline-char");
          }
          this.charCount = 0;
        } else {
          if (this.highlight) {
            // this.$refs[
            //   `char-${this.wordCount}-${this.charCount}`
            //   //   `char-${this.wordCount}-${this.charCount - 1}`
            // ][0].classList.remove("underline-char");
            // this.charCount = this.typed.length;
            // this.$refs[
                //   `char-${this.wordCount}-${this.charCount}`
            // ][0].classList.add("underline-char");
            this.removeClassFromRef(`char-${this.wordCount}-${this.charCount}`, "underline-char");
            this.charCount = this.typed.length;
            this.addClassToRef(`char-${this.wordCount}-${this.charCount}`, "underline-char");
          }
        }

        // if (this.highlight) {
        //   console.log(this.$refs[`char-${this.wordCount}-${this.charCount}`]);
        // }
      }

      if (!this.hasError() && this.equalLength()) {
        this.onGameFinish();
      }
      console.log(this.typed, this.textToType);
      console.log(this.textToType.indexOf(this.typed));
    }
  }
};
</script>

<style scoped>
#main-input-field {
  width: 50%;
  padding: 12px 20px;

  box-sizing: border-box;
  border-radius: 15px;

  outline: none;

  font-size: 20px;
  font-family: "Verdana";

  transition: 0.5s;
}

#main-input-field:focus {
  background-color: #eee;
}

#main-input-field:focus.error {
  background-color: red;
}

#type-display {
  width: 48%;
  /* border: 1px black solid; */

  font-size: 20px;
  font-family: "Verdana";
  text-align: justify;

  margin: 5px 0;
  padding: 10px;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

span.highlight-word {
  transition: 0.5s;
}

.highlight-word {
  background-color: yellow;
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
    background-color: white;
  }
}
</style>