<template>
  <div>
    <div>
      <h1>Calculadora de Proyectos</h1>
      <h2>Trascender Global</h2>
    </div>
    <hr />
    <b-container class="bv-example-row">
      <b-row>
        <b-col>
          <div>
            <h6>Cuantos pesos vale un dolar?</h6>
            <b-input-group size="sm" prepend="$" append=".00">
              <b-form-input v-model="trm" type="number"></b-form-input>
            </b-input-group>
          </div>
        </b-col>

        <b-col cols="6">
          <div>
            <h6>Contribución Deseada: {{ contribucion }}%</h6>
            <b-input-group prepend="0" append="100" class="mt-2">
              <b-form-input
                type="range"
                min="0"
                max="100"
                v-model="contribucion"
              ></b-form-input>
            </b-input-group>
          </div>
        </b-col>

        <b-col>
          <div>
            <h6>Valor a pagar</h6>
            <b-input-group size="sm" prepend="$" append=".00">
              <b-form-input type="number" v-model="valorAPagar"></b-form-input>
            </b-input-group>
          </div>
        </b-col>
      </b-row>
      <br />

      <b-row>
        <b-col>
          <div>
            <b-dropdown
              id="dropdown-3"
              text="De donde viene el proyecto?"
              variant="primary"
              class="m-2"
              v-model="plataforma"
            >
              <b-dropdown-item>Freelancer</b-dropdown-item>
              <b-dropdown-item>Upwork</b-dropdown-item>
              <b-dropdown-item>Internacional</b-dropdown-item>
              <b-dropdown-item>Local</b-dropdown-item>
            </b-dropdown>
          </div>
        </b-col>

        <b-col>
          <div>
            <b-dropdown
              id="dropdown-2"
              text="Moneda del cliente"
              variant="primary"
              class="m-2">
              <b-dropdown-item v-for="moneda in monedas" :key="moneda.value" @click="monedaCliente=moneda.value">{{moneda.text}}</b-dropdown-item>
            </b-dropdown>
          </div>
        </b-col>

        <b-col>
          <div>
            <b-dropdown
              id="dropdown-1"
              text="Moneda Trascendentales"
              variant="primary"
              class="m-2"
            >
              <b-dropdown-item @click="MTCOP"
                >Peso Colombiano (COP)</b-dropdown-item
              >
              <b-dropdown-item @click="MTUSD"
                >Dolar Estadounidense (USD)</b-dropdown-item
              >
            </b-dropdown>
          </div>
        </b-col>
      </b-row>
    </b-container>
    <hr />
    <h2>Valor Final</h2>
    <h6>{{ final_value || 0 }} {{monedaCliente}}</h6>

    <b-button variant="danger" type="button" @click="currenciesApi"
      >enter</b-button
    >
  </div>
</template>




<script>
import axios from "axios";
export default {
  data() {
    return {
      trm: 3500,
      monedaTrascendentales: "COP",
      valorAPagar: 0,
      contribucion: 60,
      monedaCliente: "USD",
      plataforma: "Freelancer",
      rates: {},
      monedas: [
        {value:'USD',text:'Dolar Estadounidense (USD)'},
        {value:'EUR',text:'Euros (EUR)'},
        {value:'GBP',text:'Libra Esterlina (GBP)'},
        {value:'CAD',text:'Dolar Canadiense (CAD)'},
        {value:'AUD',text:'Dolar Australiano (AUD)'},
        {value:'NZD',text:'Dolar Neozelandes (NZD)'},
        {value:'SGD',text:'Dolar Singapur (SGD)'},
        {value:'MXN',text:'Peso Mexicano (MXN)'},
        {value:'COP',text:'Peso Colombiano (COP)'},
      ],
    };
  },
  computed: {
    final_value() {
      let final = this.valorAPagar / (1 - parseInt(this.contribucion) / 100);
      switch (this.monedaCliente) {
        case "USD":
          final = final/this.trm;
          break;
        case "EUR":
          final = (final/this.trm)*parseFloat(this.rates.EUR)
          break;
        case "GBP":
          final = (final/this.trm)*parseFloat(this.rates.GBP)
          break;
        case "CAD":
          final = (final/this.trm)*parseFloat(this.rates.CAD)
          break;
        case "AUD":
          final = (final/this.trm)*parseFloat(this.rates.AUD)
          break;
        case "NZD":
          final = (final/this.trm)*parseFloat(this.rates.NZD)
          break;
        case "SGD":
          final = (final/this.trm)*parseFloat(this.rates.SGD)
          break;
        case "MXN":
          final = (final/this.trm)*parseFloat(this.rates.MXN)
          break;

      }

      if (this.trm > 0) {
        return final.toFixed(0);
      }
      return 0;
    },
  },
  methods: {
    async currenciesApi() {
      const output = (
        await axios.get("https://api.exchangeratesapi.io/latest?base=USD")
      ).data;
      console.log(output.rates);
      this.rates = output.rates;
    },

    MTCOP() {
      this.monedaTrascendentales = "COP";
    },

    MTUSD() {
      this.monedaTrascendentales = "USD";
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.test {
  border: 5px solid red;
}
</style>
