<script>
import ProductDescription from "@/components/product/ProductDescription.vue";
import Manufacturer from "@/components/product/Manufacturer.vue";
import ProductReviews from "@/components/product/ProductReviews.vue";
import Breadcrumbs from "@/components/nav/Breadcrumbs.vue";
import { Icon } from '@iconify/vue2';
import ProductSize from "../components/size/ProductSize.vue";

import { mapActions, mapGetters } from "vuex";

export default {
  name: "ProductDetails-page",
  components: {
    ProductDescription,
    Manufacturer,
    ProductReviews,
    Breadcrumbs,
    Icon,
    ProductSize,
  },

  data() {
    return {
      id: this.$route.params.id,
      tabIndex:0,
      category: null,
      inputValue: 1,
      size: 7,
      cartObj: {},
      loading: true,
    }
},

  methods: {
    linkClass(idx) {
      if (this.tabIndex === idx) {
        return ['tabSelected', 'text-light']
      } else {
        return ['bg-secondary', 'text-dark']
      }
    }, 

    ...mapActions([
      'getProductDetails',
      'addToCart'
    ]),

    TotalPrice() {
     return this.product.price * this.inputValue;
    },

    handleDecrement() {
      if(this.inputValue > 1) {
        this.inputValue--;
      }
    },

    handleIncrement() {
      this.inputValue++;
    },

    handleSetSize(ItemSize) {
      console.log("size: ", ItemSize);
      this.size = ItemSize;
    },

    handleChange(e) {
      console.log(e.target.value, "changed")
      this.inputValue = e.target.value;

    },

    updateCart() {
      const totalPrice = this.TotalPrice();
      this.cartObj = { ...this.cartObj,
        size: this.size,
        totalPrice: totalPrice,
        count: this.inputValue,
        price: this.product.price,
        title: this.product.title,
        img: this.product.image,
        id: this.product.id,
      }
      this.addToCart(this.cartObj);
      // resets
      this.inputValue = 1;
    }
  },

  computed: {
    ...mapGetters([
      'product'
    ]),

  },

  created() {
    this.getProductDetails(this.id);
  },

}
</script>

<template>
<div class="customContainer">
  <Breadcrumbs title="product details" />
  <div class="sectionPadding">
    <!-- Product View -->
  <div class="sectionPaddingBottom">
  <div class="row g-0 justify-content-center">
<div class="col-12 col-md-8 col-lg-7 col-xl-8 pe-md-3 pe-xl-5">
  <div class="productImage border">
    <img :src="product.image" alt="product" />
  </div>
</div>
<!-- Product Details -->
<div class="col-12 col-md-4 col-lg-5 col-xl-4 mt-4 mt-md-0">
  <section class="productDetails">
    <h3>{{ product.title }}</h3>
    <div class="detailPrice">
      <p>&dollar;{{ product.price }}</p>
      <span class="me-1"><Icon icon="bi:star-fill" /></span>
      <span class="me-1"><Icon icon="bi:star-fill" /></span>
      <span class="me-1"><Icon icon="bi:star-fill" /></span>
      <span class="me-1"><Icon icon="bi:star-fill" /></span>
      <span class="me-1"><Icon icon="bi:star" /></span>
      <span class="me-1 reviewText">&#40;{{ product.rating.count }} Rating&#41;</span>
  </div>
  <p class="detailDescription">
    {{ product.description   }}
  </p>
  </section>
    <!-- sizes -->
  <h4 class="detailsSmallHeadings">Size</h4>
  <ProductSize @setSize="handleSetSize" />
    <!-- width -->
  <h4 class="detailsSmallHeadings">Width</h4>
  <div class="widthContainer row g-0 mb-4">
    <div class="col-auto">M</div>
    <div class="col-auto">W</div>
  </div>
    <!-- quantity -->
    <div class="row g-0 quantityContainer align-items-center mb-4">
      <div class="col-auto">
        <button @click="handleDecrement">
          -
        </button>
      </div>
      <div class="col itemQuantity">
        <input min="1" type="number" name="itemQuantity" :value="inputValue" @change="handleChange" />
      </div>
      <div class="col-auto">
        <button @click="handleIncrement">
          +
        </button>
      </div>
    </div>
    <!-- <router-link to="/cart"> -->
  <button class="detailsBtn" @click="updateCart"><span><Icon icon="ion:cart-outline" /></span> Add to Cart</button>
<!-- </router-link> -->
</div>
    </div>
  </div>
  <!-- Reviews and Description -->
  <div class="productDetailsContainer">
    <b-card no-body>
      <b-tabs v-model="tabIndex" card align="left">
        <b-tab title="description" :title-link-class="linkClass(0)"><ProductDescription /></b-tab>
        <b-tab title="manufacturer" :title-link-class="linkClass(1)"><Manufacturer /></b-tab>
        <b-tab title="review" :title-link-class="linkClass(2)"><ProductReviews /></b-tab>
      </b-tabs>
    </b-card>
  </div>
  </div>
</div>
</template>

<style scoped>
@import "./productDetails.css";
</style>