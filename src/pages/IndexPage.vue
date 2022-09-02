<template>
  <q-page>
    <div class="title-wallet text-primary"><i>Carteira</i></div>
    <q-separator />
    <div class="row">
      <div class="col-12">
        <q-btn color="green" glossy label="Adicionar Pokemon" icon="fas fa-plus" @click="modal=true"/>
      </div>
    </div>
    <div class="row q-pa-md">
      <q-card
        class="cards"
        v-for="(pokemon, index) in inventory"
        :key="index"
      >
      <img
        :src="getThumb(pokemon)"
      />
      <div class="description-card">
        <div class="title-card"> {{ pokemon.name }} </div>
        <q-separator class="separator" color="secondary"/>
        <div class="price">Valor pago: $ {{ formatPrice(pokemon.buy_price) }} </div>
        <div class="price">Valor atual: $ {{ formatPrice(pokemon.currentPriceUSD) }}</div>
      </div>
      <q-card-actions class="absolute-bottom">
        <q-btn
          class="col-12"
          color="secondary"
          label="Vender"
          icon="fa-solid fa-cart-shopping"
          @click="sellModal(pokemon)"
        />
      </q-card-actions>
    </q-card>
    </div>
    <div class="row">
      <div align="right" class="col-12 q-pa-md text-h6">
       Valor total investido: ${{ formatPrice(amountApplied) }} <br/>
       Valor atual da carteira: ${{formatPrice(amountCurrent)}}
      </div>
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
              @click="resetModal()"
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
              v-model="form.buyPrice"
              label="Digite o valor da compra"
              mask="#.##"
              hint="Ao meno 3 números"
              reverse-fill-mask
              input-class="text-right"
            />
          </div>
          <div class="q-pa-md">
            <q-input
              v-model="form.buyDate"
              filled
              type="date"
            />
          </div>
          <div align="right" class="q-pa-md">
            <q-btn color="primary" label="Cancelar" @click="resetModal()"/>
            <q-btn class="q-ml-md" color="secondary" label="Adicionar" @click="addPokemon" />
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
     <!-- modal sell pokemon -->
    <q-dialog v-model="modalSell">
      <q-card class="row modal-size">
        <div class="row col-12">
          <div class="title-modal col-9">Vender Pokemon: {{formSell.name}}</div>
          <div class="col-3" align="right">
            <q-btn
              flat
              dense
              icon="fa-solid fa-xmark"
              @click="resetModal(true)"
            />
          </div>
        </div>
        <q-card-section class="col-12">
          <div class="q-pa-md">
            <q-select
              filled
              v-model="formSell.name"
              label="Selecione o Pokemon"
              :options="optionsNames"
              disable
            />
          </div>
          <div class="q-pa-md">
            <q-input
              filled
              v-model="formSell.sellPrice"
              label="Valor da venda"
              mask="#.##"
              reverse-fill-mask
              hint="Ao meno 3 números"
              input-class="text-right"
            />
          </div>
          <div class="q-pa-md">
            <q-input
              v-model="formSell.sellDate"
              label="Data da venda"
              filled
              type="date"
              input-class="text-right"
            />
          </div>

          <div align="right" class="q-pa-md">
            <q-btn color="primary" label="Cancelar" @click="resetModal(true)"/>
            <q-btn class="q-ml-md" color="secondary" label="Vender" @click="sellPokemon" />
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import axios from 'axios'
<<<<<<< HEAD
const API_LARAVEL = 'https://laravelpokecoinbyhallan.herokuapp.com/api/pokemons/'
=======
const apiPokemon = 'https://laravelpokecoinbyhallan.herokuapp.com/api/pokemons/'
>>>>>>> test

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      emptyPhoto: 'https://static.vecteezy.com/system/resources/thumbnails/008/695/917/small/no-image-available-icon-simple-two-colors-template-for-no-image-or-picture-coming-soon-and-placeholder-illustration-isolated-on-white-background-vector.jpg',
      bitcoinPrice: null,
      pokeCoinPrice: null,
      amountApplied: 0,
      amountCurrent: 0,
      form: {
        id: null,
        name: null,
        buyPrice: null,
        buyDate: null
      },
      formSell: {
        id: null,
        name: null,
        sellPrice: null,
        sellDate: null
      },
      optionsNames: [],
      modal: false,
      modalSell: false,
      inventory: []
    }
  },
  methods: {
<<<<<<< HEAD
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
      return true
    },
    async moreData () {
      return true
    },
    getNames () {
      const test = axios.get(API_LARAVEL)
      console.log(test)
=======
    getData () {
      axios.get(apiPokemon + 'index').then((data) => {
        this.inventory = data.data.inventory
        this.amountApplied = data.data.amountApplied
        this.amountCurrent = data.data.amountCurrent
        this.optionsNames = data.data.optionsNames
      })
>>>>>>> test
    },
    formatPrice (value) {
      const val = (value / 1).toFixed(2).replace('.', ',')
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.')
    },
    async addPokemon () {
<<<<<<< HEAD
      if (this.form.name && this.form.price && this.form.date) {
        const test = await this.moreData()
        const test2 = await this.setLocalPokemons()
        if (test && test2) {
          this.resetModal()
        }
=======
      if (this.form.name && this.form.buyPrice && this.form.buyDate) {
        this.form.id = this.form.name.value
        this.form.name = this.form.name.label
        const form = JSON.stringify(this.form)
        axios.post(apiPokemon + 'create/' + form).then(() => {
          this.getData()
          this.resetModal()
        })
      }
    },
    async sellPokemon () {
      if (this.formSell.id && this.formSell.sellPrice && this.formSell.sellDate) {
        const form = JSON.stringify(this.formSell)
        axios.post(apiPokemon + 'sellPokemon/' + form).then(() => {
          this.getData()
          this.resetModal(true)
        })
>>>>>>> test
      }
    },
    resetModal (sell = false) {
      if (sell) {
        this.formSell.id = null
        this.formSell.name = null
        this.formSell.sellPrice = null
        this.formSell.sellDate = null
        this.modalSell = false
      } else {
        this.form.id = null
        this.form.name = null
        this.form.buyPrice = null
        this.form.buyDate = null
        this.modal = false
      }
    },
    sellModal (pokemon) {
      this.formSell.id = pokemon.id
      this.formSell.name = pokemon.name
      this.modalSell = true
    },
    getThumb (pokemon) {
<<<<<<< HEAD
      return pokemon.img ?? this.emptyPhoto
    },
    getAmountApplied () {
      const pokemons = this.getLocalPokemons()
      pokemons.forEach(() => {})
    },
    getAmountCurrent () {

=======
      return pokemon.imagem ?? this.emptyPhoto
>>>>>>> test
    }
  },
  created () {
    this.getData()
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
