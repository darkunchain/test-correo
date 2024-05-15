<template>
  <div id="app">
    <b-container>
      <h1>Test de envio y recepción de correo</h1>
      <b-row>
        <b-col cols="12" v-for="(operation, index) in operations" :key="index">
          <b-row class="button-container">
            <b-col cols="4">
              <span class="static-text">{{ operation }}</span>
            </b-col>
            <b-col cols="4">
              <b-button @click="enviar_(operation)" variant="primary">Enviar</b-button>
            </b-col>
            <b-col cols="4">
              <div v-if="statuses[operation]" class="check-message">
                <span v-if="statuses[operation].success" class="checkmark">✔</span>
                <span v-if="statuses[operation].success" class="message">Proceso {{ operation }} finalizado correctamente</span>
              </div>
            </b-col>
          </b-row>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import axios from 'axios';
import { BContainer, BRow, BCol, BButton } from 'bootstrap-vue'

export default {
  components: {
    BContainer,
    BRow,
    BCol,
    BButton
  },
  data() {
    return {
      operations: ['Office365-Hotmail', 'Office365-gmail', 'Office365-OnPremise','Office365-Office365',
        'OnPremise-Hotmail', 'OnPremise-gmail', 'OnPremise-OnPremise','OnPremise-Office365',
        'gmail-OnPremise','gmail-Office365',
        'Hotmail-OnPremise','Hotmail-Office365'
      ],
      statuses: {}
    }
  },
  methods: {
    async enviar_(operation) {
      try {
        const response = await axios.post(`http://localhost:5000/${operation}`);
        this.$set(this.statuses, operation, response.data);
      } catch (error) {
        console.error(`Error al procesar la operación ${operation}:`, error);
      }
    }
  }
}
</script>

<style>
.button-container {
  margin-bottom: 10px;
}

.static-text {
  font-size: 16px; /* Cambia el tamaño de fuente según lo necesites */
  font-weight: bold; /* Hace que el texto esté en negrita */
}

.check-message {
  display: flex;
  align-items: center;
}

.checkmark {
  color: green;
  font-size: 20px;
  margin-right: 5px;
}

.message {
  font-size: 14px;
  color: green;
}
</style>
