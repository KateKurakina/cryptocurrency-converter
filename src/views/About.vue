<template>
  <div class="about">
    <div class="container">
      <div class="about-title">
        <h1>My Portfolio</h1>
      </div>

      <div class="table_crypto">
        <table>
          <thead class="table__head">
          <tr>
            <th class="table__id">#</th>
            <th class="table__cname">Coin</th>
            <th class="table__holdings">Holdings</th>
            <th class="table__oper">Transactions</th>
          </tr>
          </thead>
          <tbody>
          <tr>
            <td>1</td>
            <td>Bitcoin</td>
            <td>
              <span> {{ btcValue }} &#8383;</span>
            </td>
            <td class="oper__btc">
              <div class="oper__btc-container">
                <button id="minus-btc" @click="minusButtonBTC()">-</button>
                <input class="amountBTC" type="number" v-model="amountBTC" >
                <button id="plus-btc" @click="plusButtonBTC()">+</button>
              </div>
            </td>
          </tr>
          <tr>
            <td>2</td>
            <td>Ethereum</td>
            <td> {{ ethValue }} &#926;</td>
            <td class="oper__eth">
              <div class="oper__eth-container">
                <button id="minus-eth" @click="minusButtonETH()">-</button>
                <input type="number" class="amountETH" v-model="amountETH" >
                <button id="plus-eth" @click="plusButtonETH()">+</button>
              </div>
            </td>
          </tr>
          </tbody>
        </table>
      </div>

      <div class="total_bal">Total Balance: {{ rating }} &#36;</div>
      <div>
        <pie-chart :chart-data="cryptoDistribution" :height="90"></pie-chart>
      </div>
    </div>
  </div>
</template>

<script>
import PieChart from '@/components/PieChart.js'

export default {
  name: 'Home',
  components: {
    PieChart
  },
  data () {
    return {
      btcValue: 180,
      ethValue: 250,
      amountBTC: 0,
      amountETH: 0,
      rating: 0,
      amountBTCusd: 0,
      amountETHusd: 0,
      percBTC: 0,
      percETH: 0,
      cryptoDistribution: {},
    };
  },
  methods: {
    plusButtonBTC() {
      this.btcValue += Number(this.amountBTC);
      this.portfolioRating();
    },
    minusButtonBTC() {
      this.btcValue -= Number(this.amountBTC);
      this.portfolioRating();
    },
    plusButtonETH() {
      this.ethValue += Number(this.amountETH);
      this.portfolioRating();
    },
    minusButtonETH() {
      this.ethValue -= Number(this.amountETH);
      this.portfolioRating();
    },
    async portfolioRating() {
      await fetch ('https://api.coingecko.com/api/v3/simple/price?ids=bitcoin%2Cethereum&vs_currencies=usd')
          .then((res) =>  res.json())
          .then((data) => {
            this.amountBTCusd = this.btcValue * data.bitcoin.usd;
            this.amountETHusd = this.ethValue * data.ethereum.usd;
            this.rating = (this.amountETHusd + this.amountBTCusd).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
            if (this.btcValue <= 0) {
              this.percBTC = 0;
            } else this.percBTC = parseFloat((this.amountBTCusd / (this.amountETHusd + this.amountBTCusd) * 100).toFixed(2));
            if (this.ethValue <= 0) {
              this.percETH = 0;
            } else this.percETH = parseFloat((this.amountETHusd / (this.amountETHusd + this.amountBTCusd) * 100).toFixed(2));

            this.cryptoDistribution = {
              labels: ['Bitcoin', 'Ethereum'],
              datasets: [
                {
                  label: ['Распределение криптовалют в портфеле'],
                  backgroundColor: ['#b5596c', '#97738c'],
                  data: [this.percBTC, this.percETH],
                }
              ]
            }
          });
    },
  },
  mounted() {
    this.portfolioRating();
  },
};
</script>
<style lang="scss">
.about-title {
  margin: 20px 0;
  display: flex;
}

.table_crypto {
  display: flex;
  width: 100%;
  box-shadow: 0 0 4px 1px #d2d2d2;
  border-top: 1px solid #cdcdcd;
  border-bottom: 1px solid #cdcdcd;
  border-radius: 2px;
}
table {
  border-collapse: collapse;
  width: 100%;
}
th {
  border-bottom: 1px solid #b8b8b8;
}
tr {
  height: 40px;
}
.table__id {
  width: 10%;
}
.table__cname {
  width: 25%;
}
.table__holdings {
  width: 25%;
}
.table__oper {
  width: 40%;
}
.oper__btc,
.oper__eth {
  display: flex;
  justify-content: center;
  border: none;
}
.oper__btc-container,
.oper__eth-container {
  display: flex;
  width: 160px;
  height: 30px;
  border: 2px solid #000000;
  justify-content: center;
  align-items: center;
  margin: 4px 0;
}
#minus-btc,
#plus-btc,
#minus-eth,
#plus-eth{
  width: 25px;
  border: none;
  background-color: #ffffff;
  margin: 0;
  cursor: pointer;
  &:hover {
    color: #a86d79;
  }
  font-size: 20px;
}
.amountBTC,
.amountETH {
  width: 100px;
  border: none;
  background-color: #ffffff;
  margin: 0;
  text-align: center;
}
.total_bal {
  width: 100%;
  text-align: end;
  margin-top: 10px;
  font-size: 18px;
  font-weight: bold;
}
</style>
