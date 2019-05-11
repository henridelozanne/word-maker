<template>
  <div id="app">

    <div>
      <el-row>
        <el-col :span="5" style="position: relative; height: 100px">
          <h1 id="main-title"><span id="overline">W</span>ordMak<span id="underline">e</span>r</h1>
        </el-col>
      </el-row>

      <div class="main">
        <div class="generate-word-section">

          <el-row type="flex" justify="center" class="displayed-word-container">
              <el-col id="displayed-word" :span="24" style="text-align: center">
                <span style="font-size: 6em;">{{displayedWord}}</span>
              </el-col>
          </el-row>

          <div class="top-btns">
            <el-button @click="generateWord" type="primary">
              Generate word
            </el-button>

            <el-button @click="goBack">
              <i class='el-icon-d-arrow-left'></i>
            </el-button>

            <el-button @click="saveWord" type='success'>
              Save
            </el-button>
          </div>

        </div>

        <div class="params-section">
            <el-row type="flex" :gutter="20">
              <el-col :span="12">
                <el-card>
                  <span>The word must have
                    <el-input-number id="letters-quantity" v-model='lettersQuantity' controls-position="right" :min="2"></el-input-number>
                    letters
                  </span>
                </el-card>
              </el-col>
              <el-col :span="12">
                <el-card>
                  <div class="no-wrap">
                    <span>
                      Voyel Percentage :
                    </span>
                    <el-slider :show-tooltip="false" v-model="minimumVoyelRate" :min="100" :max="800" ></el-slider>
                  </div>
                </el-card>
              </el-col>
            </el-row>
            <el-switch
              class="specific-letters-switch"
              v-model="randomOrLetters"
              active-text="Completely random"
              inactive-text="With specific letters inside">
            </el-switch>
            <el-card v-show="randomOrLetters === false" class="letters-wanted-card">
              <el-row>
                <el-col :span="24">
                  <span>
                    Letters wanted :
                  </span>
                  <el-input v-model='letters' style="display: inline-block; width: 30%">
                  </el-input>
                </el-col>
              </el-row>

              <div class="position-checkboxes-ctn">
                <el-checkbox v-model="position.anywhere" label="Anywhere" @change="exclusivePositionAnywhere" />
                <el-checkbox v-model="position.start" label="At first place" @change="exclusivePositionStart" />
                <el-checkbox v-model="position.end" label="In the end" @change="exclusivePositionEnd" />
              </div>
            </el-card>
        </div>

        <div>
          <el-row class="saved-words-section">
            <el-col :span="6">
              <span>
                Saved words :
              </span>
            </el-col>
            <el-col class="saved-words-ctn">
              <span style="font-size: 3em;">{{savedWords}}</span>
            </el-col>
          </el-row>
          <el-row v-if="savedWords !== ''">
            <el-col>
              <el-button :class="{ grey: noSavedWords }" class="delete-btn" @click="deleteSavedWords" icon="el-icon-delete">
                Delete saved words
              </el-button>
            </el-col>
          </el-row>
        </div>
      </div>
    </div>

    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">

  </div>
</template>

<script>
export default {
  name: "App",
  data: function() {
    return {
      constants: {
        allLetters: [
        "a",
        "b",
        "c",
        "d",
        "e",
        "f",
        "g",
        "h",
        "i",
        "j",
        "k",
        "l",
        "m",
        "n",
        "o",
        "p",
        "q",
        "r",
        "s",
        "t",
        "u",
        "v",
        "w",
        "x",
        "y",
        "z"
      ],
      voyels: [ "a", "e", "i", "o", "u", "y"],
      },
      displayedWord: "",
      letters: '',
      lettersQuantity: 5,
      position: {
        anywhere: true,
        end: false,
        start: false,
      },
      savedWords: "",
      randomOrLetters: true,
      precedentWord: "",
      minimumVoyelRate: 300,
      word: "",
    };
  },
  computed: {
    noSavedWords: function(){
      return this.savedWords === "";
    }
  },
  methods: {
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
    deleteSavedWords() {
      this.savedWords = "";
    },
    exclusivePositionAnywhere() {
      this.position.start = false;
      this.position.end = false;
    },
    exclusivePositionEnd() {
      this.position.start = false;
      this.position.anywhere = false;
    },
    exclusivePositionStart() {
      this.position.end = false;
      this.position.anywhere = false;
    },
    generateWord() {
      this.precedentWord = this.displayedWord;
      this.word = [];
      let i;
      for (i = 0; i < this.lettersQuantity - this.letters.length; i += 1) {
        this.word.push(this.constants.allLetters[this.getRandomInt(0, 25)]);
      }
      if (this.position.anywhere === true){
        this.word.splice(this.getRandomInt(0, this.word.length), 0, this.letters);
      }
      if (this.position.start === true){
        this.word.unshift(this.letters);
      }
      if (this.position.end === true){
        this.word.splice(this.word.length, 0, this.letters);
      }
      // voyelsRatioCheck
      let z;
      let voyelsInWord = [];
      for (z = 0; z < this.word.length; z += 1) {
        if ( this.constants.voyels.indexOf(this.word[z]) !== -1 ) {
          voyelsInWord.push(this.word[z]);
        }
      }
      if (voyelsInWord.length / this.word.length < this.minimumVoyelRate / 1000) {
        this.generateWord();
      }
      // formatting
      let wordAsAString = "";
      let y;
      for (y = 0; y < this.word.length; y += 1) {
        wordAsAString += this.word[y];
      }
      this.displayedWord = wordAsAString.charAt(0).toUpperCase() + wordAsAString.substr(1);
    },
    goBack() {
      this.displayedWord = this.precedentWord;
    },
    saveWord() {
      if (this.displayedWord !== '') {
        this.savedWords += " " + this.displayedWord;
      }
    },
    setLettersQuantity() {
      this.lettersQuantity = this.getRandomInt(1, 6);
    },
    getRandomInt(min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
  }
};
</script>



