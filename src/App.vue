<template>
  <div id="app">
    <nav>
      <div class="nav-wrapper black darken-1">
        <a href="#" class="brand-logo center">Produtos</a>
      </div>
    </nav>

    <div class="container">
      <ul>

        <li id="update" v-for="(msg, index) of messageUpdate" :key="index">
        {{msg}}
        </li>

        <li id="exito" v-for="(msg, index) of messagInput" :key="index">
        {{ msg}}
        </li>

        <li id="erros" v-for="(erro, index) of errors" :key="index">
          campo <b>{{ erro.field }}</b> - {{ erro.defaultMessage }}
        </li>
      </ul>

      <form @submit.prevent="salvar">
        <label>Nome</label>
        <input type="text" placeholder="Nome" v-model="produto.nome" />
        <label>Quantidade</label>
        <input type="number" placeholder="Quantidade" v-model="produto.quantidade" />
        <label>Valor</label>
        <input type="text" placeholder="Valor" v-model="produto.valor" />

        <button class="waves-effect waves-light btn-small">
          Salvar<i class="material-icons left">save</i>
        </button>
      </form>

      <table>
        <thead>
          <tr>
            <th>Nome</th>
            <th>Quantidade</th>
            <th>Valor</th>
            <th>Opções</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="produto of produtos" :key="produto.id">
            <td>{{ produto.nome }}</td>
            <td>{{ produto.quantidade }}</td>
            <td>{{ produto.valor }}</td>
            <td>
              <button
                @click="editar(produto)"
                class="waves-effect btn-small blue darken-1"
              >
                <i class="material-icons">create</i>
              </button>
              <button
                @click="deletar(produto)"
                class="waves-effect btn-small red darken-1"
              >
                <i class="material-icons">delete_sweep</i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import Produto from "./services/produtos";
export default {
  name: "app",
  data() {
    return {
      messageUpdate: [],
      messagInput: [],
      produto: {},
      produtos: [],
      errors: [],
    };
  },

  mounted() {
    this.listar();
  },

  methods: {
    listar() {
      Produto.listar()
        .then((res) => {
          this.produtos = res.data;
        })
        .catch((e) => {
          console.log(e);
        });
    },
    salvar() {
      if (!this.produto.id) {
        Produto.salvar(this.produto)
          .then(() => {
            this.produto = {};
            this.listar();
            this.errors = {};
            this.messagInput = ["Produto salvo com sucesso."];
            setTimeout(() => {
              this.messagInput = [];
            }, 10000);
          })
          .catch((e) => {
            this.errors = e.response.data.errors;
          });
      } else {
        Produto.atualizar(this.produto)
          .then(() => {
            this.produto = {};
            this.errors = {};
            this.listar();
            this.messageUpdate = ["Produto atualizado com sucesso."];
            setTimeout(() => {
              this.messageUpdate = [];
            }, 10000);
          })
          .catch((e) => {
            this.errors = e.response.data.errors;
          });
      }
    },
    editar(produto) {
      this.produto = produto;
    },
    deletar(produto) {
      if (confirm("Deseja excluir o produto?")) {
        Produto.deletar(produto)
          .then(() => {
            this.listar();
            this.errors = {};
          })
          .catch((e) => {
            this.errors = e.response.data.errors;
          });
      }
    },
  },
};
</script>

<style>
td > .waves-effect {
  margin-left: 15px;
}

#erros {
  color: #842029;
  border: #f5c2c7;
  margin-top: 10px;
  padding: 10px 15px 10px 15px;
  border-radius: 5px;
  background-color: #f8d7da;
}
#exito {
  color: #0b997d;
  border: none;
  margin-top: 10px;
  padding: 10px 15px 10px 15px;
  border-radius: 5px;
  background-color: rgba(85, 239, 196,0.6);
}

#update {
  color: #d6ac5d;
  border: none;
  margin-top: 10px;
  padding: 10px 15px 10px 15px;
  border-radius: 5px;
  background-color: rgba(255, 234, 167,0.7);
}


</style>