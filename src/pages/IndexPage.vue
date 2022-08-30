<template>
  <q-page>
    <div class="title-wallet text-primary"><i>Carteira</i></div>
    <q-separator />
    <div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'IndexPage',
  data () {
    return {
      data: null
    }
  },
  methods: {}
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
</style>
