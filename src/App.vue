<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import GuitarraList from "./components/GuitarraList.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito,()=>{
    carritoStorage()
},{
    deep:true
})

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];

  const storage = localStorage.getItem("carrito");
  if (storage) {
    carrito.value = JSON.parse(storage);
  }
});

const carritoStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex((p) => p.id === guitarra.id);
  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }

};

const decrement = (id) => {
  const index = carrito.value.findIndex((p) => p.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};
const increment = (id) => {
  const index = carrito.value.findIndex((p) => p.id === id);
  if (carrito.value[index].cantidad >= 10) return;
  carrito.value[index].cantidad++;
};
const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((p) => p.id !== id);
};

const eliminarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @incrementar-cantidad="increment"
    @decrementar-cantidad="decrement"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @eliminar-carrito="eliminarCarrito" />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>
    <div class="row mt-5">
      <GuitarraList
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito" />
    </div>
  </main>
  <Footer />
</template>
