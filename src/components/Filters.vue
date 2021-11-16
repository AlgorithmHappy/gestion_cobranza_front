<template>
  <v-container fluid>
    <v-row align="center">
      <v-col
        class="d-flex"
        cols="12"
        sm="4"
      >
        <v-select
          :items="arrOficinas"
          v-model="oficinaSelection"
          label="Oficina"
          v-on:change="resetAgenteValue"
        ></v-select>
      </v-col>

      <v-col
        class="d-flex"
        cols="12"
        sm="4"
      >
        <v-select
          :items="arrAgentes"
          label="Agente"
          v-model="agenteSelection"
          :disabled="disabledAgente"
          v-on:change="resetEjecutivoValue"
        ></v-select>
      </v-col>

      <v-col
        class="d-flex"
        cols="12"
        sm="4"
      >
        <v-select
          :items="arrEjecutivos"
          label="Ejecutivo"
          v-model="ejecutivosSelection"
          :disabled="disabledEjecutivo"
          v-on:change="shotEvent"
        ></v-select>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
    name: 'Filters',
    
    data: () => ({
        items: ['Foo', 'Bar', 'Fizz', 'Buzz'],
        respJsonEstados: [],
        respJsonAgentes: [],
        respJsonEjecutivos: [], 
        oficinaSelection: '',
        agenteSelection: '',
        ejecutivosSelection: '',
        disabledAgente: true,
        disabledEjecutivo: true,
    }),

    computed: {
        arrOficinas(){
            let arrOfi = [];
            for(let it of this.respJsonEstados){
                arrOfi.push(it.nombreDeEstado);
            }
            arrOfi.push('');
            return arrOfi;
        },

        arrAgentes(){
            let arrAg = [];
            for(let it of this.respJsonAgentes){
                arrAg.push(it.nombreCompleto);
            }
            arrAg.push('');
            return arrAg;
        },

        arrEjecutivos(){
            let arrEj = [];
            for(let it of this.respJsonEjecutivos){
                arrEj.push(it.nombreCompleto);
            }
            arrEj.push('');
            return arrEj;
        },

    },

    methods: {
        async getOficinas() {
            let url = new URL('http://localhost:8080/WsFinanzas/getAllEstados');
            const dataRequest = {
                method: 'GET',
            };
            let response = await fetch(url, dataRequest)
            let result = await response.json();
            this.respJsonEstados = result;
        },

        async getAgentesByOficina(idOficina) {
            var params = {id_estado: idOficina}; 
            let url = new URL('http://localhost:8080/WsFinanzas/getAgentesByEstado');
            url.search = new URLSearchParams(params).toString();
            const dataRequest = {
                method: 'GET',
            };
            let response = await fetch(url, dataRequest)
            let result = await response.json();
            this.respJsonAgentes = result;
        },

        async getEjecutivosByOficinaAndAgente(idOficina, idAgente) {
            var params = {id_estado: idOficina, id_agente: idAgente};
            let url = new URL('http://localhost:8080/WsFinanzas/getEjecutivosByEstadoAndId');
            url.search = new URLSearchParams(params).toString();
            const dataRequest = {
                method: 'GET',
            };
            let response = await fetch(url, dataRequest)
            let result = await response.json();
            this.respJsonEjecutivos = result;
        },

        idOficinaSelection(){
            let strId = this.oficinaSelection;
            let idReturn = 0;
            console.log(strId);
            for(let it of this.respJsonEstados){
                if(it.nombreDeEstado === strId){
                    idReturn = it.id;
                }
            }
            return idReturn;
        },

        idAgenteSelection(){
            let strAgId = this.agenteSelection;
            let idReturn = 0;
            for(let it of this.respJsonAgentes){
                if(it.nombreCompleto === strAgId){
                    idReturn = it.id;
                }
            }
            return idReturn;
        },

        idEjecutivoSelection(){
            let strEjId = this.ejecutivosSelection;
            let idReturn = 0;
            for(let it of this.respJsonEjecutivos){
                if(it.nombreCompleto === strEjId){
                    idReturn = it.id;
                }
            }
            return idReturn;
        },
        
        resetAgenteValue(){
            this.agenteSelection = '';
            this.ejecutivosSelection = '';

            if(this.oficinaSelection === ''){
                this.disabledAgente = true;
            }else{
                this.disabledAgente = false;
            }

            this.getAgentesByOficina(this.idOficinaSelection());

            this.disabledEjecutivo = true;

            this.shotEvent();
        },

        resetEjecutivoValue(){
            if(this.agenteSelection === ''){
                this.ejecutivosSelection = '';
                this.disabledEjecutivo = true;
                this.shotEvent();
                return;
            }
            this.ejecutivosSelection = '';
            this.getEjecutivosByOficinaAndAgente(this.idOficinaSelection(), this.idAgenteSelection());
            this.disabledEjecutivo = false;
            this.shotEvent();
        },

        shotEvent(){
            let parameters = {
                oficina: this.idOficinaSelection(),
                agente: this.idAgenteSelection(),
                ejecutivo: this.idEjecutivoSelection(),
            }
            this.$emit('updateTable', parameters);
        }
    },

    created(){
        this.getOficinas();
    }
}
</script>
