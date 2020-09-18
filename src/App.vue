<template>
  <div>
    <button class="button is-primary" @click="adicionar">Adicionar</button>
    <table class="table is-bordered">
        <thead>
          <tr>
              <td colspan="5">
                  <input type="text" class="input" v-model="filtro" placeholder="Filtro"/>
                  <button class="button" @click="sortearPessoa">Sortear uma Pessoa</button>
              </td>
          </tr>
          <tr>
              <th>Nome</th>
              <th>Genero</th>
              <th>UF Nascimento</th>
              <th class="has-text-right">Renda</th>
              <th></th>
              </tr>
        </thead>
        <tbody>
          <tr v-for="p in pessoasFiltradas" :key="p.id">
              <td> {{p.nome}} </td>
              <td> {{p.genero}} </td>
              <td> {{p.estadoNascimentoSelecionado}} </td>
              <td class="has-text-right"> {{ formatNumber(p.renda) }}</td>
              <td>
                  <button class="button" @click="editar(p)">Editar</button>
                  <button class="button" @click="excluir(p)">Excluir</button>
              </td>
          </tr>
        </tbody>
    </table>

    <soma-generos :generos="pessoas">
    </soma-generos>  

    <cadastro-pessoa :show="showForm" :pessoa="pessoaEditar" @close="showForm = false" @save="salvar" > </cadastro-pessoa>       
  </div>
</template>

<script>
import SomaGeneros from "./components/SomaGeneros"
import CadastroPessoa from "./components/CadastroPessoa"
import FormatNumber from "./components/mixins/FormatNumber"
export default { 
  mixins: [FormatNumber],  
  components: {
    SomaGeneros, CadastroPessoa 
  },  
  name: 'app',
  data () {
    return {
      pessoas: [],
      filtro:"",      
      pessoaEditar: null,
      showForm: false,
    }
  },
  methods: { 
    adicionar() {
        this.pessoaEditar = {
            id: null,
            nome: '',
            genero: '',
            dataNascimento: null,
            estadoNascimentoSelecionado: null,
            renda: null,
            observacao:'',
        },
        this.showForm = true;
    },  

    inserir (pessoa) {
        let pessoaAdicionar = Object.assign({}, pessoa);
        pessoaAdicionar.id = this.pessoas.length + 1;
        this.pessoas.push(pessoaAdicionar);
    },

    alterar (pessoa) {
        let idx = this.pessoas.findIndex(c => {
            return c.id == pessoa.id;
        });
        if (idx > -1) {
            this.pessoas[idx] = pessoa;
            this.$set(this.pessoas, idx, this.pessoas[idx]);
        }
    },

    editar(pessoa) {
        this.pessoaEditar = Object.assign({}, pessoa);
        this.showForm = true;
    },

    excluir(pessoa) {
        if (confirm("Excluir registro?")) {
            this.pessoas.splice(this.pessoas.indexOf(pessoa), 1);
        }
    },

    salvar(pessoa) {
        if (pessoa.id == null) {
            this.inserir(pessoa);
        }
        else
        {
            this.alterar(pessoa);
        }
        this.showForm = false;
    }, 
   
    sortearPessoa: function() {
      if (this.pessoas.length > 0) {
          var pos = Math.floor(Math.random() * (this.pessoas.length));
          this.filtro = this.pessoas[pos].nome;
      }
      else
        alert("Nenhum registro cadastrado!");
    } 

  },

  computed: {
      pessoasFiltradas: function() {
        if (this.filtro == '') {
          return this.pessoas
        }
        return this.pessoas.filter(c => {
            return c.nome.startsWith(this.filtro)
        })
      },   
  }, 

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

h1, h2 {
  font-weight: normal;
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
</style>
