<template>
  <q-page>
    <div class="title-wallet text-primary"><i>Carteira</i></div>
    <q-separator />
    <div class="row">
      <div class="col-9">

      </div>
      <div class="price col-3">
        Preço atual Bitcoin:<br/>$ {{bitcoinPrice}}
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      bitcoinPrice: null,
      pokeCoinPrice: null,
      myPokemons: null
    }
  },
  methods: {
    calculatePokeCoin (base_experience) {
      if (base_experience) {
        var unitPokePrice = this.bitcoinPrice * 0.00000001
        return unitPokePrice * base_experience
      }
      return null
    },
    getBitcoinPrice () {
      axios.get('https://blockchain.info/ticker').then((data) => {
        this.bitcoinPrice = data.data.USD.last
      })
    },
    getPokemons () {
      var stringPokemons = localStorage.getItem('Pokemons')
      if (stringPokemons) {
        return JSON.parse(stringPokemons)
      }
      return null
    }
  },
  created () {
    this.getBitcoinPrice()
    this.getPokemons()
  }
})
</script>

<style>
  .title-wallet {
    font-weight: bold;
    font-size: 30pt;
    padding: 10px;
    width: 100%;
    text-align: center;
  }

  .price {
    font-size: 20pt;
    text-align: right;
    padding: 10px
  }
</style>
