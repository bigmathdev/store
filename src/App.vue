<template>
  <main>
    <section class="items">
      <h4>Selecione os produtos</h4>
      <div v-for="product in products" @click="quantityPerPage(product)" class="product"
        :class="{ selected: product.active }">
        <div class="photo">
          <img :src="product.image" />
        </div>
        <div class="description">
          <span class="name">{{ product.title }}</span>
          <span class="price">{{
            new Intl.NumberFormat("pt-br", {
              style: "currency",
              currency: "BRL",
            }).format(product.price)
          }}</span>
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
          <tr v-for="product in cart">
            <td>{{ product.quantity + "x " + product.name }}</td>
            <td>
              {{
                new Intl.NumberFormat("pt-br", {
                  style: "currency",
                  currency: "BRL",
                }).format(product.quantity * product.price)
              }}
            </td>
          </tr>
          <tr>
            <th>Total</th>
            <th>{{ totalAmount }}</th>
          </tr>
        </tbody>
      </table>
    </section>
  </main>
</template>

<script setup>
import { onMounted, ref, computed } from "vue";
import axios from "axios";

const products = ref(undefined);

const storeAPI = async () => {
  const response = await axios.get("https://fakestoreapi.com/products");
  products.value = response.data;
  return response;
};

const cart = computed(() => {
  return products.value
    ?.filter((f) => f.quantity > 0 && f.active == true)
    .map((m) => {
      return {
        id: m.id,
        name: m.title,
        price: m.price,
        quantity: m.quantity,
        active: m.active,
      };
    });
});

const totalAmount = computed(() => {
  const total = cart.value
    ?.map((m) => m.quantity * m.price)
    .reduce((a, b) => a + b, 0);
  return new Intl.NumberFormat("pt-br", {
    style: "currency",
    currency: "BRL",
  }).format(total);
});

const quantityPerPage = (product) => {
  product.active = !product.active;
  if(product.active) {
    product.quantity = 1
  } else {
    product.quantity = 0
  }
}

onMounted(() => {
  storeAPI();
});
</script>

<style>
body {
  margin: 0;
  font-family: "Open Sans", sans-serif;
}

main>section.items h4 {
  text-align: center;
  margin-top: 0;
  width: 100%;
}

main {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
  padding-top: 20px;
}

main>section.items {
  display: flex;
  flex-wrap: wrap;
  border: 1px solid lightgrey;
  padding: 20px;
  max-width: 500px;
  min-width: 300px;
  justify-content: center;
}

section.items .product {
  border: 1px solid lightgrey;
  margin: 6px;
  flex: 0 0 calc(33.333% - 24px);
  cursor: pointer;
  text-align: center;
}

section.items .product.selected {
  border: 2px solid rgb(29, 134, 233);
}

section.items .photo img {
  max-width: 90px;
}

section.items .description {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  font-size: 11px;
  line-height: 15px;
}

section.items .description .price {
  font-weight: bold;
  margin: 4px auto;
}

section.items .description .quantity-area {
  margin: 8px auto;
  height: 14px;
}

section.items .description .quantity-area button {
  width: 16px;
  height: 16px;
  display: inline-flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

section.items .description .quantity-area .quantity {
  font-weight: bold;
  margin: 0 4px;
}

section.summary {
  background-color: rgb(245, 245, 245);
  padding: 20px;
  min-height: 200px;
  min-width: 200px;
  text-align: center;
}

section.summary table {
  width: 100%;
  padding-top: 12px;
  font-size: 11px;
  margin: auto;
}

section.summary table tbody tr:last-of-type th {
  border-top: 1px solid black;
  padding-top: 4px;
}
</style>
