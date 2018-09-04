<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <div v-if="!logged" ><Login msg="Bem vindo ao sistema de funcionários desenvolvido em Vue.js"/></div>  
    <div v-else ><WorkersList msg="Lista de funcionarios" /></div>
  </div>
</template>

<script>
import Login from './components/Login.vue';
import WorkersList from './components/WorkersList.vue';
import axios from 'axios';

export default {
  name: 'app',
  components: {
    Login,
    WorkersList
  },
  data() {
    return {
      logged: false,
      error: ''
    };
  },
  mounted() {
    const matricula = localStorage.getItem('matricula');
    const senha = localStorage.getItem('senha');
    if (matricula && senha) {
      axios({
        method: 'POST',
        url: 'http://localhost:3000/api/login',
        data: {
          matricula,
          senha
        }
      })
        .then(result => {
          if (result && result.data) {
            this.logged = true;
          }
        })
        .catch(err => (this.error = err));
    }
  }
};
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
