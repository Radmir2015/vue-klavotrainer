<template>
  <!-- <p id="type-display">{{ textToType }}</p> -->
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
</template>

<script>
export default {
  name: "MainInput",
  props: {
    textToType: String
  },
  data: function() {
    return {
      slicedTextToType: "",
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
    }
  },
  methods: {
    hasError() {
      console.log(this.slicedTextToType, this.typed)
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
      
      let tempParams = {
        wordCount: this.wordCount,
        oldChar: this.charCount - 1,
        params: { remove: true, add: false }
      };

      this.$emit("wordWritten", tempParams);
      this.$emit("charChanged", tempParams);
      this.$emit("gameOver")

      // alert("Game finished!");
      this.wordCount = 0;
      this.charCount = 0;
      this.typed = "";
      this.slicedTextToType = this.textToType;
    },
    onFocus() {
      //   console.log(this.highlight, !!this.highlight);

      // let tempParams = {
      //   wordCount: this.wordCount - 1,
      //   newChar: this.charCount,
      //   newWordCount: this.wordCount,
      //   params: { remove: false, add: true }
      // };
      // this.$emit("wordWritten", tempParams);
      // this.$emit("charChanged", tempParams);
    },
    onBlur() {
      this.$emit("wordWritten", {
        wordCount: this.wordCount,
        params: { remove: true, add: false }
      });
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
          // process whitespace
          this.wordCount++;

          this.slicedTextToType = this.slicedTextToType
            .split(" ")
            .slice(1)
            .join(" ");
          this.typed = "";

          let tempParams = {
            wordCount: this.wordCount - 1,
            oldChar: this.charCount,
            newWordCount: this.wordCount,
            newChar: 0,
            params: { remove: true, add: true }
          };

          this.$emit("wordWritten", tempParams);
          this.$emit("charChanged", tempParams);

          this.charCount = 0;
        } else {
          // any non-whitespace char
          this.$emit("charChanged", {
            wordCount: this.wordCount,
            oldChar: this.charCount,
            newWordCount: this.wordCount,
            newChar: this.typed.length,
            params: { remove: true, add: true }
          });

          this.charCount = this.typed.length;
        }
      } else {
        this.$emit("errorTyped", {
          errorCharCount: this.charCount,
        })
      }

      if (!this.hasError() && this.equalLength()) {
        this.onGameFinish();
      }
      console.log(this.typed, this.textToType);
      console.log(this.textToType.indexOf(this.typed));
    }
  },
  mounted() {
    this.slicedTextToType = this.textToType
  },
  watch: {
    textToType() {
      this.slicedTextToType = this.textToType
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
</style>