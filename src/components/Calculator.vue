<template>
  <div>
    <h1>TRASCENDER GLOBAL</h1>
    <h2>Calculadora de Proyectos</h2>

    <hr />

    <b-container class="bv-example-row">
      <b-row>
        <b-col>
          <img src="../assets/TG.png" width="180" />
        </b-col>

        <b-col>
          <div>
            <h6>Cuantos pesos vale un dolar?</h6>
            <b-input-group size="sm" prepend="$" append=".00">
              <b-form-input v-model="trm" type="number"></b-form-input>
            </b-input-group>
          </div>
        </b-col>
      </b-row>
      <br />

      <b-row>
        <b-col>
          <div>
            <h6>Contribución Deseada: {{ contribucion }}%</h6>
            <b-input-group prepend="0" append="100" class="mt-2">
              <b-form-input
                type="range"
                min="0"
                max="100"
                v-model="contribucion"
                @click="saturar_contribucion"
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
            >
              <b-dropdown-item
                v-for="p in plataformasProyectos"
                :key="p.value"
                @click="plataforma = p"
                >{{ p.text }}</b-dropdown-item
              > </b-dropdown
            ><br />
            <p>{{ plataforma.text }}</p>
          </div>
        </b-col>

        <b-col>
          <div>
            <b-dropdown
              id="dropdown-2"
              text="Moneda del cliente"
              variant="primary"
              class="m-2"
            >
              <b-dropdown-item
                v-for="moneda in monedas"
                :key="moneda.value"
                @click="monedaCliente = moneda.value"
                >{{ moneda.text }}</b-dropdown-item
              > </b-dropdown
            ><br />
            <p>{{ monedaCliente }}</p>
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
              > </b-dropdown
            ><br />
            <p>{{ monedaTrascendentales }}</p>
          </div>
        </b-col>
      </b-row>
    </b-container>
    <hr />
    <h2>Valor Final</h2>
    <h3>$ {{ final_value || 0 }} {{ monedaCliente }}</h3>

    <b-button variant="success" type="button" @click="currenciesApi"
      class="verde">Consultar Tasas de Cambio</b-button
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
      plataforma: { value: 0.15, text: "Freelancer Recruiter" },
      rates: {},
      monedas: [
        { value: "USD", text: "Dolar Estadounidense (USD)" },
        { value: "EUR", text: "Euros (EUR)" },
        { value: "GBP", text: "Libra Esterlina (GBP)" },
        { value: "CAD", text: "Dolar Canadiense (CAD)" },
        { value: "AUD", text: "Dolar Australiano (AUD)" },
        { value: "NZD", text: "Dolar Neozelandes (NZD)" },
        { value: "SGD", text: "Dolar Singapur (SGD)" },
        { value: "MXN", text: "Peso Mexicano (MXN)" },
        { value: "COP", text: "Peso Colombiano (COP)" },
      ],
      plataformasProyectos: [
        { value: 0.15, text: "Freelancer Recruiter" },
        { value: 0.1, text: "Freelancer Normal" },
        { value: 0.2, text: "Upwork 20%" },
        { value: 0.11, text: "Upwork 10%" },
        { value: 0.05, text: "Upwork 5%" },
        { value: 0.0301, text: "Upwork Direct 3%" },
        { value: 0.0501, text: "Internacional PayPal" },
        { value: 0.03, text: "Internacional Payoneer" },
      ],
    };
  },
  computed: {
    final_value() {
      let final = 0;
      if (this.monedaTrascendentales == "USD") {
        final = this.valorAPagar * this.trm;
      } else {
        final = this.valorAPagar;
      }

      final = final / (1 - parseInt(this.contribucion) / 100);

      switch (this.monedaCliente) {
        case "USD":
          final = final / this.trm;
          break;
        case "EUR":
          final = (final / this.trm) * parseFloat(this.rates.EUR);
          break;
        case "GBP":
          final = (final / this.trm) * parseFloat(this.rates.GBP);
          break;
        case "CAD":
          final = (final / this.trm) * parseFloat(this.rates.CAD);
          break;
        case "AUD":
          final = (final / this.trm) * parseFloat(this.rates.AUD);
          break;
        case "NZD":
          final = (final / this.trm) * parseFloat(this.rates.NZD);
          break;
        case "SGD":
          final = (final / this.trm) * parseFloat(this.rates.SGD);
          break;
        case "MXN":
          final = (final / this.trm) * parseFloat(this.rates.MXN);
          break;
      }
      final = final / (1 - this.plataforma.value);

      //divisas
      final = final / (1 - 0.25);

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
    saturar_contribucion() {
    if (this.contribucion < 50) {
      this.contribucion = 50;
    }
  },
  },
  
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.verde {
  background: #3ACB89;
}

</style>
