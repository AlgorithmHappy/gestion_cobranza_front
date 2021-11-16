<template>
  <v-simple-table>
    <template v-slot:default>
      <thead>
        <tr>
          <th class="text-left">
            Fianza
          </th>
          <th class="text-left">
            Movimiento
          </th>
          <th class="text-left">
            Fiado
          </th>
          <th class="text-left">
            Antigüedad
          </th>
          <th class="text-left">
            Días de vencimiento
          </th>
          <th class="text-right">
            Importe
          </th>
        </tr>
      </thead>
      <tbody>
          <slot name="contentData">
          </slot>
      </tbody>
    </template>
  </v-simple-table>
</template>
<script>
export default {
    name: 'TableDatos',
    data: () => ({
      desserts: [
        {fianza: 1, movimiento: 21, fiado: 'hola', antiguedad: 'resto', dias: 21, importe: 13},
        {fianza: 1, movimiento: 21, fiado: 'hola', antiguedad: 'resto', dias: 21, importe: 13},
        {fianza: 1, movimiento: 21, fiado: 'hola', antiguedad: 'resto', dias: 21, importe: 13},
        {fianza: 1, movimiento: 21, fiado: 'hola', antiguedad: 'resto', dias: 21, importe: 13}
      ]
    })
}
</script>