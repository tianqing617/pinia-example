<template>
  <Layout>
    <div>
      <h2>Hello {{ user.name }}</h2>

      <form @submit.prevent="addItemToCart" data-testid="add-items">
        <input type="text" v-model="itemName" />
        <button>Add</button>
      </form>

      <form @submit.prevent="buy">
        <ul data-testid="items">
          <li v-for="item in cart.items" :key="item.name">
            {{ item.name }} ({{ item.amount }})
            
            <button type="button" @click="cart.removeItem(item.name)">X</button>
          </li>
        </ul>

        <!-- 操作按钮区 -->
        <button :disabled="!user.name">Buy</button>
        
        <button type="button" data-testid="clear"
          :disabled="!cart.items.length"
          @click="clearCart"
        >
          Clear the cart
        </button>
      </form>
    </div>
  </Layout>
</template>

<script lang="ts">
import Layout from './layouts/default.vue'
import PiniaLogo from './components/PiniaLogo.vue'

import { defineComponent, ref } from 'vue'
import { useUserStore } from './stores/user'
import { useCartStore } from './stores/cart'

export default defineComponent({
  components: { Layout, PiniaLogo },

  setup() {
    const user = useUserStore()
    const cart = useCartStore()

    const itemName = ref('')

    function addItemToCart() {
      if (!itemName.value) return
      cart.addItem(itemName.value)
      // 添加成功后，清空输入框
      itemName.value = ''
    }

    function clearCart() {
      if (window.confirm('Are you sure you want to clear the cart?')) {
        // 清空store中的rawItems。直接访问state中的变量
        cart.rawItems = []
      }
    }

    async function buy() {
      const n = await cart.purchaseItems()

      console.log(`Bought ${n} items`)

      // cart.rawItems = []
    }

    // @ts-ignore
    window.stores = { user, cart }

    return {
      itemName,
      addItemToCart,
      cart,

      user,
      buy,
      clearCart,
    }
  },
})
</script>

<style scoped>
img {
  width: 200px;
}

button,
input {
  margin-right: 0.5rem;
  margin-bottom: 0.5rem;
}
</style>
