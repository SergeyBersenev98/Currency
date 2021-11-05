<template>
  <div class="about">
    <h1>Crypto on your account</h1>
    <div class="flex">
      <div class="changeVolume">
        <h3>Buy or sell currency</h3>
        <div>
          <div class="volume">USD: {{ USD_on_account }}</div>
          <input
            id="USD_on_account"
            type="number"
            name="kye"
            size="10"
            maxlength="9"
            max="10000000"
            v-bind:value="0"
          />
        </div>
        <div>
          <div class="volume">ETH: {{ ETH_on_account }}</div>
          <input
            id="ETH_on_account"
            type="number"
            name="kye"
            size="10"
            maxlength="7"
            max="1000000"
            v-bind:value="0"
          />
        </div>
        <div>
          <div class="volume">BTC: {{ BTC_on_account }}</div>
          <input
            id="BTC_on_account"
            type="number"
            name="kye"
            size="10"
            maxlength="5"
            max="100000"
            v-bind:value="0"
          />
        </div>

        <button @click="changePortfile">Submit</button>
      </div>
      <canvas ref="canvas" id="canva" class="pieBar"></canvas>
    </div>
  </div>
  <div>Total: {{ total }} USD</div>
</template>

<script type="text/javascript">
import { Pie, mixins } from "vue-chartjs";
const { reactiveProp } = mixins;
import axios from "axios";
export default {
  name: "Wallet",
  extends: Pie,
  mixins: [reactiveProp],
  components: {},
  props: {
    USD_on_account: 0,
    ETH_on_account: 0,
    BTC_on_account: 0,
  },
  data() {
    return {
      total: 0,
      DollarsPerBitcoin: 0,
      EthereumsPerBitcoin: 0,
      DollarsPerEthereum: 0,
      componentKey: 0,
      chartdata4: {
        labels: ["USD", "ETH in USD", "BTC in USD"],

        datasets: [
          {
            label: "in USD",
            backgroundColor: ["#7ED99E", "#31D999", "#9FFABD"],
            data: [0, 0, 0],
          },
        ],
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },

  computed: {},
  methods: {
    forceRerender() {
      this.componentKey += 1;
    },
    changePortfile() {
      if (
        this.BTC_on_account +
          Number(document.getElementById("BTC_on_account").value) <
        0
      ) {
        document.getElementById("BTC_on_account").value = 0;
        alert("You cannot sell more BTC than you have");
      }
      if (
        this.ETH_on_account +
          Number(document.getElementById("ETH_on_account").value) <
        0
      ) {
        document.getElementById("ETH_on_account").value = 0;
        alert("You cannot sell more ETH than you have");
      }
      if (
        this.USD_on_account +
          Number(document.getElementById("USD_on_account").value) <
        0
      ) {
        document.getElementById("USD_on_account").value = 0;
        alert("You cannot sell more USD than you have");
      }

      this.$emit(
        "changeBTC_on_account",
        Number(document.getElementById("BTC_on_account").value)
      );
      this.$emit(
        "changeETH_on_account",
        Number(document.getElementById("ETH_on_account").value)
      );
      this.$emit(
        "changeUSD_on_account",
        Number(document.getElementById("USD_on_account").value)
      );
      this.chartdata4.datasets[0].data[0] =
        (this.USD_on_account +
          Number(document.getElementById("USD_on_account").value)) *
        1;
      this.chartdata4.datasets[0].data[1] =
        (this.ETH_on_account +
          Number(document.getElementById("ETH_on_account").value)) *
        this.DollarsPerEthereum;
      this.chartdata4.datasets[0].data[2] =
        (this.BTC_on_account +
          Number(document.getElementById("BTC_on_account").value)) *
        this.DollarsPerBitcoin;

      this.total =
        this.chartdata4.datasets[0].data[0] +
        this.chartdata4.datasets[0].data[1] +
        this.chartdata4.datasets[0].data[2];
      this.renderChart(this.chartdata4, this.options);
      document.getElementById("BTC_on_account").value = 0;
      document.getElementById("ETH_on_account").value = 0;
      document.getElementById("USD_on_account").value = 0;
    },
  },
  watch: {
    chartData() {
      this.$data._chart.update();
    },
  },
  mounted() {
    axios
      .get(`https://api.coingecko.com/api/v3/coins/bitcoin`)
      .then((response) => {
        this.DollarsPerBitcoin = response.data.market_data.current_price.usd;
        this.EthereumsPerBitcoin = response.data.market_data.current_price.eth;
        this.DollarsPerEthereum = Math.floor(
          this.DollarsPerBitcoin / this.EthereumsPerBitcoin
        );
        this.chartdata4.datasets[0].data[0] = this.USD_on_account;
        this.chartdata4.datasets[0].data[1] =
          this.ETH_on_account * this.DollarsPerEthereum;
        this.chartdata4.datasets[0].data[2] =
          this.BTC_on_account * this.DollarsPerBitcoin;
        this.total =
          this.USD_on_account +
          this.ETH_on_account * this.DollarsPerEthereum +
          this.BTC_on_account * this.DollarsPerBitcoin;
        this.renderChart(this.chartdata4, this.options);
      });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

.pieBar {
  width: 20%;
  max-height: 400px;
}

.changeVolume {
  padding-top: 1vh;
  padding-bottom: 5vh;
  margin-right: 50px;
}

.flex {
  display: flex;
}

input {
  width: 30vw;
  min-width: 200px;
  margin: 5px;
  font-size: 25px;
  border-radius: 4px;
  background-color: #d5ffef;
  border-color: #84cf8e;
}

.about {
  padding-left: 10%;
  padding-right: 10%;
}

h1 {
  margin-bottom: 8vh;
  margin-top: 4vh;
}

button {
  width: 30vw;
  min-width: 200px;
  margin: 5px;
  font-size: 25px;
  margin-top: 25px;
  border-radius: 4px;
  background-color: #c1ffd7;
  border-color: #84cf8e;
}

button:hover {
  background-color: #84cf8e;
  transition: 0.3s;
}

#canva {
  max-width: 50%;
}

h3 {
  margin-bottom: 2vh;
}

@media (max-width: 900px) {
  .flex {
    display: block;
  }

  #canva {
    max-width: 100%;
  }
}
</style>
