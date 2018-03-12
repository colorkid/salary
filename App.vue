<template>
  <div id="app">
    <SalaryInfo/>
  </div>
</template>

<script>
import SalaryInfo from './components/SalaryInfo'

export default {
  name: 'App',
  components: {
    SalaryInfo
  }
}
</script>

<style>
#app {
  text-align: center;
  margin-top: 60px;
  padding: 0 15px;
}
</style>
