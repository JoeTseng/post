<template>
  <div class="cart-box">
    <h2>購物車</h2>
    <div v-if="items.length === 0" class="empty">目前沒有商品</div>
    <ul>
      <li v-for="it in items" :key="it.id" class="cart-item">
        <div class="left">
          <strong>{{ it.name }}</strong>
          <div class="small">NT$ {{ it.price }}</div>
        </div>
        <div class="right">
          <div class="qty-controls">
            <button @click="changeQty(it.id, it.qty - 1)">-</button>
            <input type="number" v-model.number="it.qty" min="1" class="qty" @change="changeQty(it.id, it.qty)" />
            <button @click="changeQty(it.id, it.qty + 1)">+</button>
          </div>
          <span class="sub">NT$ {{ it.price * it.qty }}</span>
          <button class="remove" @click="$emit('remove', it.id)">移除</button>
        </div>
      </li>
    </ul>
    <div class="summary" v-if="items.length">
      <div>總計: NT$ {{ total }}</div>
      <button @click="$emit('checkout')">結帳</button>
    </div>
  </div>
</template>

<script>
import { computed } from 'vue'

export default {
  name: 'Cart',
  props: { items: Array },
  emits: ['remove', 'checkout', 'update-qty'],
  setup(props, { emit }) {
    const total = computed(() => props.items.reduce((s, i) => s + i.price * i.qty, 0))

    function changeQty(id, qty) {
      const q = Math.max(1, Number(qty) || 1)
      emit('update-qty', id, q)
    }

    return { total, changeQty }
  }
}
</script>
