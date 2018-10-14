<template>
  <div id="app">

    <div>
      <el-row>
        <el-col :span="5" style="position: relative; height: 100px">
          <h1 id="main-title"><span id="overline">W</span>ordMak<span id="underline">e</span>r</h1>
        </el-col>
      </el-row>
      <div id="main">
        <div id="generate-word-section">
          <el-row id="displayed-word" type="flex" justify="center">
              <el-col :span="20" style="text-align: center">
                <span style="font-size: 6em;">{{displayedWord}}</span>
              </el-col>
          </el-row>
          <br>  
          <el-row type="flex" justify="space-around" class="top-buttons">
            <el-col :offset="10" :span="6">
              <el-button @click="generateWord" type="primary">
                Generate word
              </el-button>
            </el-col>
            <el-col :span="2">
              <el-button @click="goBack">
                <i class='el-icon-d-arrow-left'></i>
              </el-button>
            </el-col>
            <el-col :span="6">
              <el-button @click="saveWord" type='success'>
                Save
              </el-button>
            </el-col>
          </el-row>
          <br>
          <br><br><br>
        </div>  
        <div id="params-section">
            <el-card>
              <el-row>
              <el-col :span="24">
                <span>The word must have
                  <el-input-number id="letters-quantity" v-model='lettersQuantity' controls-position="right" :min="2"></el-input-number>
                   letters
                </span>
              </el-col>
            </el-row>
            </el-card>
            <br>
            <el-switch
              v-model="randomOrLetters"
              active-text="Completely random"
              inactive-text="With specific letters inside">
            </el-switch>
            <br>
            <br>
            <el-card v-show="randomOrLetters === false">
              <el-row>
                <el-col :span="24">
                  <span>
                    Letters wanted :
                  </span>
                  <el-input v-model='letters' style="display: inline-block; width: 30%">
                  </el-input>
                </el-col>
              </el-row>
              <br>
              <el-row id="conditions" type="flex">
                <el-col :span="8">
                  <el-checkbox v-model="position.anywhere" label="Anywhere" @change="exclusivePositionAnywhere">
                    </el-checkbox>
                </el-col>
                <el-col :span="8">
                  <el-checkbox v-model="position.start" label="At first place" @change="exclusivePositionStart">
                    </el-checkbox>
                </el-col>
                <el-col :span="8">
                    <el-checkbox v-model="position.end" label="In the end" @change="exclusivePositionEnd">
                    </el-checkbox>
                </el-col>
              </el-row>
            </el-card>
            <br><br><br><br>
        </div>

        <el-row>
            <el-col class="voyel-percentage-label">
              <span>
                Voyel Percentage : 
              </span>
            </el-col>
            <el-col id="sliderman">
              <div class="newDiv">
                              <el-slider :show-tooltip="false" v-model="minimumVoyelRate" :min="100" :max="800" ></el-slider>

              </div>
            </el-col>
          </el-row>

        <div id="saved-words-section">
          <el-row>
            <el-col :span="6">
              <span>
                Saved words : 
              </span>
            </el-col>
            <el-col>
              <span style="font-size: 3em;">{{savedWords}}</span>
            </el-col>
          </el-row>
          <br>
          <el-row>
            <el-col>
              <el-button :class="{ grey: noSavedWords }" @click="deleteSavedWords" icon="el-icon-delete">
                Delete saved words
              </el-button>
            </el-col>
          </el-row>
          <br>
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
  created() {
    console.log(`%c ${this.minimumVoyelRate}`, 'color: blue')
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
      this.savedWords += " " + this.displayedWord;
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



