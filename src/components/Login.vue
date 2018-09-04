<template>
  <div class="hello" id="hellow">
    <h1>{{ msg }}</h1>
    <p>
      Realize o login na aplicação para visualizar a lista de funcionários.
    </p>
    <div>
      <div>
        <div>
          <input required placeholder="Matrícula" v-model="login" />
        </div>
        <div>
          <input required placeholder="Senha" type="password" v-model="pass" />
        </div>
        <button class="loginButton" type="submit" v-on:click.capture="loginApp">Login</button>
      </div>
      <p>{{error}}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'Login',
  data() {
    return {
      matricula: '',
      senha: '',
      login: '',
      pass: '',
      error: ''
    };
  },
  props: {
    msg: String
  },
  methods: {
    //Realiza o login na aplicação.
    loginApp: function() {
      axios({
        method: 'POST',
        url: 'http://localhost:3000/api/login',
        data: {
          matricula: this.login.trim(),
          senha: this.pass.trim()
        }
      })
        .then(result => {
          if (result && result.data) {
            this.matricula = result.data.data[0].matricula;
            this.senha = result.data.data[0].senha;
            localStorage.setItem('matricula', this.matricula);
            localStorage.setItem('senha', this.senha);
            this.error = 'Logado com sucesso!';
            window.location.reload();
          } else {
            this.error = 'Login inválido';
          }
        })
        .catch(() => (this.error = 'Erro ao logar.'));
    }
  }
};
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.loginButton {
  margin-top: 10px;
  background-color: #42b983;
  color: white;
  width: 100px;
  font-size: 1.5em;
}

#form {
  border-width: 10em;
  border-color: darkslategrey;
  border-bottom-color: #42b983;
  padding: 1em;
}
</style>
