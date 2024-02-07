<template>
    <div class="change-detail-container">

      <h2 class="title">Detalle de Cambios</h2>
      <!-- Listado de ciudades -->
      <ul class="city-list">
        <li v-for="(city, index) in cities" :key="city.id" class="city-item" :class="{ 'separator': index !== cities.length - 1 }" @click="selectCity(city)">
          <span class="city-name">{{ city.name }}</span>
          <span class="city-cost">{{ city.cost }}</span>
        </li>
      </ul>
  
      <!-- Input para el pago del cliente -->
      <div class="payment-input">
        <label for="payment">Pago del Cliente:</label>
        <input type="number" id="payment" v-model="paymentAmount">
      </div>
  
      <!-- Etiqueta para mostrar la ciudad seleccionada -->
      <div class="selected-city" v-if="selectedCity">
        <label>Ciudad Seleccionada:</label>
        <span>{{ selectedCity.name }}</span>
      </div>
  
      <!-- Etiqueta para mostrar el cambio a devolver al cliente -->
      <div class="change-label">
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
  }
  
  export default defineComponent({
    name: 'ChangeDetail',
    data() {
      return {
        cities: [
          { id: 1, name: 'Km 531', cost: 1130 },
          { id: 2, name: 'Devoto', cost: 1360 },
          { id: 3, name: 'Jeanmaire', cost: 1740 },
          { id: 4, name: 'La Francia', cost: 2310 },
          { id: 5, name: 'Km 581', cost: 2650 },
          { id: 6, name: 'El Tío', cost: 3060 },
          { id: 7, name: 'El Fuertecito', cost: 3430 },
          { id: 8, name: 'Arroyito', cost: 3750 },
          { id: 9, name: 'Puente del Río Segundo', cost: 3570 },
          { id: 10, name: 'Tránsito', cost: 3790 },
          { id: 11, name: 'Santiago Temple', cost: 4840 },
          { id: 12, name: 'Pedro Vivas', cost: 4900 },
          { id: 13, name: 'Río Primero', cost: 5190 },
          { id: 14, name: 'K711 Malvinas Argentinas', cost: 6770 },
          { id: 15, name: 'Córdoba', cost: 7920 },
        ] as City[],
        paymentAmount: 0, // Variable para almacenar el monto del pago del cliente
        selectedCity: null as City | null, // Ciudad seleccionada
      };
    },
    computed: {
      changeToReturn(): number | null {
        // Calcula el cambio a devolver restando el costo de la ciudad seleccionada del pago del cliente
        if (this.paymentAmount <= 0 || !this.selectedCity) {
          return null; // Si el pago es nulo o cero, o no hay ciudad seleccionada, devuelve null
        }
        return this.paymentAmount - this.selectedCity.cost;
      },
    },
    methods: {
      selectCity(city: City) {
        this.selectedCity = city;
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
  </style>
  