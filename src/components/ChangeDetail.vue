<template>
  <div class="change-detail-container">

    <h2 class="title">Detalle de Cambios</h2>

    <!-- Campo de búsqueda -->
    <div class="search-container">
      <input type="text" v-model="searchTerm" placeholder="Buscar ciudad...">
    </div>
    <!-- Listado de ciudades filtradas -->
    <ul class="city-list">
      <li v-for="(city, index) in filteredCities" :key="city.id" class="city-item" :class="{ 'separator': index !== filteredCities.length - 1 }" @click="selectCity(city)">
        <span class="city-name">{{ city.name }}</span>
        <span class="city-cost">{{ city.cost }}</span>
      </li>
    </ul>

    <!-- Etiqueta para mostrar la ciudad seleccionada -->
    <div class="selected-city" v-if="selectedCity">
      <label>Ciudad Seleccionada:</label>
      <span>{{ selectedCity.name }}</span>
    </div>

    <!-- Input para la cantidad de pasajes vendidos -->
    <div class="ticket-input" v-if="selectedCity">
      <label for="ticketQuantity">Cantidad de Pasajes Vendidos:</label>
      <input type="number" id="ticketQuantity" v-model.number="selectedCity.quantity" min="1">
    </div>

    <!-- Botón para agregar al carrito -->
    <button @click="addToCart" v-if="selectedCity" class="add-to-cart-button">Agregar al Carrito</button>

    <!-- Carrito de compras -->
    <div class="cart" v-if="cart.length > 0">
      <h3>Carrito de Compras</h3>
      <ul>
        <li v-for="(item, index) in cart" :key="index">
          <span>{{ item.city.name }} - Cantidad: {{ item.quantity }} </span>
          <span> - Total: {{ item.total }}</span>
          <button @click="removeFromCart(index)" class="remove-from-cart-button">Eliminar</button>
        </li>
      </ul>
      <p class="total-purchase-label" v-if="totalPurchase > 0">Total de la Compra: {{ totalPurchase }}</p>
    </div>


    <!-- Input para el pago del cliente -->
    <div class="payment-input">
      <label for="payment">Pago del Cliente:</label>
      <input type="number" id="payment" v-model="paymentAmount">
    </div>

    <!-- Etiqueta para mostrar el total de la compra -->
    <div class="total-purchase" v-if="selectedCity">
      <label for="totalPurchase">Total de la Compra:</label>
      <span>{{ totalTicketsCost }}</span>
    </div>

    <!-- Etiqueta para mostrar el cambio a devolver al cliente -->
    <div class="change-label" v-if="cart.length > 0 && paymentAmount > 0">
      <label for="change">Cambio a Devolver al Cliente:</label>
      <span>{{ changeToReturn }}</span>
    </div>



  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface City {
  id: number;
  name: string;
  cost: number;
  quantity: number; // Nueva propiedad para almacenar la cantidad de pasajes vendidos
}

interface CartItem {
  city: City;
  quantity: number;
  total: number;
}

export default defineComponent({
  name: 'ChangeDetail',
  data() {
    return {
      cities: [
        { id: 1, name: 'Km 531', cost: 1130, quantity: 1 },
        { id: 2, name: 'Devoto', cost: 1360, quantity: 1 },
        { id: 3, name: 'Jeanmaire', cost: 1740, quantity: 1 },
        { id: 4, name: 'La Francia', cost: 2310, quantity: 1 },
        { id: 5, name: 'Km 581', cost: 2650, quantity: 1 },
        { id: 6, name: 'El Tío', cost: 3060, quantity: 1 },
        { id: 7, name: 'El Fuertecito', cost: 3430, quantity: 1 },
        { id: 8, name: 'Arroyito', cost: 3750, quantity: 1 },
        { id: 9, name: 'Puente del Río Segundo', cost: 3570, quantity: 1 },
        { id: 10, name: 'Tránsito', cost: 3790, quantity: 1 },
        { id: 11, name: 'Santiago Temple', cost: 4840, quantity: 1 },
        { id: 12, name: 'Pedro Vivas', cost: 4900, quantity: 1 },
        { id: 13, name: 'Río Primero', cost: 5190, quantity: 1 },
        { id: 14, name: 'K711 Malvinas Argentinas', cost: 6770, quantity: 1 },
        { id: 15, name: 'Córdoba', cost: 7920, quantity: 1 },
      ] as City[],
      paymentAmount: 0,
      selectedCity: null as City | null,
      searchTerm: '', // Nueva propiedad para el término de búsqueda
      cart: [] as CartItem[], // Array para almacenar los elementos seleccionados en el carrito
    };
  },
  computed: {
    filteredCities(): City[] {
      // Filtrar ciudades basado en el término de búsqueda
      return this.cities.filter(city =>
        city.name.toLowerCase().includes(this.searchTerm.toLowerCase())
      );
    },

    totalTicketsCost(): number {
      // Calcular el monto total de la compra de pasajes
      return this.selectedCity ? this.selectedCity.cost * this.selectedCity.quantity : 0;
    },

    totalPurchase(): number {
      // Calcular el total de la compra sumando los totales de cada elemento en el carrito
      return this.cart.reduce((total, item) => total + item.total, 0);
    },

    changeToReturn(): number | null {
      // Verificar si el carrito tiene algún elemento añadido
      if (this.cart.length === 0) {
        return null; // Si el carrito está vacío, no se puede calcular el cambio
      }

      // Calcular el total de la compra sumando los totales de cada elemento en el carrito
      const totalPurchase = this.cart.reduce((total, item) => total + item.total, 0);

      // Calcular el cambio a devolver al cliente
      return this.paymentAmount - totalPurchase;
    },

  },
  methods: {
    selectCity(city: City) {
      this.selectedCity = city;
    },

    addToCart() {
      // Verificar si la ciudad ya está en el carrito
      const existingItemIndex = this.cart.findIndex(item => item.city.id === this.selectedCity!.id);

      if (existingItemIndex !== -1) {
        // Si la ciudad ya está en el carrito, actualizar la cantidad y el total
        this.cart[existingItemIndex].quantity = this.selectedCity!.quantity;
        this.cart[existingItemIndex].total = this.selectedCity!.quantity * this.selectedCity!.cost;
      } else {
        // Si la ciudad no está en el carrito, añadirla
        this.cart.push({
          city: this.selectedCity!,
          quantity: this.selectedCity!.quantity,
          total: this.selectedCity!.quantity * this.selectedCity!.cost,
        });
      }

      // Reiniciar la selección de ciudad
      this.selectedCity = null;
    },

    removeFromCart(index: number) {
      this.cart.splice(index, 1); // Eliminar el elemento del carrito en la posición indicada
    },
  },
});
</script>

