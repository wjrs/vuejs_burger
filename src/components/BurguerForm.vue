<template>
  <div>
    <Menssage :msg="msg" v-show="msg" />

    <div>
      <form action="" id="burguer-form" @submit.prevent="createBurguer">
        <div class="input-container">
          <label for="nome">Nome do Cliente:</label>
          <input type="text" id="nome" name="name" v-model="nome">
        </div>
        <div class="input-container">
          <label for="pao">Escolha o pão:</label>
          <select name="pao" id="pao" v-model="pao">
            <option value="">Selecione o Pão</option>
            <option v-for="pao in paes" :value="pao.tipo">{{ pao.tipo}}</option>
          </select>
        </div>
        <div class="input-container">
          <label for="carne">Escolha a carne:</label>
          <select name="carne" id="carne" v-model="carne">
            <option value=""></option>
            <option v-for="carne in carnes" :value="carne.tipo">{{ carne.tipo}}</option>
          </select>
        </div>
        <div class="input-container" id="opc-container">
          <label id="opc-title" for="opc">Selecione os opcionais:</label>
          <div class="checkbox-container" v-for="opc in opcsdata">
            <input type="checkbox" name="opcs" v-model="opcs" :value="opc.tipo">
            <span>{{ opc.tipo }}</span>
          </div>
        </div>
        <div class="input-container">
          <input type="submit" class="submit-btn" value="Criar meu Burguer">
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Menssage from "@/components/Menssage";
export default {
  name: "BurguerForm",
  components: {Menssage},
  data() {
    return {
      paes: null,
      carnes: null,
      opcsdata: null,
      nome: null,
      pao: null,
      carne: null,
      opcs: [],
      msg: null,
    }
  },
  methods: {
    async getIngredientes() {
      const req = await fetch("http://localhost:3000/ingredientes");
      const data = await req.json();

      this.paes = data.paes;
      this.carnes = data.carnes;
      this.opcsdata = data.opcionais;
    },
    async createBurguer() {
      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcs),
        status: "Solicitado"
      }

      const dataJson = JSON.stringify(data);
      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: {"Content-Type":"application/json"},
        body: dataJson
      });

      const res = await req.json();

      // Retorna a mensagem com o número do pedido
      this.msg = `Pedido N ${res.id} realizado com sucesso`;

      // Remove a mensagem depois de 3s
      setTimeout(() => this.msg = "", 3000)

      // Limpa os campos após o envio do formulário
      this.nome = "";
      this.pao = "";
      this.carne = "";
      this.opcionais = "";
    }
  },
  mounted() {
    this.getIngredientes();
  }
}
</script>

<style scoped>
  #burguer-form {
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
    border-left: 4px solid #FCBA03;
  }
  input, select {
    padding: 5px 10px;
    width: 300px;
  }

  #opc-container {
    flex-direction: row;
    flex-wrap: wrap;
  }
  #opc-title {
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
    color: #FCBA03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    cursor: pointer;
    transition: .5s;
  }
  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }
</style>