<template>
  <div id="app">
    <AutoComplete 
    @updateData='updateData' 
    :wordList='wordList'
    >
    </AutoComplete>
  </div>
</template>

<script>
import AutoComplete from './components/AutoComplete.vue'
import WordsJson from './assets/words.json'

export default {
  name: 'App',
  components: {
    AutoComplete
  },
  data () {
    return {
      wordList: []
    }
  },

  methods: {
    // 真实项目时可以通过接口获取数据
    getWords () {
      this.wordList = WordsJson
    },

    findInWordList (msg) {
      var idx = -1
      for (let i=0; i<this.wordList.length; i++) {
        if (msg === this.wordList[i].word) {
          idx = i
        }
      }
      return idx
    },

    addSingleWord (msg) {
      var idx = this.findInWordList(msg)
      // 如果没有则新增
      if (idx === -1) {
        this.wordList.push({
          word: msg,
          often: 1,
          nextword: []
        })
      } else {
        this.wordList[idx].often++
      }
    },

    updateData (msg) {
      // msg中没有空格说明是单词
      if (msg.indexOf(' ') === -1) {
        this.addSingleWord(msg)
      } else {
        // msg中有空格说明是句子
        var arr = msg.split(' ').map(item=>item.trim())

        // 处理句子中的前n-1个单词
        for (let i=0; i<arr.length-1; i++) {
          var idx = this.findInWordList(arr[i])

          // 如果没有则新增
          if (idx === -1) {
            // 连它的下一个单词一起新增了
            this.wordList.push({
              word: arr[i],
              often: 1,
              nextword: [{
                word: arr[i+1],
                often:1
              }]
            })
          } else if (idx !== -1){
            // 如果存在这个单词，则使用下一个元素查找nextword里面的元素
            var idx1 = -1
            var len = this.wordList[idx].nextword.length

            for (let j=0; j<len; j++) {
              if (arr[i+1] === this.wordList[idx].nextword[j].word) {
                idx1 = j
              }
            }
            // nextword里面不存在下一个元素则新增
            if (idx1 === -1) {
              this.wordList[idx].nextword.push({
                word: arr[i+1],
                often: 1
              })
            } else {
              // nextword里面存在下一个元素则频次增加
              this.wordList[idx].nextword[idx1].often++
            }
          }
        }

        // 处理最后一个元素
        var last = arr.slice(-1).toString().trim()
        this.addSingleWord(last)
      }
    }
  },

  mounted () {
    this.getWords()
  }

}
</script>