<style scoped>
/* Estilos mejorados para el componente de detalle de cambios */
body {
  font-family: 'Open Sans', Arial, sans-serif; /* Fuente Open Sans para todo el documento */
}

.change-detail-container {
  max-width: 80%;
  margin: 0 auto;
  padding: 20px;
  background-color: #005a3c; /* Verde corporativo de Fono Bus */
  border-radius: 10px;
  color: #ffffff; /* Letras blancas */
}

.title {
  margin-bottom: 20px;
}

.city-list {
  list-style-type: none;
  padding: 0;
}

.city-item {
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.3s;
  background-color: #ffffff; /* Fondo blanco */
  color: #000000; /* Letras negras */
}

.city-item.separator {
  border-bottom: 1px solid #00a957; /* Verde corporativo de Fono Bus */
}

.city-item:hover {
  background-color: #e7f7ef; /* Verde más claro para resaltar al hacer hover */
}

.city-name {
  font-weight: bold;
}

.city-cost {
  margin-left: 10px;
}

/* Estilos para el input de pago del cliente */
.payment-input {
  margin-top: 20px;
}

.payment-input label {
  font-weight: bold;
}

.payment-input input {
  margin-top: 5px;
  padding: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

/* Estilos para la etiqueta de cambio a devolver */
.change-label {
  margin-top: 20px;
}

.change-label label {
  font-weight: bold;
}

.change-label span {
  display: block;
  margin-top: 5px;
}

/* Estilos para la sección de ciudad seleccionada */
.selected-city {
  margin-top: 20px;
}

.selected-city label {
  font-weight: bold;
}

.selected-city span {
  display: block;
  margin-top: 5px;
}

.search-container {
  margin-bottom: 20px;
}

.search-container input {
  width: 100%;
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

/* Estilos para el botón "Agregar al Carrito" */
.add-to-cart-button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #007bff; /* Azul */
  color: #ffffff; /* Letras blancas */
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.add-to-cart-button:hover {
  background-color: #0056b3; /* Azul más oscuro al hacer hover */
}

/* Estilos para el botón "Eliminar" del carrito */
.remove-from-cart-button {
  margin-left: 10px;
  padding: 5px 10px;
  background-color: #dc3545; /* Rojo */
  color: #ffffff; /* Letras blancas */
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.remove-from-cart-button:hover {
  background-color: #bd2130; /* Rojo más oscuro al hacer hover */
}

/* Estilos para el carrito de compras */
.cart {
  margin-top: 20px;
  padding: 20px;
  background-color: #f0f0f0; /* Fondo gris claro */
  border-radius: 5px;
  color: #000000; /* Letras negras */
}

.cart h3 {
  margin-bottom: 10px;
}

.cart ul {
  list-style-type: none;
  padding: 0;
}

.cart li {
  margin-bottom: 10px;
}

/* Estilos para el elemento del "Total de la Compra" en el carrito */
.total-purchase-label {
  font-weight: bold;
  font-size: 18px; /* Tamaño de fuente más grande */
  color: #007bff; /* Color azul para resaltar */
}
</style>
