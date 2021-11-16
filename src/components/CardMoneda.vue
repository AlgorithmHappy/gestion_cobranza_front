<template>
  <v-card
    class="mx-auto"
    max-width="344"
  >
    <v-card-text>
      <slot name="tipoMoneda">
      </slot>
      <!--<p class="text-h4 text--primary">
      </p>-->
      <slot name="cantidadTotal">
      </slot>

      <p class="text-h6" :style="{'color': this.arrColores.find(it => it.color === 'Azul').codigoHexadecimal}">
        <v-icon
            dense
            color="green"
        >
            mdi-check-underline-circle
        </v-icon>
          <slot name="cantidadQueSePago">
          </slot>
        <!-- 800 -->
      </p>

      <p class="text-h6" :style="{'color':this.arrColores.find(it => it.color === 'Rojo').codigoHexadecimal}">
        <v-icon
            dense
            color="red"
        >
            mdi-alert-circle
        </v-icon>
          <slot name="cantidadQueSeDebe">
          </slot>
        <!-- 800 -->
      </p>
      
    </v-card-text>
  </v-card>
</template>
<script>
  export default {
    name: 'CardMoneda',

    data: () => ({
      respJsonColores: [],
      arrColores: []
    }),

    methods:{
      async getColoresByIds(arrCol) {
        let url = new URL('http://localhost:8080/WsFinanzas/getColores');
        const dataRequest = {
          method: 'POST',
          body: JSON.stringify(arrCol),
          headers: { 'Content-type': 'application/json' },
        };
        let response = await fetch(url, dataRequest)
        let result = await response.json();
        this.respJsonColores = result;
      },

      convertToArrColores(arrNew){
        this.arrColores = []
        for(let it of arrNew){
          let obj = {
            color: it.nombreDeColor,
            codigoHexadecimal: it.codigoHexadecimal
          };
          this.arrColores.push(obj);
        }
        
      },
    },

    watch: {
      respJsonColores(arrNew, arrOld){
        this.convertToArrColores(arrNew);
        arrOld;
      }
    },

    created(){
      this.getColoresByIds(['Rojo', 'Azul']);
      this.convertToArrColores(this.respJsonColores);
    }
  }
</script>