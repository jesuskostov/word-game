<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div class="letters-wrapper">
      <p v-for="(letter, i) in letters" :key="i">{{ letter }}</p>
    </div>
    <input type="text" v-model="text" placeholder="Insert one word">
    <div>
      <button @click="submit">Submit</button>
      <br><br>
      <button @click="makeGame">Refresh</button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
// import { words } from '../store/words'

export default {
  name: 'Home',
  data() {
    return {
      currentWord: '',
      word: 'RANDOM',
      letters: [],
      text: ''
    }
  },
  methods: {
    makeGame() {
      let letters = this.word.split('')
      
      let max = letters.length - 1
      let min = 0
      var count = 0
      var letterForRemove = []
      let rotate = setInterval(() => {
        if (count === 3) {
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
          letters[i] = '?'
          this.letters = letters
        })
      }, 10)

    },
    submit() {
      this.word = this.text.toUpperCase()
      this.text = ''
      this.makeGame()
    },
  },
  mounted() {
    this.makeGame()
  }
}
</script>

<style lang="scss" scoped>
.letters-wrapper {
  display: flex;
  justify-content: center;
  gap: 30px;
  p {
    background-color: #000;
    border-radius: 12px;
    min-width: 100px;
    height: 100px;
    font-size: 80px;
    color: #fff;
    font-weight: bold;
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