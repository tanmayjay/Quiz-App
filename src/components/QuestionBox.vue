<template>
  <div>
    <Question :question="questions[index]" :quesIndex="index" :total="questions.length" @next="next" @back="back" />
  </div>
</template>

<script>
import Question from './Question';

export default {
  name: 'QuestionBox',
  components: {
    Question,
  },
  data() {
    return {
      index: 0,
    }
  },
  props: {
    questions: Array
  },
  methods: {
    next() {
      this.index++
    },
    back() {
      this.index--
    },
  }
}
</script>
