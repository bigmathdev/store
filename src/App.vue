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
            <button @click.stop="product.quantity--" :disabled="product.quantity <= 1">-</button>
            <span class="quantity">{{ product.quantity }}</span>
            <button @click.stop="product.quantity++">+</button>
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
          <tr v-for="product in carrinho">
            <td>{{ product.quantidade + 'x ' + product.nome  }}</td>
            <td>{{ product.quantidade * product.preco }}</td>
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
import { onMounted, ref, computed } from 'vue'
import axios from "axios";

const products = ref(undefined)
const quantity = ref(1)

const storeAPI = async () => {
  const response = await axios.get('https://fakestoreapi.com/products')
  products.value = response.data
  products.value.forEach(i => {
    i.quantity = 0
  })
  console.log(response)
  return response
}

const carrinho = computed(() => {
  return products.value?.filter(f => f.quantity > 0).map(m => {
    return {
      id: m.id,
      nome: m.title,
      preco: m.price,
      quantidade: m.quantity
    }
  })
})

onMounted(() => {
  storeAPI()
})
</script>

<style></style>