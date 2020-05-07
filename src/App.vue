<template>
  <div id="app">
    <AutoComplete 
      @updateData='updateData' 
      :words='words' 
      :sentences='sentences'
    ></AutoComplete>
  </div>
</template>

<script>
import AutoComplete from './components/AutoComplete.vue'

export default {
  name: 'App',
  components: {
    AutoComplete
  },
  data () {
    return {
      words: [],
      sentences: []
    }
  },

  methods: {
    // 真实项目时可以通过接口获取数据
    getWords () {
      this.words = ['apple', 'and', 'tel', 'tomorrow', 'today', 'call', 'phone', 'open', 'age']
    },
    getSentences () {
      this.sentences = ['apple is red', 'apple is', 'call me', 'open the box', 'today is sunday']
    },
    updateData (msg) {
      var regex = /^[a-zA-Z]+\s/
      if (regex.test(msg.trim())) {
        // msg 是句子时更新sentences
        if (this.sentences.indexOf(msg.trim()) === -1) {
          this.sentences.push(msg.trim())
        }
      } else if (this.words.indexOf(msg.trim()) === -1) {
        this.words.push(msg.trim())
      }
      // console.log(this.words)
      // console.log(this.sentences)
    }
  },

  mounted () {
    this.getWords()
    this.getSentences()
  }

}
</script>

<style>
#app {
}
</style>
