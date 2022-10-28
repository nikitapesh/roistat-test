<template>
    <div v-if="this.$store.state.activeCard == true">
      <ProductDescriptionDrawer
        :product="product"
        :active="active.dat_drawer"
        v-on:closeProductDrawer="closeProductDrawer"
      />
    </div>
  <div class="container" style="position:relative; padding: 0 20px; padding-bottom:50px;">
    <div class="fixed-header">
      <header class="header d-flex justify-content-between">
        <div class="logo"><img src="@/assets/img/logo.svg" alt="logotype" /></div>
        <ul style="margin-top: 0.8rem!important;" class="header-ul d-flex mt-3 col-sm-8 col-lg-6" v-if="search_input == false">
          <li class="header-list" v-for="(cat, index) in returnCategory" :key="cat.id">
            <Category :cat="cat" :class="{ active: index === 0 }" @sendObject2="myCatEmit" />
          </li>
        </ul>
        <div class="ui-block_1 pt-3" v-show="!search_input" @click="searchControl">
          <img src="@/assets/img/search.svg" />
        </div>
        <input
          v-show="search_input"
          class="search-input mt-3"
          v-model="search"
          type="text"
          placeholder="Название блюда"
        />
        <img
          v-show="search_input"
          @click="closeSearch"
          src="@/assets/img/close.svg"
          alt=""
        />
      </header>
    </div>
  <div class="hidden-search"  v-show="visibleSlider == true" style="padding-top: 70px;">
    <SliderComponent />
  </div>
    <div class="menu-list">
      <template v-for="cat in categoryArr" :key="cat.id">
        <section
          class="mt-3 section"
        >
          <h3 v-if="visibleCategory == true" class="title-top" :id="cat.id">
            {{ cat.title }}
          </h3>
          <div class="row mt-3">
            <template v-for="product in filteredList" :key="product.id">
              <ProductItem 
                v-if="product.category == cat.title" 
                @sendObject="viewProduct" 
                :product="product" 
              />
            </template>
          </div>
        </section>
      </template>
      <div class="footer-btns d-flex">
        <button @click="openWaiter" class="servise-btn">Официант</button>
        <div class="cart" @click="openCart">
          <img class="cart-btn-img" src="../assets/img/cart.svg" alt="">
          <div class="cart-circle"><span>{{ totalQuantity }}</span></div>
        </div>
        <button class="oplata-btn" @click="openOnlinePayment">Онлайн оплата</button>
      </div>
    </div>
  </div>
  <Cart 
  v-show="visibleCart == true"
  :openCart="openCart"
  :openWaiter="openWaiter"
  :openOnlinePayment="openOnlinePayment"
  />
  <WaiterModal 
  :openWaiter="openWaiter"
  :openConfirmation="openConfirmation"
  :visibleConfirmation="visibleConfirmation"
  :visibleWaiter="visibleWaiter" 
  />
  <OnlinePayment
  v-show="visibleOnlinePayment"
  :openOnlinePayment="openOnlinePayment" 
  />
</template>
<script>
import ProductItem from "@/components/ProductItem.vue";
import Category from "@/components/Category.vue";
import ProductDescriptionDrawer from "@/components/ProductDescriptionDrawer.vue";
import Cart from "@/components/Cart.vue";
import SliderComponent from "@/components/SliderComponent.vue";
import WaiterModal from "@/components/WaiterModal.vue"
import OnlinePayment from '@/components/OnlinePayment.vue'

