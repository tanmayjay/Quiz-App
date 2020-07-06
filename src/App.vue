<template>
  <div id="app">
    <Header />
    <QuestionBox :questions="questions" v-if="questions.length" />
  </div>
</template>

<script>
import axios from 'axios'
import Header from './components/Header'
import QuestionBox from './components/QuestionBox'

export default {
  name: 'App',
  data() {
    return {
      questions: [],
    }
  },
  components: {
    Header,
    QuestionBox,
  },
  created() {
    axios.get('https://opentdb.com/api.php?amount=20&category=18&type=multiple')
      .then(res =>  this.questions = res.data.results)
        .catch(err => console.log(err))
  },
}
</script>

<style>
body {
  background: #151515;
  color: #D0CDCD;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin: 2rem 6rem 1rem 6rem;
}
</style>
