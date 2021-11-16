<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <h1>Gestion de Cobranza</h1>
    </v-app-bar>

    <v-main>
      <h1 class="text-center mt-4">Prima por cobrar</h1>
      <Filters @updateTable="pickUpFilters" />

      <v-container
        class="lighten-5"
      >
        <v-row
          no-gutters
          style="height: 350px;"
        >
          <v-col>
            <CardMoneda>
              <template v-slot:tipoMoneda>
                <p class="text-h5 text--primary">{{arrMonedas[2].tipoDeMoneda}}</p>
              </template>

              <template v-slot:cantidadTotal>
                <p class="text-h5 text--secondary">
                  ${{ dicSumasMonedas.find(it => it.id === arrMonedas[2].id ).total.toFixed(2) }}
                </p>
              </template>

              <template v-slot:cantidadQueSePago> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[2].id ).pagado.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[2].id ).porcentajePagado.toFixed(2) }}%
              </template>

              <template v-slot:cantidadQueSeDebe> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[2].id ).debe.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[2].id ).porcentajeDebe.toFixed(2) }}%
              </template>
            </CardMoneda>

          </v-col>

          <v-col>
            
            <CardMoneda>
              <template v-slot:tipoMoneda>
                <p class="text-h5 text--primary">{{arrMonedas[0].tipoDeMoneda}}</p>
              </template>

              <template v-slot:cantidadTotal>
                <p class="text-h5 text--secondary">
                  ${{ dicSumasMonedas.find(it => it.id === arrMonedas[0].id ).total.toFixed(2) }}
                </p>
              </template>

              <template v-slot:cantidadQueSePago> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[0].id ).pagado.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[0].id ).porcentajePagado.toFixed(2) }}%
              </template>

              <template v-slot:cantidadQueSeDebe> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[0].id ).debe.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[0].id ).porcentajeDebe.toFixed(2) }}%
              </template>    
            </CardMoneda>

          </v-col>

          <v-col>
            
            <CardMoneda>
              <template v-slot:tipoMoneda>
                <p class="text-h5 text--primary">{{arrMonedas[1].tipoDeMoneda}}</p>
              </template>

              <template v-slot:cantidadTotal>
                <p class="text-h5 text--secondary">
                  ${{ dicSumasMonedas.find(it => it.id === arrMonedas[1].id ).total.toFixed(2) }}
                </p>
              </template>

              <template v-slot:cantidadQueSePago> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[1].id ).pagado.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[1].id ).porcentajePagado.toFixed(2) }}%
              </template>

              <template v-slot:cantidadQueSeDebe> 
                ${{ dicSumasMonedas.find(it => it.id === arrMonedas[1].id ).debe.toFixed(2) }} - {{ dicSumasMonedas.find(it => it.id === arrMonedas[1].id ).porcentajeDebe.toFixed(2) }}%
              </template>
            </CardMoneda>
          </v-col>
        </v-row>
      </v-container>

      <v-container class="mt-n16">
        <TableDatos class="mt-n12">
          <template v-slot:contentData>
            <tr v-for="item in arrDatos" :key="item.fianza" :style="{'color': arrColores.find(it => it.color === item.color).codigoHexadecimal}">
              <td>{{ item.fianza }}</td>
              <td>{{ item.movimiento }}</td>
              <td>{{ item.fiado }}</td>
              <td>{{ item.antiguedad }}</td>
              <td>{{ item.diasVencimiento }}</td>
              <td>{{ item.dias }}</td>
              <td>${{ item.importe }}</td>
            </tr>
          </template>
        </TableDatos>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Filters from "./components/Filters";
import CardMoneda from "./components/CardMoneda";
import TableDatos from "./components/TableDatos";

