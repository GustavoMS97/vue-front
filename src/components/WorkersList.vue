<template>
  <div class="hello" id="hellow">
    <h2>Adicionar/Editar/Excluir funcionarios</h2>
    <div class="container">
      <div class="resp-table-form">
        <div class="resp-table-body">
          <div class="resp-table-row">
            <div class="label">
              <span>CPF: </span>
            </div>
            <div class="inputText">
              <the-mask required :mask="['###.###.###-##']" placeholder="xxx.xxx.xxx-xx" v-model="cpf" />
            </div>
            <div class="label">
              <span>Data de nascimento: </span>
            </div>
            <div class="inputText">
              <the-mask required :mask="['####-##-##']" placeholder="YYYY-MM-DD" masked=true v-model="data_nascimento" />
            </div>
          </div>
          <div class="resp-table-row">
            <div class="label">
              <span>Nome: </span>
            </div>
            <div class="inputText">
              <input required placeholder="ex:Claudio Lacerda" v-model="nome" />
            </div>
            <div class="label">
              <span>Contato: </span>
            </div>
            <div class="inputText">
              <input required placeholder="ex: Email, telefone" v-model="contato" />
            </div>
          </div>
          <div class="resp-table-row">
            <div class="label">
              <span>Matricula: </span>
            </div>
            <div class="inputText">
              <input required placeholder="xxxxxxx" v-model="newmatricula" maxlength="7" />
            </div>
            <div class="label">
              <span>Chefe: </span>
            </div>
            <div class="inputText">
              <the-mask :mask="['###.###.###-##']" placeholder="xxx.xxx.xxx-xx" v-model="chefe" />
            </div>
          </div>
          <div class="resp-table-row">
            <div class="label">
              <span>Senha: </span>
            </div>
            <div class="inputText">
              <input required placeholder="Nova senha" type="password" v-model="newsenha" />
            </div>
            <div class="label">
            </div>
            <div class="inputText">
              <button class="loginButton" style="margin-left: 45%;" v-on:click="sendUser">Enviar</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <h1>{{ msg }}</h1> 
    <div class="resp-table">
      <div class="resp-table-header">
        <div class="table-header-cell">
          Cpf
        </div>
        <div class="table-header-cell">
          Nome
        </div>
        <div class="table-header-cell">
          Matricula
        </div>
        <div class="table-header-cell">
          Forma de contato
        </div>
        <div class="table-header-cell">
          Data de nascimento
        </div>
        <div class="table-header-cell">
          Nome do chefe
        </div>
      </div>
      <div class="resp-table-body">
        <div v-for="funcionario in funcionarios" v-bind:key="funcionario.cpf" class="resp-table-row">
          <div class="table-body-cell">
            {{funcionario.cpf}}
          </div>
          <div class="table-body-cell">
            {{funcionario.nome}}
          </div>
          <div class="table-body-cell">
            {{funcionario.matricula}}
          </div>
          <div class="table-body-cell">
            {{funcionario.contato}}
          </div>
          <div class="table-body-cell">
            {{new Date(funcionario.data_nascimento).toLocaleDateString("pt-BR")}}
          </div>
          <div class="table-body-cell">
            {{funcionario.nome_do_chefe}}
          </div>
        </div>
      </div>
    </div>
    <the-mask :mask="['###.###.###-##']" placeholder="xxx.xxx.xxx-xx" v-model="deleteCpf" />
    <button class="loginButton" v-on:click="deleteUser">Excluir</button>
    <button class="loginButton" v-on:click="logout" style="marginRight: 50px">Logout</button>
  </div>
</template>

<script>
import axios from 'axios';
import { TheMask } from 'vue-the-mask';
import VueSweetalert2 from 'vue-sweetalert2';
import swal from 'sweetalert2';
export default {
  components: {
    TheMask,
    VueSweetalert2
  },
  name: 'WorkersList',
  data() {
    return {
      funcionarios: [],
      cpf: '',
      deleteCpf: '',
      nome: '',
      newmatricula: '',
      newsenha: '',
      data_nascimento: '',
      contato: '',
      chefe: ''
    };
  },
  mounted() {
    axios({
      method: 'GET',
      url: 'http://localhost:3000/api/funcionarios'
    }).then(result => {
      if (result && result.data) {
        this.funcionarios = result.data.data;
      }
    });
  },
  props: {
    msg: String
  },
  methods: {
    //Metodo que realiza o logout da aplicação.
    logout: function() {
      localStorage.removeItem('matricula');
      localStorage.removeItem('senha');
      window.location.reload();
    },
    //Metodo que cria um usuario na aplicação.
    sendUser: function() {
      if (
        this.cpf &&
        this.nome &&
        this.newmatricula &&
        this.newsenha &&
        this.data_nascimento &&
        this.contato
      ) {
        var data = {
          cpf: this.cpf,
          nome: this.nome,
          matricula: this.newmatricula,
          senha: this.newsenha,
          data_nascimento: this.data_nascimento,
          contato: this.contato
        };
        if (this.chefe) {
          data = {
            ...data,
            cpf_chefia: this.chefe
          };
        }
        axios({
          method: 'POST',
          url: 'http://localhost:3000/api/funcionario',
          data
        }).then(() => {
          swal({
            type: 'success',
            title: 'Adicionado com sucesso!',
            text:
              'O Funcionário foi adicionado ao sistema e seu cadastro está vigente.'
          }).then(() => {
            window.location.reload();
          });
        });
      }
    },
    //Metodo que deleta um usuario da aplicação
    deleteUser: function() {
      if (this.deleteCpf) {
        axios({
          method: 'POST',
          url: 'http://localhost:3000/api/demitir',
          data: {
            cpf: this.deleteCpf
          }
        }).then(() => {
          swal({
            type: 'error',
            title: 'Removido com sucesso!',
            text:
              'O Funcionário foi removido do sistema e seu cadastro foi apagado.'
          }).then(() => {
            window.location.reload();
          });
        });
      }
    }
  }
};
</script>

<style scoped>
.container {
  width: 100%;
  align-content: center;
}
.resp-table {
  width: 90%;
  display: table;
  margin-left: 5%;
  margin-right: 5%;
}
.resp-table-form {
  width: 30%;
  display: table;
  margin-left: 35%;
  margin-right: 5%;
}
.resp-table-body {
  display: table-row-group;
}
.table-header-cell {
  display: table-cell;
  padding: 10px;
  text-align: justify;
  border-bottom: 1px solid black;
}
.resp-table-header {
  display: table-header-group;
  font-weight: bold;
  font-size: 25px;
}
.resp-table-row {
  display: table-row;
  border-width: 1em;
}
.table-body-cell {
  display: table-cell;
  border: solid 1px black;
}
.label {
  display: table-cell;
  text-align: right;
  width: 10%;
}
.inputText {
  display: table-cell;
  text-align: left;
  width: 10%;
}
.loginButton {
  margin-top: 10px;
  background-color: #42b983;
  color: white;
  font-size: 1.2em;
  margin: 1em;
}
input {
  margin: 10px;
}
</style>
