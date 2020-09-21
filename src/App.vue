<template>
  <div id="app">
    <div class="header">
      <img alt="Vue logo" src="./assets/logo.png">
      <h2>KlavoTrainer</h2>
    </div>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <MainUI />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
// import MainInput from './components/MainInput.vue'
// import Display from './components/Display.vue'
import MainUI from './components/MainUI.vue'

export default {
  name: 'app',
  components: {
    MainUI
  }
}
</script>

<style>
.header {
  display: flex;
  justify-content: center;
  align-items: center;
}

.header img {
  width: 40px;
  height: 40px;
  margin: 10px;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
