<template>
  <div class="product-list">
    <div v-for="p in products" :key="p.id" class="product">
      <div class="info">
        <h3>{{ p.name }}</h3>
        <p class="price">NT$ {{ p.price }}</p>
      </div>
      <div class="actions">
        <div class="qty-controls">
          <button @click="decr(p.id)">-</button>
          <input type="number" min="1" v-model.number="qtys[p.id]" class="qty" />
          <button @click="incr(p.id)">+</button>
        </div>
        <button @click="add(p)">加入</button>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, toRefs } from 'vue'

export default {
  name: 'ProductList',
  props: { products: Array },
  emits: ['add-to-cart'],
  setup(props, { emit }) {
    const qtys = reactive({})
    props.products.forEach(p => { qtys[p.id] = 1 })

    function add(p) {
      const qty = Math.max(1, Number(qtys[p.id]) || 1)
      for (let i = 0; i < qty; i++) emit('add-to-cart', p)
      qtys[p.id] = 1
    }

    function incr(id) { qtys[id] = (Number(qtys[id]) || 1) + 1 }
    function decr(id) { qtys[id] = Math.max(1, (Number(qtys[id]) || 1) - 1) }

    return { ...toRefs(qtys), qtys, add, incr, decr }
  }
}
</script>
