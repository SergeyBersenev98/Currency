<template>
  <div>
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script type="text/javascript">
import { Line } from "vue-chartjs";
import axios from "axios";
export default {
  name: "Chart1",
  extends: Line,
  data() {
    return {
      chartdata2: {
        labels: [1, 2, 3, 4, 5],
        datasets: [
          {
            label: "Ethereum/USD",
            backgroundColor: "#9FFABD",
            data: [210, 30, 40, 52, 20],
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
        `https://api.coingecko.com/api/v3/coins/ethereum/market_chart?vs_currency=usd&days=14&interval=daily`
      )
      .then((response) => {
        for (let i = 0; i < 15; i++) {
          this.chartdata2.datasets[0].data[i] = response.data.prices[i][1];
          let maxDate = new Date(new Date() - (14 - i) * 86400000);
          let isoDate = maxDate.toISOString().substr(5, 5);
          this.chartdata2.labels[i] = isoDate;
        }
        this.renderChart(this.chartdata2, this.options);
      });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
