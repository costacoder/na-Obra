<template>
  <v-app>
    <v-main>
      <medidasObra/>
    </v-main>
  </v-app>
</template>

<script>
import medidasObra from './components/medidas-obra.vue'

export default {
  name: 'App',

  components: {
    medidasObra,
  },

  data: () => ({
    //
  }),
}
</script>
