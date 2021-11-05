<template>
  <div class="home"></div>

  <div>
    <div>Bitcoin</div>
    <input
      min="0"
      id="BTC"
      type="number"
      name="kye"
      size="10"
      maxlength="7"
      v-bind:value="1"
      @change="changeAmountOfCurrencyBTC"
    />
  </div>

  <div>
    <div>Etherium</div>
    <input
      min="0"
      id="ETH"
      type="number"
      name="kye"
      size="10"
      maxlength="7"
      v-bind:value="btceth"
      @change="changeAmountOfCurrencyETH"
    />
  </div>

  <div>
    <div>Dollar</div>
    <input
      min="0"
      id="USD"
      type="number"
      name="kye"
      size="10"
      maxlength="7"
      v-bind:value="btcusd"
      @change="changeAmountOfCurrencyUSD"
    />
  </div>
  <Chart1 />
  <div>
    <canvas ref="canvas"></canvas>
  </div>
  <Chart2 />
</template>

<script>
// @ is an alias to /src
import Chart1 from "@/components/Chart1.vue";
import Chart2 from "@/components/Chart2.vue";
import { Line } from "vue-chartjs";
import axios from "axios";

export default {
  name: "Home",
  extends: Line,
  components: {
    Chart1,
    Chart2,
  },
  methods: {
    sendDataToParent() {
      this.$emit("sendCourses", this.btceth);
      console.log("l");
    },
    changeAmountOfCurrencyBTC(event) {
      this.BTCusd = event.target.value * this.btcusd;
      this.BTCeth = event.target.value * this.btceth;
      document.getElementById("USD").value = this.BTCusd;
      document.getElementById("ETH").value = this.BTCeth;
    },
    changeAmountOfCurrencyUSD(event) {
      this.USDbtc = event.target.value / this.btcusd;
      this.USDeth = event.target.value / this.ethusd;
      document.getElementById("BTC").value = this.USDbtc;
      document.getElementById("ETH").value = this.USDeth;
    },
    changeAmountOfCurrencyETH(event) {
      this.ETHbtc = event.target.value / this.btceth;
      this.ETHusd = event.target.value * this.ethusd;
      document.getElementById("BTC").value = this.ETHbtc;
      document.getElementById("USD").value = this.ETHusd;
    },
    loadBtcUsdGraph() {
      this.renderChart(this.chartdata, this.options);
    },
  },
  props: {
    btcusd: 0,
    ethusd: 0,
    btceth: 0,
  },
  data() {
    return {
      BTCeth: 0,
      BTCucd: 0,
      USDbtc: 0,
      USDeth: 0,
      ETHusd: 0,
      ETHbtc: 0,

      chartdata: {
        labels: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14],
        datasets: [],
        datasets: [
          {
            label: "Bitcoin/USD",
            backgroundColor: "#7ED99E",
            data: [10, 30, 40, 52, 20],
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
  mounted() {
    axios
      .get(
        `https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=14&interval=daily`
      )
      .then((response) => {
        for (let i = 0; i < 15; i++) {
          this.chartdata.datasets[0].data[i] = response.data.prices[i][1];
          let maxDate = new Date(new Date() - (14 - i) * 86400000);
          let isoDate = maxDate.toISOString().substr(5, 5);
          this.chartdata.labels[i] = isoDate;
        }
        this.renderChart(this.chartdata, this.options);
      });
  },
};
</script>
