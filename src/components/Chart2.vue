<template>
  <div>
    <canvas ref="canvas"></canvas>
  </div>
</template>

<script type="text/javascript">
import { Line } from "vue-chartjs";
import axios from "axios";
export default {
  name: "HelloWorld2",
  extends: Line,
  data() {
    return {
      chartdata3: {
        labels: [1, 2, 3, 4, 5],
        datasets: [
          {
            label: "Bitcoin/Ethereum",
            backgroundColor: "#31D999",
            data: [0, 30, 0, 52, 20],
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
        `https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=eth&days=14&interval=daily`
      )
      .then((response) => {
        console.log(response);
        for (let i = 0; i < 15; i++) {
          this.chartdata3.datasets[0].data[i] = response.data.prices[i][1];
          let maxDate = new Date(new Date() - (14 - i) * 86400000);
          let isoDate = maxDate.toISOString().substr(5, 5);
          this.chartdata3.labels[i] = isoDate;
        }

        this.renderChart(this.chartdata3, this.options);
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
