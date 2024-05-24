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
              <div v-if="statuses[operation]" :class="['check-message', { 'error-message': !statuses[operation].success }]">
                <span v-if="statuses[operation].success" class="checkmark">✔</span>
                  <span v-else class="errormark">✘</span>
                  <span class="message">{{ statuses[operation].message }}</span>                   
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
      statuses: {},
      tokenStatuses: {},
      timers: {}
    }
  },
  methods: {
    async enviar_(operation) {
      try {
        const response = await axios.post(`http://localhost:5000/${operation}`);
        this.$set(this.statuses, operation, response.data);

        // Borrar la notificación de envío correcto después de 10 segundos
        if (response.data.success) {
          this.timers[operation] = setTimeout(() => {
            this.$delete(this.statuses, operation);
            
            // Mostrar la notificación de estado del token si existe
            if (response.data.token_status) {
              this.$set(this.tokenStatuses, operation, response.data.token_status);

              // Borrar la notificación de estado del token después de 10 segundos
              setTimeout(() => {
                this.$delete(this.tokenStatuses, operation);
              }, 10000);
            }
          }, 10000);
        }

      } catch (error) {
        console.error(`Error al procesar la operación ${operation}:`, error);
        this.$set(this.statuses, operation, { success: false, message: 'Error al enviar el correo' });
      }
    }
  },
  beforeDestroy() {
    // Limpiar los temporizadores para evitar fugas de memoria
    for (let operation in this.timers) {
      clearTimeout(this.timers[operation]);
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
  color: green;  
}

.checkmark {
  color: green;
  font-size: 20px;
  margin-right: 5px;
}


.errormark {
    color: red;
    font-size: 20px;
    margin-right: 5px;
  }
  
  .message {
    font-size: 14px;
  }
  
  .error-message .message {
    color: red; /* Cambia el color del mensaje de error a rojo */
  }
</style>
