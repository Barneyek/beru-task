<template>
  <button v-if="singleProduct" @click="showModal = true">Edit product</button>
  <button v-else @click="showModal = true">Add product</button>
  <!-- overlay -->
  <div class="overlay" v-if="showModal" @click="showModal = false"></div>

  <teleport to="body">
    <div class="modal" v-if="showModal">
      <div class="modal-container">
        <button class="close" @click="showModal = false">x</button>
        <h2 v-if="singleProduct">Edit product</h2>
        <h2 v-else>New product</h2>
        <form @submit="handleSubmitForm" class="form" ref="formRef" >
          <div>
            <label for="title">Title:</label>
            <input type="text" v-model="form.title" id="title">
          </div>
          <div>
            <label for="price">Price:</label>
            <input type="number" v-model="form.price" id="price">
          </div>
          <div>
            <label for="description">Description:</label>
            <textarea v-model="form.description" id="description" cols="5" rows="2"></textarea>
          </div>
          <div>
            <label for="category">Category:</label>
            <select v-model="form.categoryId" id="category">
              <option :value="1">Category 1</option>
              <option :value="2">Category 2</option>
              <option :value="3">Category 3</option>
            </select>
          </div>
          <div>
            <label for="images">Add url image:</label>
            <input type="text" v-model="form.images[0]" id="images">
          </div>
          <div class="btn-wrapper">
            <button v-if="singleProduct" type="submit">Edit Product</button>
            <button v-else type="submit">Add Product</button>
          </div>
        </form>
      </div>
    </div>
  </teleport>

</template>

<script setup lang="ts">
import {ref, reactive, watch, PropType} from "vue";
import axios from "axios";
import { v4 as uuidv4 } from 'uuid';
import { Product } from '@/types/Types';


const props = defineProps({
  singleProduct: {
    type: Object as PropType<Product>,
  },
});

const API = import.meta.env.VITE_API_URL;
const showModal = ref<boolean>(false);
const formRef = ref();
const form = reactive({
  id: props.singleProduct ? props.singleProduct?.id : uuidv4(),
  title: "",
  price: null,
  description: "",
  categoryId: "1",
  images: [],
});
const emits = defineEmits(["refresh-product"]);

watch(showModal, () => {
  if (showModal.value && props.singleProduct) {
    Object.assign(form, props.singleProduct);
  }
});

const handleSubmitForm = async (event) => {
  event.preventDefault();
  try {
    if (!props.singleProduct) {
      await axios.post(API, form);
      alert('Product added');
    } else {
      await axios.put(`${API}/${form.id}`, form);
      alert('Product edited');
    }
    emits("refresh-product");
    showModal.value = false;
  } catch (error) {
    console.error(`Error ${props.singleProduct ? 'editing' : 'adding'} product:`, error);
  }
};

</script>
<style lang="scss" scoped>
#app .overlay {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
}

.modal {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-container {
  position: relative;
  width: 500px;
  padding: 20px 30px;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input, textarea, select {
  margin: 10px 0;
  padding: 15px 10px;
  width: 100%;
  outline: none;
  border: 1px solid #bbb;
  border-radius: 20px;
  display: inline-block;
  box-sizing: border-box;
  transition: 0.2s ease all;
}

form input[type=text]:focus,
form input[type="password"]:focus {
  border-color: cornflowerblue;
}

.btn-wrapper {
  display: flex;
  justify-content: flex-end;
}

</style>
