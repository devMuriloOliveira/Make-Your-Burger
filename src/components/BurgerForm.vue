<template>
  <Message :msg="msg" v-show="msg"/>
  <div>
    <form id="burger-form" @submit.prevent="createBurger">
      <div class="input-container">
        <label for="nome">Nome do cliente:</label>
        <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
      </div>
      <div class="input-container">
        <label for="pao">Escolha o pão:</label>
        <select name="pao" id="pao" v-model="pao">
          <option value="">Selecione o seu pão</option>
          <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
        </select>
      </div>
      <div class="input-container">
        <label for="carne">Escolha a carne do seu Burger:</label>
        <select name="carne" id="carne" v-model="carne">
          <option value="">Selecione o tipo de carne</option>
          <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
        </select>
      </div>
      <div id="opcionais-container" class="input-container">
        <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
        <div class="checkbox-container" v-for="opcional in opcionaisdata" :key="opcional.id">
          <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
          <span>{{ opcional.tipo }}</span>
        </div>
      </div>
      <div class="input-container">
        <input class="submit-btn" type="submit" value="Criar meu Burger!">
      </div>
    </form>
  </div>
</template>

<script>
import Message from './Message.vue';
export default {
  components: { 
    Message 
    },
    name: 'BurgerForm',

    data(){
        return{
            paes: null,
            carnes: null,
            opcionaisdata: null,
            nome: null,
            pao: null,
            carne: null,
            opcionais: [],
            msg: null
        }
    },
    methods:{
        async getIngredients(){
            const req = await fetch('http://localhost:3000/ingredientes');
            const data = await req.json();
            this.paes = data.paes;
            this.carnes = data.carnes;
            this.opcionaisdata = data.opcionais;
        },  
        async createBurger() {
          // Buscar todos os pedidos
          const pedidosReq = await fetch('http://localhost:3000/burgers');
          const pedidos = await pedidosReq.json();

          // Verifica se há pedidos e calcula o último ID corretamente
          let lastId = 0;
          if (pedidos.length > 0) {
            const ids = pedidos
              .map(p => Number(p.id))     // Garante que o id seja número
              .filter(id => !isNaN(id));  // Tira qualquer valor inválido
            lastId = ids.length > 0 ? Math.max(...ids) : 0;
          }

          // Montar o novo pedido com ID sequencial
          const novoPedido = {
            id: lastId + 1,
            nome: this.nome,
            carne: this.carne,
            pao: this.pao,
            opcionais: Array.from(this.opcionais),
            status: "Solicitado"
          };

          // Enviar o novo pedido
          await fetch('http://localhost:3000/burgers', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(novoPedido)
          });

          // Usar o ID que você já sabe
          this.msg = `Pedido nº ${novoPedido.id} foi realizado com sucesso!`;

          setTimeout(() => {
            this.msg = null;
          }, 3000);

          this.nome = "";
          this.pao = "";
          this.carne = "";
          this.opcionais = [];
        }

    },
    mounted(){
        this.getIngredients();
    },
}
</script>

<style scoped>
  /* Estilo do formulário */
  #burger-form {
    max-width: 400px;
    margin: 0 auto;
  }
  
  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;
    padding: 5px 10px;
    border-left: 4px solid #fcba03;
  }

  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #opcionais-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%;
    margin-bottom: 20px;
  }

  .checkbox-container span,
  .checkbox-container input {
    width: auto;
  }

  .checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
  }

  .submit-btn {
    background-color: #222;
    color:#fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>