import axios from 'axios'
export default {
  components: {
    ProductItem,
    ProductDescriptionDrawer,
    Category,
    Cart,
    SliderComponent,
    WaiterModal,
    OnlinePayment
  },
  //props: ["product", "active"],
  computed: {
    filteredList: function () {
      let self = this;
      return this.products.filter(function (product) {
          if (self.search.length > 1) {
              self.visibleCategory = false;
              self.visibleSlider = false;
          }
          return product.title.toLowerCase().indexOf(self.search.toLowerCase()) >= 0
      });
    },
    returnCategory: function() {
      return this.categoryArr;
    },
    cartCount: function () {
      return this.$store.state.cart.length;
    },
    totalQuantity: function () {
      return this.$store.state.cart.reduce((sum, n) => sum + n.quantity, 0)
    },
  },
  data() {
    return {
      visibleCart: false,
      visibleCategory: true,
      visibleSlider: true,
      search_input: false,
      visibleWaiter: false,
      visibleConfirmation: false,
      visibleOnlinePayment: false,
      search: '',
      active: {
        dat_drawer: false,
      },
      product: null,
      products: [
        {
          id: 1,
          title: "ЛУКОВЫЙ КОРОЛЬ",
          price: 299,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require("../assets/img/1.svg"),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 2,
          title: "ТОМАТНЫЙ КОРОЛЬ",
          price: 299,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require("../assets/img/2.svg"),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 3,
          title: 'СЫРНЫЙ КОРОЛЬ',
          price: 299,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require('../assets/img/1.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 4,
          title: 'ОСТРОЕ БЕЗУМИЕ',
          price: 299,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require('../assets/img/2.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 5, 
          title: 'КОРОЛЕВСКАЯ КОЛА',
          price: 199,
          category: 'ФИРМЕННЫЕ НАПИТКИ',
          img: require('../assets/img/chivas.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 6, 
          title: 'ОСВЕЖАЮЩИЙ ДЖУС',
          price: 199,
          category: 'ФИРМЕННЫЕ НАПИТКИ',
          img: require('../assets/img/orange.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 7, 
          title: 'КОРОЛЬ ВЕЧЕРИНОК',
          price: 399,
          category: 'ФИРМЕННЫЕ НАПИТКИ',
          img: require('../assets/img/svag.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 8,
          title: 'ФИРМЕННЫЙ СЫРНЫЙ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 9,
          title: "СОЧНЫЙ КОРОЛЬ",
          price: 199,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require("../assets/img/1.svg"),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 10,
          title: "ДВОЙНОЙ ТОМАТНЫЙ",
          price: 399,
          category: "ФИРМЕННЫЕ БУРГЕРЫ",
          img: require("../assets/img/2.svg"),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 650 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 11,
          title: 'ФИРМЕННЫЙ КЕТЧУП',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 12,
          title: 'ФИРМЕННЫЙ 1000 ОСТРОВОВ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 13,
          title: 'ФИРМЕННЫЙ ЧЕСНОЧНЫЙ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 14,
          title: 'ФИРМЕННЫЙ БАРБЕКЮ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 15,
          title: 'ВКУСНЫЙ КИСЛО-СЛАДКИЙ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 16,
          title: 'ЖГУЧИЙ ЧИЛИ',
          price: 60, 
          category: 'ФИРМЕННЫЕ СОУСЫ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
        {
          id: 17,
          title: 'ФИРМЕННЫЙ ХАЧАПУРИ',
          price: 60, 
          category: 'ФИРМЕННЫЕ ХАЧАПУРИ',
          img: require('../assets/img/sous.svg'),
          description: "Лучший из классических бургеров ресторана",
          sostav: "Состав: булка, мясо, салат, помидоры",
          weight: "Вес: 450 гр",
          calorii: "Каллорийность: 1200 ККал"
        },
      ],
      categoryArr: [
        {
          id: 1,
          title: 'ФИРМЕННЫЕ БУРГЕРЫ',
        },
        {
          id: 2,
          title: 'ФИРМЕННЫЕ НАПИТКИ',
        },     
        {
          id: 3, 
          title: 'ФИРМЕННЫЕ СОУСЫ',
        },
        {
          id: 3, 
          title: 'ФИРМЕННЫЕ ХАЧАПУРИ',
        }
      ],
    };
  },
  mounted() {
    window.addEventListener("scroll", this.scrollHandler);
  },
  methods: {
    myCatEmit(cat) {
      document.querySelectorAll('.section').forEach((elem) => {
        if (cat.title == elem.querySelector('.title-top').outerText) {
          window.removeEventListener('scroll', this.scrollHandler, false);
          document.documentElement.scrollTop = elem.offsetTop - 80
          window.addEventListener("scroll", this.scrollHandler);
        }
      })
    },
    scrollHandler() {
      const headerFixed = document.querySelector(".fixed-header").offsetHeight;
      const mainMenu = document.querySelector(".header-ul");
      document.querySelectorAll(".section").forEach((elem) => {
        const top = elem.offsetTop - headerFixed - 33;
        if (window.scrollY > top) {
          const titleSection = elem.querySelector(".title-top").outerText;
          document.querySelectorAll(".cat-link").forEach((elem) => {
            if (elem.outerText == titleSection) {
              elem.classList.add("active");
              mainMenu.scrollLeft =
                document.querySelector(".active").offsetLeft - 80;
            } else {
              elem.classList.remove("active");
            }
          });
        }
      });
    },
    searchControl() {
      this.search_input = true;
      this.visibleCategory = false;
      this.visibleSlider = false
      document.querySelector('.menu-list').style = "margin-top: 100px;"
    },
    closeSearch() {
      this.search_input = false;
      this.visibleCategory = true;
      this.visibleSlider = true
      this.search = '';
      document.querySelector('.menu-list').style = "margin-top: 0"
    },
    viewProduct(product) {
      this.product = product;
      this.active.dat_drawer = true;
      this.$store.state.activeCard = true;
      console.log(this.product);
      document.body.style = 'overflow: hidden;'
    },
    closeProductDrawer() {
      this.$store.state.activeCard = false;
      this.active.dat_drawer = false;
    },
    openCart() {
      this.visibleCart = !this.visibleCart
      document.body.style = 'overflow: auto;'
      this.$store.state.activeCard = false;
      if (this.visibleCart == true) {
        document.body.style = 'overflow: hidden;'
      }
    },
    openWaiter() {
      this.visibleWaiter = !this.visibleWaiter
      document.body.style = 'overflow: auto;'
      if (this.visibleWaiter == true) {
        document.body.style = 'overflow: hidden;'
      }
    },
    openConfirmation() {
      this.visibleConfirmation = !this.visibleConfirmation
      document.body.style = 'overflow: auto;'
      this.visibleWaiter = false
      if (this.visibleConfirmation) {
        document.body.style = 'overflow: hidden;'
      }
    },
    openOnlinePayment() {
      this.visibleOnlinePayment = !this.visibleOnlinePayment
    },
    async getMenu() {
      await axios.get('http://director-kab.ru.local/api/menu')
      .then((response) => {
        this.products = response.data.products
        this.categoryArr = response.data.categories
      })
      .catch((err) => {
        console.log(err);
      });
    },
  },
  created() {
    this.getMenu()
  }
};
</script>

<style>

</style>