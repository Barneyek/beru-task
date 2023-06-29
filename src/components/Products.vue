<template>
  <div class="products-title mb-4">
    <h2>Products List:</h2>
    <ManageProduct @refresh-product="$emit('fetch-products')"/>
  </div>
  <!-- Products -->
  <div class="products-list">
    <div
      v-for="(el, index) in products"
      :key="index"
      class="product"
    >
      <div class="product-left">
        <div class="product-img">
          <a href="#">
            <img :src="el.images[0]" :alt="el.title"/>
          </a>
        </div>
        <div class="product-title">
          <h6 class="font-weight-medium">
            <a href="#">{{ el.title }}</a>
          </h6>
          <div class="price-and-cart">
            <div class="price">
              <span>${{ el.price }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="product-right">
        <ManageProduct :single-product="el" @refresh-product="$emit('fetch-products')"/>
        <button class="product-remove" @click="$emit('remove-product', el.id)">
          Remove
        </button>
      </div>
    </div>
  </div>
  <!-- end row -->
</template>
<script setup lang="ts">
import ManageProduct from "@/components/Manage-Product.vue";

defineProps({
  products: {
    type: Array,
    required: true,
  },
});

defineEmits(['fetch-products', 'remove-product']);
</script>

<style lang="scss" scoped>
.products-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.products-list {
  display: flex;
  align-items: center;
  width: 100%;
  gap: 10px;
  flex-wrap: wrap;
}

.product {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;

  &:not(:last-child) {
    padding-bottom: 10px;
    border-bottom: 1px solid #ccc;
  }

  &-left,
  &-right {
    display: flex;
    align-items: center;
    gap: 20px;
  }

  &-title {
    display: flex;
    align-items: center;
    gap: 20px;
    h6 {
      margin: 0;
    }
     a {
      font-size: 20px;
       color: #1a1a1a;
    }
  }
}

.product-img {
  max-width: 70px;

  img {
    max-width: 100%;
    height: auto;
  }
}
</style>
