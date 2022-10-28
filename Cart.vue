<template>
  <div class="cart-component">
    <div class="cart-header">
      <button @click="openCart" class="cart-close">Закрыть</button>
      <h2 style="color: #fff;">Корзина</h2>
      <button class="cart-clear" @click="clearCartModal">Очистить</button>
    </div>
    <div class="cart-items row">
      <div v-for="product in items" :key="product.id" class="col-12 col-md-6"> 
        <CartItem  :product="product" />
      </div>
      <div v-show="this.$store.state.cart.length == 0">
        <p style="color:#FFA800; margin-top:20px;">В корзине нет товаров</p>
      </div>
      <h2 style="color: #fff; margin-top: 50px;" v-show="this.$store.state.cart.length !== 0">Итого: {{ totalPrice }} рублей</h2>
    </div>
    <div class="footer-btns d-flex">
      <button style="margin-right: 17px; padding: 4px 0px;" @click="openWaiter" class="servise-btn">Официант</button>
      <button style="margin-left: 17px; padding: 4px 0px;" class="oplata-btn" @click="openOnlinePayment">Онлайн оплата</button>
    </div>
    <CartClearModal :clearCartModal="clearCartModal" :clearCart="clearCart" v-if="visibleModalClear" />
  </div>
</template>
<script>
import CartItem from '@/components/CartItem.vue'
import CartClearModal from '@/components/CartClearModal.vue'
export default {
    props: ['openCart', 'openWaiter', 'openOnlinePayment'],
    components: {CartItem, CartClearModal},
    data() {
        return {
          cartNull: false,
          visibleModalClear: false,
        }
    },
    methods: {
      clearCart() {
        this.$store.commit("clearCart", this.$store.state.cart);
        localStorage.clear()
        this.visibleModalClear = false
      },
      clearCartModal() {
        this.visibleModalClear = !this.visibleModalClear
      },
    },
    computed: {
      items() {
        return this.$store.getters.cartItems;
      },
      totalPrice() {
        return this.$store.state.cart.reduce((sum, n) => sum + (n.price * n.quantity), 0)
      }
    }
}
</script>

<style>

</style>