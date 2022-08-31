<template>
  <q-page>
    <div class="title-wallet text-primary"><i>Carteira</i></div>
    <q-separator />
    <div class="row">
      <div class="col-9">
        <q-btn color="green" glossy label="Adicionar Pokemon" icon="fas fa-plus" @click="modal=true"/>
      </div>
      <div align="right" class="col-3 q-pa-md text-h6">
       Valor total investido: $ {{bitcoinPrice}} <br/>
       Valor atual da carteira: $ {{bitcoinPrice}}
      </div>
    </div>
    <div class="row q-pa-md">
      <q-card
        class="cards"
        v-for="(pokemon, index) in getLocalPokemons()"
        :key="index"
      >
      <img
        :src="pokemon.image"
      />
      <div class="description-card">
        <div class="title-card"> {{ pokemon.name }} </div>
        <q-separator class="separator" color="secondary"/>
        <div class="price">valor pagor: $ {{ formatPrice(pokemon.buyPrice) }} </div>
        <div class="price">valor atual: $ {{ formatPrice(pokemon.buyPrice) }} </div>
      </div>
      <q-card-actions class="absolute-bottom">
        <q-btn
          class="col-12"
          color="secondary"
          label="Vender"
          icon="fa-solid fa-cart-shopping"
        />
      </q-card-actions>
    </q-card>
    </div>
    <!-- modal add pokemon -->
    <q-dialog v-model="modal">
      <q-card class="row modal-size">
        <div class="row col-12">
          <div class="title-modal col-9">Adicionar novo Pokemon a carteira</div>
          <div class="col-3" align="right">
            <q-btn
              flat
              dense
              icon="fa-solid fa-xmark"
              @click="resetModal"
            />
          </div>
        </div>
        <q-card-section class="col-12">
          <div class="q-pa-md">
            <q-select
              filled
              v-model="form.name"
              label="Selecione o Pokemon"
              :options="optionsNames"
            >
              <template v-slot:no-option>
                <q-item>
                  <q-item-section class="text-grey">
                    No results
                  </q-item-section>
                </q-item>
              </template>
            </q-select>
          </div>
          <div class="q-pa-md">
            <q-input
              filled
              v-model="form.price"
              label="Digite o valor da compra"
              mask="#.##"
              reverse-fill-mask
              fill-mask
              input-class="text-right"
            />
          </div>
          <div class="q-pa-md">
            <q-input
              v-model="form.date"
              filled
              type="date"
            />
          </div>
          <div align="right" class="q-pa-md">
            <q-btn color="primary" label="Cancelar" @click="resetModal"/>
            <q-btn class="q-ml-md" color="secondary" label="Adicionar" @click="addPokemon" />
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'
const apiPokemon = 'https://pokeapi.co/api/v2/pokemon/'

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      bitcoinPrice: null,
      pokeCoinPrice: null,
      form: {
        name: null,
        price: null,
        date: null,
        baseExp: null,
        img: null
      },
      optionsNames: [],
      modal: false
    }
  },
  methods: {
    calculatePokeCoin (baseExperience) {
      if (baseExperience) {
        const unitPokePrice = this.bitcoinPrice * 0.00000001
        return unitPokePrice * baseExperience
      }
      return null
    },
    getBitcoinPrice () {
      axios.get('https://blockchain.info/ticker').then((data) => {
        this.bitcoinPrice = data.data.USD.last
      })
    },
    getLocalPokemons () {
      const stringPokemons = localStorage.getItem('Pokemons')
      if (stringPokemons) {
        return JSON.parse(stringPokemons)
      }
      return []
    },
    async setLocalPokemons () {
      const itens = this.getLocalPokemons()
      itens.push({
        name: this.form.name.label,
        buyPrice: this.form.price,
        buyDate: this.form.date,
        baseExp: this.baseExp,
        image: this.form.img
      })
      localStorage.setItem('Pokemons', JSON.stringify(itens))
    },
    async moreData () {
      axios.get(apiPokemon + this.form.name.label).then((data) => {
        this.baseExp = data.data.base_experience
        axios.get(data.data.forms[0].url).then((data) => {
          this.form.img = data.data.sprites.front_default
        })
      })
    },
    getNames () {
      const names = []
      axios.get(apiPokemon + '?limit=9999').then((data) => {
        data.data.results.forEach((value, key) => {
          names.push({
            label: value.name,
            value: key,
            description: value.name
          })
          this.optionsNames = names
        })
      })
    },
    formatPrice (value) {
      const val = (value / 1).toFixed(2).replace('.', ',')
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.')
    },
    async addPokemon () {
      if (this.form.name && this.form.price && this.form.date) {
        await this.moreData().then(this.setLocalPokemons()).then(this.resetModal())
      }
    },
    resetModal () {
      this.form.name = null
      this.form.price = null
      this.form.date = null
      this.modal = false
    }
  },
  created () {
    this.getBitcoinPrice()
    this.getLocalPokemons()
    this.getNames()
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
    font-size: 14pt;
    text-align: right;
    padding: 2px 10px
  }

  .title-modal {
    font-size: 14pt;
    padding:10px;
    font-weight: bold;
  }

  .cards {
    background-color: rgba(240, 240, 247, 0.842);
    min-height: 450px !important;
    width: 300px !important;
    padding: 10px 10px;
    margin: 10px 30px;
  }

  .title-card {
    font-size: 16pt
  }

</style>
