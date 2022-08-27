<template>
  <div>
    <h1 style="margin-top:15px; margin-bottom:25px; text-align:center;">Calendario del dolar a peso Chileno</h1>
    <v-layout>
    <v-flex xs12>
      <v-card>
            <v-date-picker 
            locale="es-ve"
            :min="minimo"
            :max="maximo"
            @change="getdolar(fecha)"
            full-width v-model="fecha"></v-date-picker>
      </v-card>
      <v-card color="error" dark>
        <v-card-text class="display-1 text-center">
         <strong>Precio</strong>: {{valor}} pesos 
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
  </div>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
  export default {
    name: 'Home',
    data() {
      return {
        fecha: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        minimo:'1975',
        maximo:  (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        valor: null
      }
    },
    methods:{
      ...mapMutations(['mostrarLoading', 'ocultarLoading']),
      async getdolar(dia){
        
        let arrayFecha = dia.split(['-'])
        let ddmmaa = arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0]

        try {

          this.mostrarLoading({titulo:'Cargando', color: 'secondary'})

          let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmaa}`)

          if(datos.data.serie.length > 0){
        this.valor = await datos.data.serie[0].valor
          }else{
            this.valor = "sin resultados"
          }
        console.log(datos.data.serie[0].valor);

        } catch (error) {
          console.log(error)
        }
        finally{
          this.ocultarLoading()
        }
  
      }
    },
    created(){
      this.getdolar(this.fecha)
    }
  }
</script>
