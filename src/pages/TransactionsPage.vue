<template>
  <div class="q-pa-md">
    <q-table
      title="Histórico de transações"
      :rows="rows"
      :columns="columns"
      row-key="name"
      dark
      color="amber"
    />
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'TransactionsPage',
  data () {
    return {
      rows: [],
      columns: [
        {
          name: 'id',
          required: true,
          label: 'id',
          align: 'left',
          field: 'id',
          sortable: true
        },
        {
          name: 'date',
          required: true,
          label: 'Data da transação',
          align: 'left',
          field: 'date',
          sortable: true
        },
        {
          name: 'name',
          label: 'Nome do Pokemon',
          align: 'left',
          field: 'name'
        },
        {
          name: 'type',
          align: 'center',
          label: 'Tipo de transação',
          field: 'type',
          format: (val) => val === 1 ? 'Compra' : 'Venda',
          sortable: true
        },
        {
          name: 'value',
          label: 'Valor da transação',
          field: 'value',
          format: (val) => {
            return '$ ' + (val / 1).toFixed(2).replace('.', ',').toString().replace(/\B(?=(\d{3})+(?!\d))/g, '.')
          },
          sortable: true
        }
      ]
    }
  },
  mounted () {
    axios.get('https://laravelpokecoinbyhallan.herokuapp.com/api/pokemons/history')
      .then((data) => {
        this.rows = data.data
      })
  },
  methods: {
    getDataGrid () {
      axios.get('https://laravelpokecoinbyhallan.herokuapp.com/api/pokemons/history')
        .then((data) => {
          console.log(data)
        })
    }
  }
}
</script>
