<template>
  <q-page>
    <div class="title-wallet text-primary"><i>Carteira</i></div>
    <q-separator />
    <div class="row">
      <div class="col-9">
        <q-btn color="green" glossy label="Adicionar Pokemon" icon="fas fa-plus" @click="modal=true"/>
      </div>
      <div align="right" class="col-3 q-pa-md text-h6">
       Valor total investido: ${{ formatPrice(amountApplied) }} <br/>
       Valor atual da carteira: ${{getAmountCurrent()}}
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
        <div class="price">Valor atual: $ {{ calculatePokeCoin(pokemon.base_experience) }}</div>
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
              reverse-fill-mask
              fill-mask
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
              fill-mask
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
const apiPokemon = 'https://laravelpokecoinbyhallan.herokuapp.com/api/pokemons/'

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      emptyPhoto: 'https://static.vecteezy.com/system/resources/thumbnails/008/695/917/small/no-image-available-icon-simple-two-colors-template-for-no-image-or-picture-coming-soon-and-placeholder-illustration-isolated-on-white-background-vector.jpg',
      bitcoinPrice: null,
      pokeCoinPrice: null,
      amountApplied: null,
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
    calculatePokeCoin (baseExperience) {
      if (baseExperience) {
        const unitPokePrice = this.bitcoinPrice * 0.00000001
        return this.formatPrice(unitPokePrice * baseExperience)
      }
      return '0.00'
    },
    getBitcoinPrice () {
      axios.get('https://blockchain.info/ticker').then((data) => {
        this.bitcoinPrice = data.data.USD.last
      })
    },
    getPokemons () {
      axios.get(apiPokemon + 'index').then((data) => { this.inventory = data.data })
    },
    getNames () {
      axios.get(apiPokemon + 'names').then((data) => { this.optionsNames = data.data })
    },
    formatPrice (value) {
      const val = (value / 1).toFixed(2).replace('.', ',')
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.')
    },
    async addPokemon () {
      if (this.form.name && this.form.buyPrice && this.form.buyDate) {
        this.form.id = this.form.name.value
        this.form.name = this.form.name.label
        const form = JSON.stringify(this.form)
        axios.post(apiPokemon + 'create/' + form).then(() => {
          this.getPokemons()
          this.resetModal()
        })
      }
    },
    async sellPokemon () {
      if (this.formSell.id && this.formSell.sellPrice && this.formSell.sellDate) {
        const form = JSON.stringify(this.formSell)
        axios.post(apiPokemon + 'sellPokemon/' + form).then(() => {
          this.getPokemons()
          this.resetModal(true)
        })
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
      return pokemon.imagem ?? this.emptyPhoto
    },
    getAmountApplied () {
      axios.get(apiPokemon + 'amountApplied').then((response) => { this.amountApplied = response.data })
    },
    getAmountCurrent () {
      console.log(this.inventory.length)
      // if (this.inventory.length > 1) {
      // }
    }
  },
  created () {
    this.getBitcoinPrice()
    this.getPokemons()
    this.getNames()
    this.getAmountApplied()
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
