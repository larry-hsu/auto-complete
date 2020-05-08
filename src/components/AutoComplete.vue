<template>
  <div>
    <input 
      class='inp' 
      v-model='word' 
      type='text' 
      @input='startWith'
      @keydown.tab='handleTab'
    >
    <input type='text' class='msg'>
    <button class='btn' @click='updateData'>commit</button>
  </div>
</template>

<script>
export default {
  name: 'AutoComplete',
  props: {
    wordList: Array
  },

  data () {
    return {
      word: '',
      findwords: ''
    }
  },

  methods: {
    startWith () {
      var len = this.word.length
      var tmpArr = []
      // 开头都匹配后
      for (let i=0; i<this.wordList.length; i++) {
        tmpArr[i] = this.wordList[i].word.substr(0, len)
      }

      var findObj = []
      for(let i=0; i<tmpArr.length; i++) {
        if (tmpArr[i] === this.word) {
          findObj.push(this.wordList[i])
        }
      }
      this.handleFind(findObj)
      this.matchSentence()
    },

    matchSentence () {
      // 输入的字符是以任意字母开头且有空格时，开始匹配句子
      // var regex = /^[a-zA-Z]+\s/
      // 取得句子的最后一个单词，这个单词还可能正在输入中
      var lastWord = this.word.trim().split(' ')
          .pop().trim()

      if (this.word.slice(-1) === ' ') {
        var tmpArr = []
        for (let i=0; i<this.wordList.length; i++) {
          if (lastWord === this.wordList[i].word) {
            tmpArr = this.wordList[i].nextword
          } 
        } 
        if (tmpArr.length) {
          this.handleFind(tmpArr)
        }
      } else {
        var len = lastWord.length
        var tmpArr1 = []
        // 开头都匹配后
        for (let i=0; i<this.wordList.length; i++) {
          tmpArr1[i] = this.wordList[i].word.substr(0, len)
        }

        var findObj = []
        for(let i=0; i<tmpArr1.length; i++) {
          if (tmpArr1[i] === lastWord) {
            findObj.push(this.wordList[i])
          }
        }
        
        if (findObj.length) {
          this.handleFind(findObj)
        }
      }
    },

    updateData () {
      this.$emit('updateData', this.word.trim())
      var msg = document.getElementsByClassName('msg')[0]
      this.word = ''
      msg.value = ''
    },

    handleFind(arr) {
      var msg = document.getElementsByClassName('msg')[0]
      // 按照词频排序
      arr = arr.sort((a,b)=>b.often-a.often)

      if (arr.length) {
        this.findwords = arr[0].word
        // 如果当前是句子
        if (this.word.slice(-1) === ' ') {
          this.findwords = this.word.trim() + ' ' + arr[0].word
          msg.value = this.findwords
        } else {
          if (this.word.indexOf(' ') !== -1) {
            var res = this.word.trim().split(' ').slice(0, -1).join(' ')
            msg.value = res + ' ' + arr[0].word
            this.findwords = res + ' ' + arr[0].word
          } else {
            msg.value = arr[0].word
          }
        } 
      } else {
        this.findwords = ''
        msg.value = ''
      }
      if (this.word === '') {
        msg.value = ''
      }
    },

    handleTab (e) {
      e.preventDefault()

      if (this.findwords) {
        this.word = this.findwords + ' '
      }

      this.matchSentence()
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  margin: 0;
  padding: 0;
}
input.inp {
  position: absolute;
  width:200px;
  height: 30px;
  font-size:22px;
  top:0;
  left:0;
  letter-spacing: 0px;
  color:black;
  z-index:1;
  background: transparent;
}

input.msg {
  position: absolute;
  width:200px;
  height: 30px;
  font-size:22px;
  top:0;
  left:0;
  letter-spacing: 0px;
  pointer-events: none;
  color:grey;
  z-index:-1;
}

button.btn {
  position: absolute;
  top: 50px;
}
</style>
