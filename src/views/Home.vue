<template>
  <div>
    <div v-if="!newGame && !successScreen" class="home">
      <img style="margin-bottom: 50px" alt="Vue logo" src="../assets/logo.png">
      <h5 style="font-size: 30px">Tries {{ tries }}/5</h5>
      <div class="letters-wrapper">
        <drop @dragover="currentIndex = i" @drop="handleDrop" class="drop" v-for="(letter, i) in letters" :key="i">{{ letter }}</drop>
      </div>
      <div class="letters-wrapper">      
        <drag class="drag" v-for="(letter, i) in missingLetters" :key="i" :transfer-data="letter" >{{ letter }}</drag>
      </div>
    </div>
    <div v-if="newGame">
      <h1>Dolen proval</h1>
      <button @click="generateWord">Start new game</button>
    </div>
    <div v-if="successScreen">
      <h1>Bravo</h1>
      <button @click="generateWord">Start new game</button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import { words } from '../store/words'
import axios from 'axios'

export default {
  name: 'Home',
  data() {
    return {
      currentWord: '',
      word: '',
      letters: [],
      missingLetters: [],
      currentIndex: null,
      tries: 0,
      newGame: false,
      successScreen: false
    }
  },
  methods: {
    handleDrop(data) {
      let draggedLetter = JSON.stringify(data).split('"')[1]
      let word = this.word.split('')
      if (this.tries >= 4) {
        this.newGame = true
        return
      }
      console.log(word.indexOf(draggedLetter));
      console.log('index',this.currentIndex);
      if (word.indexOf(draggedLetter) === this.currentIndex) {
        console.log(word.indexOf(draggedLetter));
        console.log(this.currentIndex);
        // Updating the correct letter
        this.letters[this.currentIndex] = draggedLetter
        // Updating the word
        let memory = this.letters
        this.letters = []
        this.letters = memory
        // Updating the letters that you have to choose
        let i = this.missingLetters.indexOf(draggedLetter)
        this.missingLetters.splice(i, 1);
        if (this.missingLetters.length === 0) {
          this.successScreen = true
        }
      } else {
        this.tries++
      }
    },
    makeGame() {
      // Resetting the letters you have to choose
      this.missingLetters = []
      let letters = this.word.split('')
      // Picking random letters from the word
      let max = letters.length - 1
      let min = 0
      var count = 0
      var letterForRemove = []
      let rotate = setInterval(() => {
        if (count === letters.length / 2) {
          clearInterval(rotate)
          return
        } 
        let i = Math.floor(Math.random() * (max - min + 1) + min)
        if (!letterForRemove.includes(i)) {
          letterForRemove.push(i)
          count++
        } 
      })
      setTimeout(() => {
        letterForRemove.map(i => {
          this.missingLetters.push(letters[i])
          letters[i] = '?'
          this.letters = letters
        })
      }, 10)

    },
    async generateWord() {
      let res = await axios.get('https://random-word-api.herokuapp.com/word?number=1')
      this.word = res.data[0].toUpperCase()
      console.log(123);
      this.makeGame()
      this.newGame = false
      this.successScreen = false
      this.tries = 0
    }
  },
  mounted() {
    this.makeGame()
    this.generateWord()
  }
}
</script>

<style lang="scss" scoped>
.letters-wrapper {
  display: flex;
  justify-content: center;
  gap: 30px;
  .drop, .drag {
    margin-bottom: 50px;
    background-color: #000;
    border-radius: 12px;
    min-width: 100px;
    height: 100px;
    font-size: 80px;
    color: #fff;
    font-weight: bold;
  }
  .drag {
    cursor: move;
  }
}

input {
  width: 50%;
  height: 60px;
  font-size: 20px;
  text-align: center;
  margin-bottom: 20px;
}
button {
  width: 30%;
  height: 60px;
  font-size: 20px;
}
</style>