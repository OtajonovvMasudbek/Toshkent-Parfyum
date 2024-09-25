<template>
    <div class="relative w-full">
      <div class="flex items-center border rounded p-2 w-full">
        <input
          v-model="searchTerm"
          type="search"
          placeholder="Qidirish"
          @input="searchProducts"
          class="flex-1 outline-none"
        />
        <button v-if="searchTerm" @click="clearSearch" class="ml-2 text-gray-500">
          &#x2715;
        </button>
      </div>
  
      <ul
        class="absolute left-0 w-full mt-2 bg-white border rounded shadow-lg max-h-60 overflow-y-auto z-10"
      >
      <pre>{{ regex }}</pre>

        <li
          v-for="product in filteredProducts"
          :key="product.id"
          class="flex items-center p-2 hover:bg-gray-100 cursor-pointer"
        >
          <img :src="product.image" alt="Product" class="w-8 h-8 mr-2" />
          <span v-html="highlightText(product.title)"></span>
        </li>
      </ul>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const searchTerm = ref('');
  const results = ref([]);
  const filteredProducts = ref([]);
  
  const fetchProducts = async (term = '') => {
    try {
      const response = await axios.get(`https://toshkent-parfum.uz/api/v1/products/?search=${term}&page=1&size=10`);
      results.value = response.data.data; 
      filteredProducts.value = results.value; 
    } catch (error) {
      console.error('Error fetching products:', error);
    }
  };
  
  const searchProducts = () => {
    if (searchTerm.value) {
      fetchProducts(searchTerm.value);
    } else {
      filteredProducts.value = [];
    }
  };
  
  const clearSearch = () => {
    searchTerm.value = '';
    filteredProducts.value = [];
  };
  
  const highlightText = (text) => {

      const regex = new RegExp(`(${searchTerm.value})`, 'gi');
      return text.replace(regex, `<mark class="bg-yellow-300">$1</mark>`);
    };
  
  fetchProducts();
  </script>
  