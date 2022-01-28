<template>
  <div>
    <div class="container p-5 mb-4 bg-light rounded-3">
      <div class="container-fluid py-5">
        <h1 class="display-5 fw-bold">Fast Typing Competition</h1>
        <p class="fs-4">Test how fast you are using the keyboard.</p>
        <div>
          True Count : {{ trueCount }}<br>
          False Count : {{ falseCount }}<br>
        </div>
        <hr class="my-4">
        <div v-if="isFinish" class="alert alert-primary">
          <h3>Time Over</h3>
          <p class="display-4">{{ dks }} DKS</p>
          <span>True word percent: {{ truePercent }}</span><br>
          <span>True Count : {{ trueCount }}</span><br>
          <span>False Count : {{ falseCount }}</span><br>
          <button @click="newGame" type="button" class="btn btn-success">New Game</button>
        </div>
        <div v-else>
          <div class="card">
            <div class="card-body">
              <span v-for="(word,key) in words.filter((item,index) => index < 20)" :key="key" class="mx-2 word" :class="key!==0 || writingWordControl">{{ word }}</span>
            </div>
          </div>

          <div class="card bg-secondary">
            <div class="card-body">
              <div class="input-group input-group-lg">
                <input type="text" class="form-control" v-model="writingWord">
                <button type="button" class="btn btn-dark">{{ timer }} sec</button>
                <button :disabled="isRunning" @click="getWords" type="button" class="btn btn-primary">Refresh</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import wordList from "@/assets/words.json"

export default {
  name: 'Main',
  data() {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 10,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList
    }
  },
  watch: {
    writingWord(newValue, oldValue) {
      if (!newValue || newValue === '') {
        this.writingWord = ''
      } else {
        if (!this.isRunning) this.toggleTimer()
        let word = this.words[0].slice(0, newValue.length).toUpperCase()
        let userWord = newValue.replace(" ", "").toUpperCase()
        this.isTrue = (word === userWord)

        if (newValue.indexOf(" ") !== -1) {
          this.isTrue ? this.trueCount++ : this.falseCount++
          this.words.splice(0, 1)
          this.writingWord = ""
          this.isTrue = true
        }
      }
    }
  },
  computed: {
    writingWordControl() {
      return this.isTrue ? "writing-word" : "writing-word bg-danger"
    },
    dks() {
      return 300 - this.words.length
    },
    truePercent() {
      let percent = (100 / this.dks)
      let val = percent * this.trueCount
      return isNaN(val) ? 0 : val
    }
  },
  mounted() {
    this.getWords()
  },
  methods: {
    newGame() {
      this.getWords()
      this.isFinish = false
      this.timer = 10
      this.isTrue = true
      this.isRunning = false
      this.trueCount = 0
      this.falseCount = 0
    },
    getWords() {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    },
    toggleTimer() {
      this.isRunning = true
      this.interval = setInterval(() => {
        if (this.timer === 0) {
          clearInterval(this.interval)
          this.isFinish = true
          return;
        }
        this.timer--
      }, 1000)
    }
  },
}
</script>

<style scoped>
.word {
  font-size: 25px;
  font-weight: 400;
}

.writing-word {
  background-color: slategrey;
  color: white;
  padding: 5px;
  border-radius: 5px;
}
</style>
