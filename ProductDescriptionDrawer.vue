<template>
<div class="card-bg">
  <div class="card modal-content">
    <div class="modal-okno">
      <div class="modal-left">
        <img :src="'http://director-kab.ru.local/' + product.img" class="product-img" alt="">
        <h6>{{product.title}}</h6>
        <p class="modal-price">{{product.price}} рублей</p>
      </div>
      <div class="modal-right">
        <div class="modal-right-description">
          <p>{{ product.description }}</p>
          <p>{{ product.compound }}</p>
          <p>{{ product.weight }} г.</p>
          <p>{{ product.calories }} калорий</p>
        </div>
      </div>
    </div>
    <div class="modal-btns">
      <button @click="closeCard" class="modal-close-btn">Закрыть</button>
      <div class="detail_quantity_inner" v-show="product_total > 0">        
          <button class="detail_bt_minus" @click="removeToCart">
              <svg viewBox="0 0 24 24"><line x1="5" y1="12" x2="19" y2="12"></line></svg>
          </button>
          <p class="detail_quantity">{{ product_total }}</p>
          <button class="detail_bt_plus" @click="addToCart">
              <svg viewBox="0 0 24 24"><line x1="12" y1="5" x2="12" y2="19"></line><line x1="5" y1="12" x2="19" y2="12"></line></svg>
          </button>
      </div>
      <button @click="addToCart" class="modal-btn-basket" v-show="product_total == null">Добавить в заказ</button>
    </div>
  </div>
</div>
</template>
<script>
export default {
props: ['product', 'active'],
  data() {
    
  },
  methods: {
    closeCard() {
      this.$store.state.activeCard = false;
      document.body.style = "";
    },
    // addToCart() {
    //   this.$store.state.cart.push(this.product);
    //   console.log(this.$store.state.cart);
    // },
    addToCart() {
      this.$store.commit("addToCart", this.product);
    },
    removeToCart() {
      this.$store.commit("removeToCart", this.product);
    },
  },
  computed: {
    items() {
      return this.$store.getters.cartItems;
    },
    product_total() {
      return this.$store.getters.productQuantity(this.product); 
    },
  }
}
</script>

<style lang="scss">

</style>