<template>
  <div>
    <VueHeader></VueHeader>
    <main>
      <div
        id="banner"
        style="background-image: url(https://c.pxhere.com/photos/5d/b4/kitchen_inside_modern_furniture_contemporary_domestic_lifestyle_faucet-1248463.jpg!d"
      >
        <h2>
          所有產品
          <span class="subtitle">All Products</span>
        </h2>
      </div>
      <div id="products">
        <div class="row mt-4 wrapper">
          <div
            class="item col-md-3"
            v-for="item in products"
            :key="item.id"
            @click="getProduct(item.id)"
          >
            <div class="photo">
              <img :src="item.imageUrl" />
            </div>
            <div class="category text-center">{{item.category}}</div>
            <div class="title text-center">{{ item.title }}</div>
            <div class="price text-center">NT ＄{{ item.price }}</div>
          </div>
        </div>
      </div>
    </main>
    <VueFooter></VueFooter>
    <div id="pm" v-if="pm">
      <div id="pm_content">
        <div class="shut" @click="shut()"></div>
        <h3 class="title">{{ product.title }}</h3>
        <div class="photo">
          <img :src="product.imageUrl" />
        </div>
        <div class="category">分類：{{ product.category }}</div>
        <div class="origin_price">原價：{{ product.origin_price }}</div>
        <div class="price">售價：{{ product.price }}</div>
        <div class="qty">
          <label for="select">購買數量：</label>
          <select id="select" v-model="defaultNum">
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
            <option value="4">4</option>
            <option value="5">5</option>
          </select>
        </div>
        <button
          type="button"
          @click="addtoCart"
          class="btn btn-primary btn-lg btn-block"
        >
          加入購物車
        </button>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-unused-vars */
/* eslint-disable vue/no-unused-components */
import VueHeader from "./shared/header";
import VueFooter from "./shared/footer";

export default {
  components: {
    VueHeader,
    VueFooter
  },
  data() {
    return {
      productsUser: [],
      product: {},
      defaultNum: 1,
      pm: false,
      status: {
        loadingItem: ""
      }
    };
  },
  computed: {
    products: function() {
      return this.productsUser
        .filter(product => product.is_enabled === "1")
    }
  },
  methods: {
    getProducts() {
      const url = `https://vue-course-api.hexschool.io/api/han_vue/products/all`;
      this.$http.get(url).then(res => {
        this.productsUser = res.data.products;
        //console.log(this.productsUser);
      });
    },
    shut() {
      this.pm = false;
      this.product = "";
    },
    getProduct(id) {
      //抓取單一資料
      const url = `https://vue-course-api.hexschool.io/api/han_vue/product/${id}`;
      this.$http.get(url).then(res => {
        this.product = res.data.product;
        //console.log(res);
        this.pm = true;
      });
    },
    addtoCart(id, qty = "1") {
      const self = this;
      const url = `https://vue-course-api.hexschool.io/api/han_vue/cart`;
      self.status.loadingItem = this.product.id;
      const cart = {
        product_id: this.product.id,
        qty: this.defaultNum
      };
      self.$http.post(url, { data: cart }).then(res => {
        //console.log(res);
        self.shut();
        self.pm = false;
        self.product = "";
        self.$swal("完成了!", "商品已加入購物車", "success");
        this.defaultNum= 1;
        this.status.loadingItem = "";
      });
    }
  },
  created() {
    this.getProducts();
  }
};
</script>

<style lang="scss">
#banner {
  margin-top: 90px;
  height: 450px;
  background-position: 50%;
  background-size: cover;
  position: relative;
  z-index: 1;
  h2 {
    color: #fff;
    position: absolute;
    width: 100%;
    text-align: center;
    top: 50%;
    transform: translateY(-50%);
    font-size: 36px;
    font-family: "Noto Serif SC", serif;
    font-weight: 700;
    text-shadow: black 0.1em 0.1em 0.5em;
    .subtitle {
      width: 100%;
      display: block;
      font-family: "Noto Serif SC", serif;
      font-size: 18px;
      font-weight: 900;
      color: #d70545;
      margin-top: 10px;
      padding-top: 10px;
      &::before {
        content: "";
        display: block;
        width: 150px;
        height: 2px;
        background-color: #fff;
        position: absolute;
        top: 53px;
        left: 0;
        right: 0;
        margin: auto;
      }
    }
  }
}

#products {
  padding: 30px;
  .item {
    cursor: pointer;
    padding: 30px 30px 30px 30px;
    position: relative;
    .photo {
      img {
        max-width: 100%;
      }
    }
    .category {
      font-size: 14px;
      color: #a1a1a1;
      padding: 10px 0;
    }
    .title {
      font-family: "Roboto", "Pacifico", "Noto Serif SC", serif;
      font-size: 16px;
      font-weight: 600;
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
    .price {
      padding-top: 10px;
      font-size: 20px;
      font-weight: bolder;
      color: #ff0000;
    }
    &::before {
      content: "";
      display: block;
      position: absolute;
      width: 100%;
      height: 100%;
      margin: -30px;
      background-color: rgba(0, 0, 0, 0.6);
      opacity: 0;
      transition: 0.6s;
    }
    &::after {
      content: "查看產品詳細";
      display: block;
      position: absolute;
      width: 150px;
      height: 150px;
      line-height: 150px;
      background-color: #d70445;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      margin: auto;
      border-radius: 120px;
      opacity: 0;
      transition: 1.5s;
      text-align: center;
      color: #fff;
      font-size: 18px;
      font-family: "Noto Serif SC", serif;
      font-weight: 700;
    }
    &:hover {
      &::before {
        opacity: 1;
      }
      &::after {
        opacity: 1;
      }
    }
  }
}
</style>