<template>
  <main id="app">
    <section class="items">
      <h4>Selecione os produtos</h4>
      <div v-for="product in products" @click="product.active = !product.active" class="product"
        :class="{ selected: product.active }">
        <div class="photo">
          <img :src="product.image">
        </div>
        <div class="description">
          <span class="name">{{ product.title }}</span>
          <span class="price">R${{ product.price }}</span>
          <div class="quantity-area" v-if="product.active">
            <button @click.stop="quantity--" :disabled="quantity <= 1">-</button>
            <span class="quantity">{{ quantity }}</span>
            <button @click.stop="quantity++">+</button>
          </div>
        </div>
      </div>
    </section>

    <section class="summary">
      <strong>Resumo do pedido</strong>
      <table>
        <thead>
          <tr>
            <th>Item</th>
            <th>Total</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>1x </td>
            <td>5.99</td>
          </tr>

          <tr>
            <th>Total</th>
            <th>5.99</th>
          </tr>
        </tbody>
      </table>
    </section>

  </main>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from "axios";

const products = ref(undefined)
const quantity = ref(1)

const storeAPI = async () => {
  const response = await axios.get('https://fakestoreapi.com/products')
  products.value = response.data
  console.log(response)
  return response
}

onMounted(() => {
  storeAPI()
})
</script>

<style></style>