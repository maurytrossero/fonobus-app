<template>
  <div class="change-detail-container">

    <h2 class="title">Venta de pasajes</h2>

    <!-- Campo de búsqueda -->
    <div class="search-container">
      <input type="text" v-model="searchTerm" placeholder="Buscar ciudad...">
    </div>

    <!-- Botón para mostrar u ocultar la sección de destinos -->
    <button @click="toggleCityList"  class="toggle-section-button">{{ showCityList ? 'Ocultar Destinos' : 'Mostrar Destinos' }}</button>

    <!-- Listado de ciudades filtradas -->
    <ul class="city-list" v-if="showCityList">
      <li v-for="(city, index) in filteredCities" :key="city.id" class="city-item" :class="{ 'separator': index !== filteredCities.length - 1 }" @click="selectCity(city)">
        <span class="city-name">{{ city.name }}</span>
        <span class="city-cost"> $ {{ city.cost }}</span>
      </li>
    </ul>


    <!-- Etiqueta para mostrar la ciudad seleccionada -->
    <div class="selected-city" v-if="selectedCity">
      <label>Ciudad Seleccionada:</label>
      <span>{{ selectedCity.name }}</span>
    </div>

    <!-- Input para la cantidad de pasajes vendidos -->
    <div class="ticket-input" v-if="selectedCity">
      <label for="ticketQuantity">Cantidad de Pasajes: </label>
      <input type="number" id="ticketQuantity" v-model.number="selectedCity.quantity" min="1">
    </div>

    <!-- Etiqueta para mostrar el total de la compra -->
        <div class="total-purchase" v-if="selectedCity">
      <label for="totalPurchase">Total: $ </label>
      <span>{{ totalTicketsCost }}</span>
    </div>

    <!-- Botón para agregar al carrito -->
    <button @click="addToCart" v-if="selectedCity" class="add-to-cart-button">Agregar al Carrito</button>

    <!-- Carrito de compras -->
    <div class="cart" v-if="cart.length > 0">
      <h3>Carrito de Compras</h3>
      <ul>
        <li v-for="(item, index) in cart" :key="index">
          <span>{{ item.city.name }} - Cantidad: {{ item.quantity }} </span>
          <span> - Total: $ {{ item.total }}</span>
          <button @click="removeFromCart(index)" class="remove-from-cart-button">Eliminar</button>
        </li>
      </ul>
      <p class="total-purchase-label" v-if="totalPurchase > 0">Total de la Compra: $ {{ totalPurchase }}</p>

    <!-- Botón para realizar la compra del carrito -->
    <button @click="completePurchase" v-if="cart.length > 0" class="complete-purchase-button">Comprar Carrito</button>

    </div>


    <!-- Input para el pago del cliente -->
    <div class="payment-input">
      <label for="payment">Pago del Cliente: $ </label>
      <input type="number" id="payment" v-model="paymentAmount">
    </div>

    <!-- Etiqueta para mostrar el cambio a devolver al cliente -->
    <div class="change-label" v-if="cart.length > 0 && paymentAmount > 0">
      <label for="change">Cambio para el Cliente: </label>
      <span>{{ changeToReturn }}</span>
    </div>

    <!-- Separador -->
    <div class="section-separator"></div>

    <!-- Botón para mostrar u ocultar la sección de compras -->
    <button @click="togglePurchasesSection" class="toggle-section-button">{{ showPurchasesSection ? 'Ocultar Ventas realizadas' : 'Mostrar Ventas realizadas' }}</button>

    <!-- Sección para mostrar las compras realizadas 
    <div class="purchases-section" v-if="showPurchasesSection">
      <h3>Ventas Realizadas</h3>
      <ul>
        <li v-for="(purchase, index) in purchases" :key="index">
          <ul>
            <li v-for="(item, i) in purchase.items" :key="i">
              <span>{{ item.city.name }} - Cantidad: {{ item.quantity }} - Total: $ {{ item.total }}</span>
            </li>
          </ul>
        </li>
      </ul>
      <p>Total de Ventas del Día: $ {{ totalSales }}</p>
    </div>
-->
<!-- Sección para mostrar las compras realizadas -->
<div class="purchases-section" v-if="showPurchasesSection">
  <h3>Ventas Realizadas</h3>
  <ul>
    <li v-for="(purchase, index) in purchases" :key="index">
      <p>Fecha de compra: {{ purchase.datetime }}</p> <!-- Mostrar la fecha de compra -->
      <ul>
        <li v-for="(item, i) in purchase.items" :key="i">
          <span>{{ item.city.name }} - Cantidad: {{ item.quantity }} - Total: $ {{ item.total }}</span>
        </li>
      </ul>
    </li>
  </ul>
  <p>Total de Ventas del Día: $ {{ totalSales }}</p>
