<template>
  <div class="container">
    <div class="converter-title">
      <h1>Cryptocurrency Converter</h1>
    </div>
    <div class="container-converter">
      <div class="converter">
        <div class="converter__side-one">
          <select id="first-currency" v-model="first_currency" @change="currencyData()">
            <option value="btc">BTC</option>
            <option value="eth">ETH</option>
            <option value="usd">USD</option>
          </select>
          <input type="number" id="amount-one" v-model="amountOne" @input="fetchData()">
        </div>
        <div class="converter__switch">
          <button id="switch-button" @click="switchValues()"></button>
        </div>
        <div class="converter__side-two">
          <select id="second-currency" v-model="second_currency" @change="currencyData()">
            <option value="btc">BTC</option>
            <option value="eth">ETH</option>
            <option value="usd">USD</option>
          </select>
          <input type="number" id="amount-two" v-model="amountTwo" disabled>
        </div>
      </div>
      <div class="converter__chart">
        <line-chart :chart-data="datacollection"></line-chart>
      </div>
    </div>
  </div>
</template>

<script>
import LineChart  from '@/components/LineChart.js'

export default {
  name: "Converter",
  components: {
    LineChart
  },
  data() {
    return {
      data: [],
      first_currency: "btc",
      second_currency: "usd",
      amountOne: 1,
      amountTwo: 0,
      btcUSD: [],
      btcETH: [],
      ethUSD: [],
      ethBTC: [],
      usdBTC: [],
      usdETH: [],
      equalCurr: [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
      datacollection: {},
      current: [],
      dateFourteen: [],
      labelChart: '',
    };
  },

  methods: {
    async fetchData() {
      await fetch('https://api.coingecko.com/api/v3/simple/price?ids=bitcoin%2Cethereum&vs_currencies=usd%2Cbtc%2Ceth')
          .then((res) =>  res.json())
          .then((data) => {
            data.usd = {btc: (1 / data.bitcoin.usd), eth: (1 / data.ethereum.usd), usd: 1};
            data.btc = {usd: data.bitcoin.usd, eth: data.bitcoin.eth, btc: 1};
            data.eth = {btc: data.ethereum.btc, usd: data.ethereum.usd, eth: 1};
            this.amountTwo = parseFloat((this.amountOne * data[this.first_currency][this.second_currency]).toFixed(8));
          });
    },
    switchValues() {
      const temporaryValue = this.first_currency;
      this.first_currency = this.second_currency;
      this.second_currency = temporaryValue;
      this.currencyData();
    },
    async currencyData() {
      await this.fetchData();
      await fetch('https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=14&interval=daily')
          .then((result) => result.json())
          .then((data) => {
            this.btcUSD = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.btcUSD).push((data.prices)[i][1])
            }
            this.usdBTC = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.usdBTC).push(1 / ((data.prices)[i][1]))
            }
          });
      await fetch('https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=eth&days=14&interval=daily')
          .then((result) => result.json())
          .then((data) => {
            this.btcETH = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.btcETH).push((data.prices)[i][1])
            }
          });
      await fetch('https://api.coingecko.com/api/v3/coins/ethereum/market_chart?vs_currency=usd&days=14&interval=daily')
          .then((result) => result.json())
          .then((data) => {
            this.ethUSD = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.ethUSD).push((data.prices)[i][1])
            }
            this.usdETH = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.usdETH).push(1 / ((data.prices)[i][1]))
            }
          });
      await fetch('https://api.coingecko.com/api/v3/coins/ethereum/market_chart?vs_currency=btc&days=14&interval=daily')
          .then((result) => result.json())
          .then((data) => {
            this.ethBTC = [];
            for (let i = 0; i < (data.prices).length; i++) {
              (this.ethBTC).push((data.prices)[i][1])
            }
          });

      if (this.first_currency === 'btc' && this.second_currency === 'usd') {
        this.current = this.btcUSD;
        this.labelChart = 'BTC/USD';
      }
      if (this.first_currency === 'btc' && this.second_currency === 'eth') {
        this.current = this.btcETH;
        this.labelChart = 'BTC/ETH';
      }
      if (this.first_currency === 'eth' && this.second_currency === 'usd') {
        this.current = this.ethUSD;
        this.labelChart = 'ETH/USD';
      }
      if (this.first_currency === 'eth' && this.second_currency === 'btc') {
        this.current = this.ethBTC;
        this.labelChart = 'ETH/BTC';
      }
      if (this.first_currency === 'usd' && this.second_currency === 'btc') {
        this.current = this.usdBTC;
        this.labelChart = 'USD/BTC';
      }
      if (this.first_currency === 'usd' && this.second_currency === 'eth') {
        this.current = this.usdETH;
        this.labelChart = 'USD/ETH';
      }
      if (this.first_currency === this.second_currency) {
        this.current = this.equalCurr;
        if (this.first_currency === 'btc') this.labelChart = 'BTC';
        if (this.first_currency === 'eth') this.labelChart = 'ETH';
        if (this.first_currency === 'usd') this.labelChart = 'USD';
      }
      this.dateFourteen = [];
      for (let j = 13; j >= 0; j--) {
        let d = new Date();
        let y = d.setDate(d.getDate() - j);
        this.dateFourteen.push((new Date(y)).toISOString().slice(0, 10));
      }

      this.datacollection = {
        labels: this.dateFourteen,
        datasets: [
          {
            label: this.labelChart,
            data: this.current,
            tension: 0,
            borderColor: '#b5596c',
            fill: false,
          }
        ]
      }
    },
  },

  mounted() {
    this.fetchData();
    this.currencyData();
  },
};
</script>

<style lang="scss">

.container-converter {
  display: flex;
  justify-content: space-between;
}
.converter-title {
  margin: 20px 0;
  display: flex;
}
.converter {
  width: 50%;
  border: 1px solid #b5596c;
  box-shadow: 0px 0px 2px 1px #b5596c;
  display: flex;
  height: 400px;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}
.converter__side-one,
.converter__side-two {
  display: flex;
  flex-direction: column;
  width: 40%;
}
#first-currency,
#second-currency {
  border: 1px solid #cdcdcd;
  box-shadow: 4px -4px 2px 0px #cdcdcd;
  border-radius: 4px;
  height: 30px;
  width: 100%;
  text-align: left;
  margin-bottom: 5px;
  padding: 5px;
  cursor: pointer;
  &:hover {
    color:#a86d79;
  }
}
select option {
  color: #000000;
}
#amount-one,
#amount-two {
  border: 1px solid #cdcdcd;
  box-shadow: inset 0px 0px 2px 0px #cdcdcd;
  border-radius: 4px;
  height: 30px;
  width: 100%;
  padding: 5px;
  margin: 0;
}
input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
  -webkit-appearance: none;
}
input[type='number'],
input[type="number"]:hover,
input[type="number"]:focus {
  appearance: none;
  -moz-appearance: textfield;
}
#switch-button {
  width: 50px;
  height: 50px;
  -webkit-mask-image: url(../assets/arrows.svg);
  background-color: #000000;
  -webkit-mask-size: cover;
  transition: 1s;
  cursor: pointer;
  border: 0;
  padding: 0;
  margin: 0 10px;
  &:hover {
    background-color: #a86d79;
  }
}
.converter__switch {
  width: 15%;
}
.converter__chart {
  margin-top: 20px;
  width: 40%;
}

</style>