<template>
  <div>
    <div id="ques-info">
      <p class="wrap-text bg-dark">Question: <span>{{ quesIndex + 1 }}</span> / <span>{{ total }}</span></p>
      <p class="wrap-text bg-dark">Category: <span>{{ question.category.split(":")[0] | ucfirst }}</span></p>
      <p class="wrap-text bg-dark">Topic: <span>{{ question.category.split(":")[1] | ucfirst }}</span></p>
      <p class="wrap-text bg-dark">Difficulty: <span>{{ question.difficulty | ucfirst }}</span></p>
      <p class="wrap-text bg-dark">Answered: <span>{{ isSubmitted.filter(val => val).length }}</span></p>
      <p class="wrap-text bg-dark">Correct: <span>{{ isCorrect.filter(val => typeof val === "number").length }}</span></p>
      <p class="wrap-text bg-dark">Incorrect: <span>{{ isWrong.filter(val => typeof val === "number").length }}</span></p>
    </div>
    <div id="question-box">
      <h2 id="question">{{ question.question }}</h2>
      <p 
      v-for="(answer, index) in getAnswers[this.quesIndex]" 
      :key="index" 
      @click.prevent="! isSubmitted[quesIndex] ? handler(index) : null" 
      :class="[{'correct' : isCorrect[quesIndex] === index}, {'incorrect' : isWrong[quesIndex] === index}, {'selected': selectedAnswers[quesIndex] === index}, {'answer-disabled' : isSubmitted[quesIndex] }, {'answer' : ! isSubmitted[quesIndex] }]" 
      >
        {{ answer }}
      </p>
    </div>
    <div class="btn-group">
      <button class="submit" @click="submit" v-bind="{'disabled': ! enable[quesIndex]}">Submit</button>
      <button class="next" @click="$emit('next')" v-bind="{'disabled': quesIndex >= total - 1}">Next</button>
      <button class="back" @click="$emit('back')" v-bind="{'disabled': quesIndex <= 0}">Previous</button>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  name: 'Question',
  data() {
    return {
      answers: [],
      correctAnswers: [],
      selectedAnswers: [],
      maxIndex: -1,
      enable: [],
      isSubmitted: [],
      isCorrect: [],
      isWrong: [],
    }
  },
  props: {
    question: Object,
    total: Number,
    quesIndex: Number,
  },
  computed: {
    getAnswers() {    
      if(this.shuffleAnswers) {
        let answers = [...this.question.incorrect_answers, this.question.correct_answer]
        answers = _.shuffle(answers)
        let correctIndex = answers.indexOf(this.question.correct_answer)
        this.$set( this.correctAnswers, this.quesIndex, correctIndex )
        this.$set( this.answers, this.quesIndex, answers )
      }
      return this.answers
    },
    shuffleAnswers() {
      if(this.quesIndex > this.maxIndex) {
        this.maxIndex = this.quesIndex
        return true
      }
      return false
    }
  },
  methods: {
    selectAnswer(index) {
      this.$set(this.selectedAnswers, this.quesIndex, index)
    },
    enableSubmit() {
      this.$set( this.enable, this.quesIndex, true )
    },
    handler(index) {
      this.selectAnswer(index)
      this.enableSubmit()
    },
    submit() {
      this.$set( this.enable, this.quesIndex, false )
      this.$set( this.isSubmitted, this.quesIndex, true )
      let selected = this.selectedAnswers[this.quesIndex]
      if(this.correctAnswers[this.quesIndex] === selected) {
        this.$set( this.isCorrect, this.quesIndex, selected )
      } else {
        this.$set( this.isWrong, this.quesIndex, selected )
      }
    },
  },
  filters: {
    ucfirst : value => (
      value[0].toUpperCase() + value.slice(1)
    )
  },
}
</script>

<style scoped>
#ques-info {
  color: #C4C4C4;
  background: #484848;
  text-align: center;
  display: flex;
  justify-content: space-between;
  padding: 3px 10px 5px 10px;
}

.wrap-text {
  padding: 5px;
  border-radius: 3px;
}

.bg-dark {
  background: black;
}

span {
  color: #FFFFFF;
}

#question-box {
  border: 1px solid white;
  background: #FFFFE5;
  color: #3E3E3E;
  text-align: left;
  padding: 0 5rem 0 5rem;
  opacity: 0.9;
}

#question {
  padding: 5px 0 8px 0;
}

.answer {
  padding: 15px;
  border-left: 10px groove #ABAAAA;
  background-color: white;
  font-size: larger;
}

.answer:hover {
  cursor: pointer;
  background-color: #C1C1C1;
  color: #002A26;
  font-weight: bold;
}

.answer-disabled {
  padding: 15px;
  border-left: 10px groove white;
  background-color: white;
  font-size: larger;
}

.answer-disabled:hover {
  cursor: not-allowed;
}

.selected {
  background-color: #B0DDD8;
  border-left: 10px groove #49C3B6;
  font-weight: bold;
}

.incorrect {
  background-color: #F86767;
  border-left: 10px groove #B12F1D;
  font-weight: bold;
}

.correct {
  background-color: #91D556;
  border-left: 10px groove #1FB909;
  font-weight: bold;
}

.btn-group {
  background-color: #FFFFE5;
  opacity: 0.9;
  padding: 5px;
}

.next {
  display: inline-block;
  border: none;
  background: #8E8E8E;
  color: #fff;
  padding: 10px 20px;
  margin: 5px;
  cursor: pointer;
  width: 10rem;
  font-size: x-large;
}

.next:disabled {
  cursor: not-allowed;
}

.next:hover {
  background: #666;
}

.back {
  display: inline-block;
  border: none;
  background: #3F8B34;
  color: #fff;
  padding: 10px 20px;
  margin: 5px;
  cursor: pointer;
  width: 10rem;
  font-size: x-large;
}

.back:disabled {
  cursor: not-allowed;
}

.back:hover {
  background: #0B5200;
}

.submit {
  display: inline-block;
  border: none;
  background: #1CA192;
  color: #fff;
  padding: 10px 20px;
  margin: 5px;
  cursor: pointer;
  width: 10rem;
  font-size: x-large;
}

.submit:disabled {
  cursor: not-allowed;
}

.submit:hover {
  background: #0D635A;
}
</style>