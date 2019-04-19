<template>
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-8">
                <div class="example">
                    <table class="table">
                        <thead>
                        <tr>
                            <th>Name</th>
                            <th>Symbol</th>
                            <th>Price ($$$)</th>
                            <th>7 Day Change (%)</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="coin in coins" :key="coin.id">
                            <td>{{ coin.name }}</td>
                            <td>{{ coin.symbol }}</td>
                            <td>{{ coin[`price_${input.fiat_currency}`] }}</td>
                            <td>{{ coin.percent_change_7d }}</td>
                        </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div class="col-md-4">
                <div class="example">
                    <form>
                        <div class="form-group">
                            <label for="usd">Amount ($$$)</label>
                            <input type="text" class="form-control" v-model="input.amount" id="usd" placeholder="Amount ($$$)">
                        </div>
                        <div class="form-group">
                            <select v-model="input.currency">
                                <option v-for="coin in coins" :value="coin.id" :key="coin.id">{{ coin.name }}</option>
                            </select>
                            <select v-model="input.fiat_currency">
                                <option value="usd">US Dollar</option>
                                <option value="eur">Euro</option>
                            </select>
                        </div>
                        <button class="btn btn-default" type="button" @click="convert()">Convert</button>
                    </form>
                    <p>
                        <strong>Value:</strong> {{ specific_coin_amount }}
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data () {
    return {
      coins: [],
      input: {
        amount: 1,
        currency: 'bitcoin',
        fiat_currency: 'usd'
      },
      specific_coin_amount: 0
    }
  },
  mounted () {
    axios({
      method: 'GET',
      url: 'https://api.coinmarketcap.com/v1/ticker/?limit=50'
    }).then(result => {
      this.coins = result.data
    }, error => {
      console.error(error)
    })
  },
  methods: {
    convert () {
      axios({
        method: 'GET',
        url: `https://api.coinmarketcap.com/v1/ticker/${this.input.currency}/`
      }).then(result => {
        this.specific_coin_amount = this.input.amount / result.data[0][`price_${this.input.fiat_currency}`]
      }, error => {
        console.error(error)
      })
    }
  }
}
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
