<template>
  <div id="nav">
    <router-link to="/">
      <i class="fas fa-balance-scale-right"></i> Exchange </router-link
    >|
    <router-link to="/profile"
      >Wallet <i class="fas fa-wallet"></i>
    </router-link>
  </div>
  <router-view
    v-bind:btcusd="btcusd"
    v-bind:ethusd="ethusd"
    v-bind:btceth="btceth"
    @changeBTC_on_account="changeBTC_on_account"
    @changeETH_on_account="changeETH_on_account"
    @changeUSD_on_account="changeUSD_on_account"
    v-bind:BTC_on_account="BTC_on_account"
    v-bind:ETH_on_account="ETH_on_account"
    v-bind:USD_on_account="USD_on_account"
  />
</template>

<script>
import axios from "axios";
import Home from "./views/Home.vue";
import Wallet from "./views/Wallet.vue";

export default {
  name: "nav",
  el: "#nav",
  components: {
    Home,
    Wallet,
  },
  props: {
    msg: String,
  },
  data() {
    return {
      btcusd: 0,
      ethusd: 0,
      btceth: 0,
      BTC_on_account: 2,
      ETH_on_account: 30,
      USD_on_account: 100000,
    };
  },
  computed: {},
  methods: {
    changeBTC_on_account(val) {
      this.BTC_on_account += val;
    },
    changeETH_on_account(val) {
      this.ETH_on_account += val;
    },
    changeUSD_on_account(val) {
      this.USD_on_account += val;
    },
    getDataFromHome(data) {
      console.log(data);
    },
  },
  watchs: {},
  mounted() {
    axios
      .get(`https://api.coingecko.com/api/v3/coins/bitcoin`)
      .then((response) => {
        this.btcusd = response.data.market_data.current_price.usd;
        this.btceth = response.data.market_data.current_price.eth;
        this.ethusd = this.btcusd / this.btceth;

        console.log(this.name);
      });
  },
};
</script>

<style>
* {
  margin: 0px;
  padding: 0px;
  background-color: #e6ffff;
  color: #1e5d3b;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 10px;
  font-size: 30px;
  background-color: #84cf8e;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
  background-color: #84cf8e;
  text-decoration: none;
}

#nav a.router-link-exact-active {
  color: #e6ffff;
}

i {
  background-color: #84cf8e;
}

#nav a.router-link-exact-active i {
  color: #e6ffff;
}
</style>