</div>



  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface City {
  id: number;
  name: string;
  cost: number;
  quantity: number;
}

interface CartItem {
  city: City;
  quantity: number;
  total: number;
}

interface Purchase {
  datetime: string;
  items: CartItem[];
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
        { id: 16, name: 'Córdoba - Carlos Paz', cost: 1600, quantity: 1 },
      ] as City[],
      paymentAmount: 0,
      selectedCity: null as City | null,
      searchTerm: '',
      cart: [] as CartItem[],
      purchases: JSON.parse(localStorage.getItem('purchases') || '[]') as Purchase[],
      showPurchasesSection: false,
      showCityList: true
    };
  },
  mounted() {
    //  localStorage.clear();
    // Inicializar localStorage si es necesario
    if (!localStorage.getItem('purchases')) {
      localStorage.setItem('purchases', '[]');
    }
  },

  computed: {
    filteredCities(): City[] {
      return this.cities.filter(city =>
        city.name.toLowerCase().includes(this.searchTerm.toLowerCase())
      );
    },

    totalTicketsCost(): number {
      return this.selectedCity ? this.selectedCity.cost * this.selectedCity.quantity : 0;
    },

    totalPurchase(): number {
      return this.cart.reduce((total, item) => total + item.total, 0);
    },

    changeToReturn(): number | null {
      if (this.cart.length === 0) {
        return null;
      }

      const totalPurchase = this.cart.reduce((total, item) => total + item.total, 0);
      return this.paymentAmount - totalPurchase;
    },
    totalSales(): number {
      let total = 0;

      // Iterar sobre cada compra y sumar los totales de todos los elementos
      this.purchases.forEach((purchase: Purchase) => {
        purchase.items.forEach((item: CartItem) => {
          total += item.total;
        });
      });

      return total;
    },
  },

  methods: {
    selectCity(city: City) {
      this.selectedCity = city;
    },

    addToCart() {
      const existingItemIndex = this.cart.findIndex(item => item.city.id === this.selectedCity!.id);

      if (existingItemIndex !== -1) {
        this.cart[existingItemIndex].quantity = this.selectedCity!.quantity;
        this.cart[existingItemIndex].total = this.selectedCity!.quantity * this.selectedCity!.cost;
      } else {
        this.cart.push({
          city: this.selectedCity!,
          quantity: this.selectedCity!.quantity,
          total: this.selectedCity!.quantity * this.selectedCity!.cost,
        });
      }

      this.selectedCity = null;
    },

    removeFromCart(index: number) {
      this.cart.splice(index, 1);
    },
/*/
    completePurchase() {
      const purchase: Purchase = {
        items: this.cart,
      };

      const purchases = JSON.parse(localStorage.getItem('purchases') || '[]') as Purchase[];
      purchases.push(purchase);
      localStorage.setItem('purchases', JSON.stringify(purchases));

      this.cart = [];
    },*/

    completePurchase() {
      const currentDate = new Date(); // Obtener la fecha y hora actual

      // Formatear la fecha y hora en una cadena legible
      const formattedDate = currentDate.toLocaleString('es-AR', {
        day: '2-digit',
        month: '2-digit',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit',
      });

      const purchase: Purchase = {
        datetime: formattedDate, // Usar la fecha y hora formateada
        items: this.cart,
      };

      const purchases = JSON.parse(localStorage.getItem('purchases') || '[]') as Purchase[];
      purchases.push(purchase);
      localStorage.setItem('purchases', JSON.stringify(purchases));

      this.cart = [];
    },



    togglePurchasesSection() {
      this.showPurchasesSection = !this.showPurchasesSection;
    },

    toggleCityList() {
      this.showCityList = !this.showCityList;
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

.toggle-section-button {
  margin-bottom: 10px;
}
.purchases-section {
  /* Estilos para la sección de compras */
  margin-top: 10px;
  border: 1px solid #ccc;
  padding: 10px;
}

/* Estilos para el botón de mostrar/ocultar compras */
.toggle-section-button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  margin-bottom: 10px;
}

.toggle-section-button:hover {
  background-color: #0056b3;
}

/* Estilos para el botón de comprar carrito */
.complete-purchase-button {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
}

.complete-purchase-button:hover {
  background-color: #218838;
}

.section-separator {
    border-top: 1px solid #ccc; /* Establece el grosor y el color de la línea */
    margin: 20px 0; /* Agrega espacio alrededor de la línea */
}

</style>
