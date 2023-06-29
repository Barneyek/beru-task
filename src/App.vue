<template>
  <div class="section">
    <ProductsList
      v-if="products && !loading"
      :products="products"
      @remove-product="removeProduct"
      @fetch-products="fetchProducts" />
    <span v-else-if="!products && !loading">No products</span>
    <div v-else-if="loading" id="loading"></div>
    <div class="select-bottom">
      <label for="category">Rows per page:</label>
      <select v-model="pagination.limit" id="category" @change="fetchProducts" class="rows-per-page">
        <option :value="5">5</option>
        <option :value="15">15</option>
        <option :value="20">20</option>
      </select>
    </div>
  </div>
</template>
<script setup lang="ts">
import ProductsList from './components/Products.vue'
import {onBeforeMount, reactive, ref} from "vue";
import axios from "axios";
import { Product } from '@/types/Types';

interface Pagination {
  limit: number;
  offset: number;
}

const pagination = reactive<Pagination>({
  limit: 5,
  offset: 0,
});

const loading = ref<boolean>(false);
const products = ref<Product | null>(null);
const API = import.meta.env.VITE_API_URL;

onBeforeMount(async () => {
  await fetchProducts();
});

const fetchProducts = async () => {
  loading.value = true;
  axios.get(`${API}?limit=${pagination.limit}&offset=${pagination.offset}`)
    .then(response => {
      products.value = response.data;
      loading.value = false;
    })
    .catch(error => {
      console.error(error);
    });
};

const removeProduct = async(id: number) => {
  // https://api.escuelajs.co/docs#/products/ProductsController_delete
  // I think something doesn't work in swagger, so magic won't work here, unfortunately
  try {
    await axios.delete(`${API}/${id}`);
    await fetchProducts();
    alert("Deleted!")
  } catch (error) {
    console.error(error);
  }
}
</script>

<style lang="scss" scoped>
#loading {
  display: inline-block;
  width: 50px;
  height: 50px;
  border: 3px solid rgba(0,0,0,.3);
  border-radius: 50%;
  border-top-color: #000;
  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to { -webkit-transform: rotate(360deg); }
}
@-webkit-keyframes spin {
  to { -webkit-transform: rotate(360deg); }
}

.rows-per-page {
  cursor: pointer;
}
.select-bottom {
  margin-top: 20px;
  display: flex;
  gap: 5px;
  justify-content: flex-end;
}
</style>
