<template>
  <div class="container">
    <header>
      <h1>銷售終端機範本</h1>
      <div>
        <input v-model="q" placeholder="搜尋商品或價格..." />
        <button class="secondary" @click="clearFilter" v-if="q">清除</button>
      </div>
    </header>
    <main>
      <section class="products card">
        <ProductList :products="filtered" @add-to-cart="addToCart" />
      </section>
      <aside class="cart card">
        <Cart :items="cartItems" @remove="removeFromCart" @checkout="checkout" @update-qty="updateQty" />
      </aside>
    </main>
    <footer>
      <small>範本 — 可擴充的 Vue + Vite 銷售終端機</small>
    </footer>

    <div :class="['toast', { show: toast.show }]">{{ toast.msg }}</div>
  </div>
</template>

<script>
import { ref, computed, watch, onMounted } from 'vue'
import ProductList from './components/ui/ProductList.vue'
import Cart from './components/ui/Cart.vue'

export default {
  components: { ProductList, Cart },
  setup() {
    const products = ref([
      { id: 1, name: '咖啡 (Hot)', price: 120 },
      { id: 2, name: '奶茶', price: 100 },
      { id: 3, name: '三明治', price: 150 },
      { id: 4, name: '點心拼盤', price: 200 }
    ])

    const cartItems = ref([])
    const q = ref('')
    const toast = ref({ show: false, msg: '' })

    const filtered = computed(() => {
      const term = q.value.trim().toLowerCase()
      if (!term) return products.value
      return products.value.filter(p => String(p.name).toLowerCase().includes(term) || String(p.price).includes(term))
    })

    function addToCart(product) {
      const existing = cartItems.value.find(i => i.id === product.id)
      if (existing) existing.qty++
      else cartItems.value.push({ ...product, qty: 1 })
      showToast(`${product.name} 已加入購物車`)
    }

    function removeFromCart(id) {
      const idx = cartItems.value.findIndex(i => i.id === id)
      if (idx >= 0) cartItems.value.splice(idx, 1)
    }

    function checkout() {
      if (cartItems.value.length === 0) {
        showToast('購物車為空，請先加入商品')
        return
      }
      const total = cartItems.value.reduce((s, i) => s + i.price * i.qty, 0)
      showToast(`結帳成功！總金額：$${total}`)
      cartItems.value = []
    }

    function updateQty(id, qty) {
      const it = cartItems.value.find(i => i.id === id)
      if (!it) return
      it.qty = Math.max(1, Number(qty) || 1)
    }

    function showToast(msg, ms = 2000) {
      toast.value.msg = msg
      toast.value.show = true
      setTimeout(() => { toast.value.show = false }, ms)
    }

    function clearFilter() { q.value = '' }

    onMounted(() => {
      try {
        const raw = localStorage.getItem('cart')
        if (raw) cartItems.value = JSON.parse(raw)
      } catch (e) {}
    })

    watch(cartItems, (v) => {
      try { localStorage.setItem('cart', JSON.stringify(v)) } catch (e) {}
    }, { deep: true })

    return { products, cartItems, addToCart, removeFromCart, checkout, q, filtered, updateQty, toast, clearFilter }
  }
}
</script>
