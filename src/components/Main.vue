<template>
  <div class="container">
    <div class="p-5 mb-4 trans-bg rounded-3">
      <div class="container-fluid py-5">
        <h1 class="display-2 fw-bold text-center">Hızlı Yazma Pratiği</h1>
        <p class="col fs-4 text-center">
          Ne kadar hızlı klavye kullandığını hiç merak ettin mi? Dene ve gör!<br/>
          Oyuna başlamak için kelime alanına yazmaya başlayabilirsin. Yazmaya başladığın an süre başlayacak. <br />
          Kelime yazmayı tamamladıktan sonra sonraki kelimeye geçmek için <span style="border:1px solid black; border-radius:5px;padding:1px 5px" class="me-1 bg-light">space</span>tuşuna basmayı unutma!
        </p>
        <hr/>
        <!--Kelimelerin listelenme alanı -->
        <div v-if="isFinish" class="alert alert-secondary text-center">
            <h3>Süre Bitti !</h3>
            <p class="display-4 mt-4">{{dks}} DKS</p>
            <div class="mt-3">
                <span class="ms-3">Doğruluk yüzdesi: %{{truePercent}}</span>
            <span class="ms-3">Doğru kelime sayısı: {{ trueCount }}</span>
            <span class="ms-3">Yanlış kelime sayısı: {{ falseCount }}</span>
            </div>
            <div class="d-grid gap-2 mt-5">
            <button @click="newGame" class="btn btn-success btn-lg mt-2"> YENİ OYUN</button>
            </div>
        </div>
        <!--Oyun kelime ve input alanı -->
        <div v-else>
          <div class="card">
            <div class="card-body">
              <span
                :key="key"
                v-for="(word, key) in words.filter((data, index) => index < 20)"
                v-bind:class="key != 0 || writingWordControl"
                class="words ms-2"
                >{{ word }}</span
              >
            </div>
          </div>
          <!--Kelimelerin girildiği alan -->
          <div class="card mt-3">
            <div class="card-body bg-light">
              <div class="input-group input-group-lg">
                <input type="text" class="form-control rounded-pill" v-model="writingWord" />
                <!--Timer -->
                <button class="btn btn-secondary disabled rounded-pill ms-1" type="button">
                  {{ timer }} sn.
                </button>
                <!-- Refresh -->
                <button class="btn btn-outline-dark rounded-pill ms-1" type="button" :disabled="isRunning" @click="getWords">Yenile</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import wordList from '@/assets/words.json'
export default {
  data () {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 60,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList
    }
  },
  watch: {
    writingWord (val) {
      if (!val || val === ' ') {
        this.writingWord = ''
        return
      }
      if (!this.isRunning) this.toggleTimer()
      const word = this.words[0].slice(0, val.length).toUpperCase()
      const userWord = val.replace(' ', '').toUpperCase()
      this.isTrue = word === userWord
      if (val.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writingWord = ''
        this.isTrue = true
      }
    }
  },
  computed: {
    writingWordControl () {
      return this.isTrue ? 'writing-word' : 'writing-word text-danger'
    },
    dks () {
      return 300 - this.words.length
    },
    truePercent () {
      const percent = (100 / this.dks)
      const result = (percent * this.trueCount).toFixed(2)
      return isNaN(result) ? 0 : result
    }
  },
  mounted () {
    this.getWords()
  },
  methods: {
    newGame () {
      this.getWords()
      this.isFinish = false
      this.timer = 60
      this.isTrue = true
      this.isRunning = false
      this.trueCount = 0
      this.falseCount = 0
      this.writingWord = ''
    },
    getWords () {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    },
    toggleTimer () {
      this.isRunning = true
      this.interval = setInterval(this.timeProcess, 1000)
    },
    timeProcess () {
      if (this.timer === 0) {
        clearInterval(this.interval)
        this.isFinish = true
        return
      }
      this.timer--
    }
  }
}
</script>

<style >
.words {
  font-size: 27px;
  font-weight: 400;
}
.writing-word {
  background-color: #aaaaaa;
  color: white;
  padding: 5px;
  border-radius: 5px;
}
.trans-bg{
  background-color: transparent;
}
</style>
