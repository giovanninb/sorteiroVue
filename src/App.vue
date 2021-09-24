<template>
  <div>
    <h1>Sorteiros vue</h1>
    <font-awesome-icon icon="sort-up" />
    <font-awesome-icon icon="sort-down" />
    <font-awesome-icon icon="sort" />
    <button @click="trocarInformacoes()">Mostrar/Esconder Pessoas</button>
    <div v-show="mostrarInformacoesComplementares">
      <h2>Informações adicionais</h2>

      <form @submit.prevent="salvarProjeto">
        <label for="nome">Nome:</label><br />
        <input
          type="text"
          id="nome"
          autofocus
          required
          v-model="projeto.nome"
        />
        <br />
        <label for="email">E-mail:</label><br />
        <input type="email" id="email" required v-model="projeto.email" />
        <br />

        <input type="submit" :value="acao" />
      </form>
        
      <table>
        <tr>
          <th  @click="ordenar('nome')" class="ordenar">Nome <font-awesome-icon :icon="getTipoOrdenacao('nome')"/></th>
          <th  @click="ordenar('email')" class="ordenar">E-mail <font-awesome-icon :icon="getTipoOrdenacao('email')"/></th>
          <th></th>
        </tr>
        <tr
          v-for="(p, idx) in projetos"
          :key="p.nome"
          :class="{ alteracao: alteracaoIdx == idx }"
        >
          <td>{{ p.nome }}</td>
          <td>{{ p.email }}</td>
          <td>
            <button @click="alterar(idx)">Alterar</button>
            <button @click="remover(p)">Remover</button>
          </td>
        </tr>

        <tr>
          <td colspan="3">Tamanho total: {{ tamanhoTotal }}</td>
        </tr>
      </table>
      <button @click="sortear()">Sortear</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      //JSON - Javascript Object Notation
      autor: "Giovanni N. Brito",
      criacao: "criando em " + new Date().toLocaleString(),
      informacao: "Posso colocar uma informação customizada",
      mostrarInformacoesComplementares: false,
      projetos: [],
      projeto: {
        nome: null,
        email: null,
      },
      alteracaoIdx: -1,
      numero: 0,
      orderField:{
        nome:null,
        type:'asc'
      }
    };
  },
  methods: {
    trocarInformacoes() {
      this.mostrarInformacoesComplementares =
        !this.mostrarInformacoesComplementares;
    },
    salvarProjeto() {
      const projetoSalvar = Object.assign({}, this.projeto);
        if (this.alteracaoIdx > -1) {
          this.projetos[this.alteracaoIdx] = projetoSalvar;
        } else {
          this.projetos.push(projetoSalvar);
        }
      this.reset();
    },
    reset() {
      this.projeto.nome = null;
      this.projeto.email = null;
      this.alteracaoIdx = -1;
    },
    remover(projetoParaRemover) {
      if (confirm("Excluir registro?")) {
        // this.projetos.splice(this.projetos.indexOf(projetoParaRemover), 1)
        this.projetos = this.projetos.filter((p) => p != projetoParaRemover);
        this.reset();
      }
    },
    sortear() {
      var total = this.projetos.map((p) => p.email).reduce((a) => a + 1, 0);
      var sorteiro = this.getRandom(total) - 1;
      console.log(sorteiro);
      this.alteracaoIdx = sorteiro;
    },
    alterar(idx) {
      this.projeto = Object.assign({}, this.projetos[idx]);
      this.alteracaoIdx = idx;
    },
    getRandom(max) {
      return Math.floor(Math.random() * max + 1);
    },
        getTipoOrdenacao(field) {
      if (field == this.orderField.nome) {
        if (this.orderField.type == 'asc')
          return 'sort-up'
        return 'sort-down'
      }
      return 'sort'
    },
        ordenar(field) {
      if (field == this.orderField.nome) {
        this.orderField.type = this.orderField.type == 'asc' ? 'desc' : 'asc'
      }
      this.orderField.nome = field
      const orderType = this.orderField.type == 'asc' ? 1 : -1
      this.projetos.sort( (a, b) => {
          if (isNaN(a[field]) || isNaN(b[field])) {
            return (a[field].localeCompare( b[field] )) * orderType
          }
          return (a[field] - b[field]) * orderType
      })
    }
  },
  computed: {
    acao() {
      return this.alteracaoIdx > -1 ? "Alterar" : "Adicionar";
    },
    tamanhoTotal() {
      return this.projetos.map((p) => p.tamanho).reduce((a) => a + 1, 0);
    },
  },
  watch: {
    alteracaoIdx: function (val) {
      console.log(val);
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.ordenar{

}

.alteracao {
  background-color: rgba(111, 73, 250, 0.562);
}
</style>