export default {
  name: "App",

  components: {
    Filters,
    CardMoneda,
    TableDatos,
  },

  data: () => ({
    jsonMonedas: [],
    jsonEquivMonedas: [],
    arrMonedas: [],
    arrEquivMonedas: [],
    filtersParams: {},
    respJsonDatos: [],
    respJsonColores: [],
    arrDatos: [],
    arrColores: [],
    dicSumasMonedas: [],
    dolares: 0.0
  }),

  computed: {
  },

  methods: {
    async getMonedasType() {
      let url = new URL('http://localhost:8080/WsFinanzas/getAllMonedas');
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.jsonMonedas = result;
      this.monedasToArr();
    },

    async getEquivalenciaMonedas() {
      let url = new URL('http://localhost:8080/WsFinanzas/getAllEquivalenciaMonedas');
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.jsonEquivMonedas = result;
      this.equivalenciMonToArr();
    },

    async getAllDatos() {
      let url = new URL('http://localhost:8080/WsFinanzas/getAllDatos');
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.respJsonDatos = result;
    },

    async getDatosByOficina(id) {
      var params = {id_oficina: id}; 
      let url = new URL('http://localhost:8080/WsFinanzas/getDatosPagosByEstado');
      url.search = new URLSearchParams(params).toString();
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.respJsonDatos = result;
    },

    async getDatosByOficinasAndAgentes(idOficina, idAgente) {
      var params = {id_oficina: idOficina, id_agente: idAgente}; 
      let url = new URL('http://localhost:8080/WsFinanzas/getDatosPagosByEstadoAgente');
      url.search = new URLSearchParams(params).toString();
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.respJsonDatos = result;
    },

    async getDatosByOficinasAndAgentesEjecutivos(idOficina, idAgente, idEjecutivo) {
      var params = {id_oficina: idOficina, id_agente: idAgente, id_ejecutivo: idEjecutivo}; 
      let url = new URL('http://localhost:8080/WsFinanzas/getDatosPagosByEstadoAgenteEjecutivo');
      url.search = new URLSearchParams(params).toString();
      const dataRequest = {
        method: 'GET',
      };
      let response = await fetch(url, dataRequest)
      let result = await response.json();
      this.respJsonDatos = result;
    },

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

    monedasToArr() {
      for(let it of this.jsonMonedas){
        this.arrMonedas.push(it);
      }
    },

    equivalenciMonToArr(){
      for(let it of this.jsonEquivMonedas){
        this.arrEquivMonedas.push(it);
      }
    },

    pickUpFilters(childVar) {
      this.filtersParams = childVar;
      if(childVar.oficina == 0){
        this.getAllDatos()
        return;
      }
      if(childVar.oficina != 0 && childVar.agente == 0 && childVar.ejecutivo == 0){
        this.getDatosByOficina(childVar.oficina);
        return;
      }
      if(childVar.oficina != 0 && childVar.agente != 0 && childVar.ejecutivo == 0){
        this.getDatosByOficinasAndAgentes(childVar.oficina, childVar.agente);
        return;
      }
      if(childVar.oficina != 0 && childVar.agente != 0 && childVar.ejecutivo != 0){
        this.getDatosByOficinasAndAgentesEjecutivos(childVar.oficina, childVar.agente, childVar.ejecutivo);
        return;
      }
    },

    convertToArrDatos(arrRaw){
      this.arrDatos = [];
      for(let it of arrRaw){
        let obj = {
          fianza: it.fianza,
          movimiento: it.movimiento,
          fiado: it.fiado,
          antiguedad: it.antiguedad,
          diasVencimiento: it.diasVencimiento,
          importe: it.importe,
          moneda: it.moneda,
          color: it.color,
        };
        this.arrDatos.push(obj);
      }
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

    distinctMoneda(){
      let objDistinct = [];
      for(let it of this.arrDatos){
        let boleano = (objDistinct.find(jt => jt.tipoDeMoneda === it.moneda)) === undefined;
        if(boleano){
          let obj = {
            id: this.arrMonedas.find(jt => jt.tipoDeMoneda === it.moneda).id,
            tipoDeMoneda: it.moneda,
          }
          objDistinct.push(obj);
        }

      }

      let arrReturn = [];

      for(let it of objDistinct){ // Sumas totales del mismo tipo de moneda
        let _total_pago = 0.0;
        let loQCPaga = 0.0;
        for(let jt of this.arrDatos){
          if(it.tipoDeMoneda === jt.moneda){
            _total_pago += jt.importe;
            if(jt.diasVencimiento > 30){
              loQCPaga += jt.importe;
            }
          }
        }
        
        //console.log(_total_pago);

        let suma = {
          id: it.id,
          tipoDeMoneda: it.tipoDeMoneda,
          total: _total_pago,
          debe: loQCPaga,
          pagado: _total_pago - loQCPaga, 
        }

        arrReturn.push(suma);
      }

      return arrReturn;
    },

    actualizaPago(){     
      let arrMonObDistinct = this.distinctMoneda();
      let intDolar = this.arrMonedas.find(r => r.tipoDeMoneda === 'Dolares').id; // Se tomo como base el dolar
      let totalDeDolares = 0.0;
      let totalDebe = 0.0;
      let totalpagado = 0.0;
      //console.log(arrMonObDistinct);

      for(let it of arrMonObDistinct){ // Convertir todo a dolares
        let objEq = this.arrEquivMonedas.find(jt => jt.monedaAConvertir === intDolar && jt.monedaBase === it.id);
        totalDeDolares += ((it.total + 0.0) * objEq.valorUnitario);
        totalDebe += ((it.debe + 0.0) * objEq.valorUnitario);
        totalpagado += ((it.pagado + 0.0) * objEq.valorUnitario);

      }

      let dolaresObj = {
          total: totalDeDolares,
          debe: totalDebe,
          pagado: totalpagado, 
      }

      this.dolares = dolaresObj;
      this.totalParaCadaTipoDeMoneda();
    },

    totalParaCadaTipoDeMoneda(){
      let intDolar = this.arrMonedas.find(r => r.tipoDeMoneda === 'Dolares').id;
      let arrTotalParaCadaMoneda = [];
      for(let it of this.arrMonedas){
        let equivalencia = 0.0;

        for(let jt of this.arrEquivMonedas){
          if(jt.monedaBase === intDolar && jt.monedaAConvertir === it.id){
            equivalencia = jt.valorUnitario;
          }
        }

        let _total = this.dolares.total * equivalencia;
        let _debe = this.dolares.debe * equivalencia;
        let _pagado = this.dolares.pagado * equivalencia;

        let obj = {
          id: it.id,
          tipoDeMoneda: it.tipoDeMoneda,
          total: _total,
          debe: _debe,
          pagado: _pagado,
          porcentajeDebe : (_debe * 100.0) / _total,
          porcentajePagado : (_pagado * 100.0) / _total,
        }
        arrTotalParaCadaMoneda.push(obj);
      }
      this.dicSumasMonedas = arrTotalParaCadaMoneda;
    }
  },

  watch: {
    respJsonDatos(newRaw, oldRaw){
      this.convertToArrDatos(newRaw);
      this.getColoresByIds(this.arrDatos.map(it => it.color));
      this.actualizaPago();
      oldRaw;
    },

    respJsonColores(newRaw, oldRaw){
      this.convertToArrColores(newRaw);
      oldRaw;
    }
  },

  created(){
    this.getMonedasType();
    this.getEquivalenciaMonedas();
  }
};
</script>