<template>
  <div id="app">
    <SeriesStat/>
  </div>
</template>

<script>
import SeriesStat from './components/SeriesStat'

export default {
  name: 'app',
  components: {
    SeriesStat
  },
  data(){
    return{
    }
  }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
