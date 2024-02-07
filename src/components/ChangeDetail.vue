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
          { id: 1, name: 'Devoto', cost: 2000 },
          { id: 2, name: 'La Francia', cost: 2500 },
          { id: 3, name: 'El Tío', cost: 3000 },
          { id: 4, name: 'Arroyito', cost: 3500 },
          { id: 5, name: 'Tránsito', cost: 4000 },
          { id: 6, name: 'Santiago Temple', cost: 4500 },
          { id: 7, name: 'Pedro E. Vivas', cost: 5000 },
          { id: 8, name: 'Río Primero', cost: 5500 },
          { id: 9, name: 'Piquillín', cost: 6000 },
          { id: 10, name: 'Montecristo', cost: 6500 },
          { id: 11, name: 'Malvinas Argentinas', cost: 7000 },
          { id: 12, name: 'Córdoba', cost: 7900 },
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
  