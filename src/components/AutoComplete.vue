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
    <button class='btn' @click='updataData'>commit</button>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    words: Array,
    sentences: Array
  },

  data () {
    return {
      word: '',
      findwords: [],
      findSentences: []
    }
  },

  methods: {
    startWith () {
      var len = this.word.length;
      var tmpArr = [];
      for (let i=0; i<this.words.length; i++) {
        tmpArr[i] = this.words[i].substr(0, len)
      }

      this.findwords = []
      for(let i=0; i<tmpArr.length; i++) {
        if (tmpArr[i] === this.word) {
          this.findwords.push(this.words[i])
        }
      }
      this.handleFind(this.findwords);
      this.matchSentence()
      //console.log(this.findwords)
    },

    matchSentence () {
      // 输入的字符是以任意字母开头且有空格时，开始匹配句子
      var regex = /^[a-zA-Z]+\s/  
      if (regex.test(this.word)) {
        var words = this.word;
        this.findSentences = []
        for (let i=0; i<this.sentences.length; i++) {
          if (this.sentences[i].indexOf(words) !== -1) {
            this.findSentences.push(this.sentences[i])
          }
        }
        //console.log(this.findSentences)
        this.handleFind(this.findSentences)
      }
    },

    handleTab (e) {
      e.preventDefault()
      var regex = /^[a-zA-Z]+\s/
      if (this.findSentences[0] && regex.test(this.word)) {
        this.word = this.findSentences[0] + ' '
      } else if (this.findwords[0]) {
        this.word = this.findwords[0]+' '
      }
      this.matchSentence()
    },

    updataData () {
      this.$emit('updateData', this.word)
      this.word = ''
    },

    handleFind(arr) {
      var msg = document.getElementsByClassName('msg')[0]
      arr = arr.sort((a,b)=>a.length-b.length)
      // console.log(arr)
      if (arr.length) {
        msg.value = arr[0]
      } else {
        msg.value = ''
      }
      if (this.word === '') {
        msg.value = ''
      }
    }
  },
  watch: {
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
