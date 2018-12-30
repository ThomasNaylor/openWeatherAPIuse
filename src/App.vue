<template>
    <v-content>
      <Weatherman/>
    </v-content>
  </v-app>
</template>

<script>
import Weatherman from './components/Weatherman';

export default {
  name: 'App',
  components: {
    Weatherman
  },
  data () {
    return {
      //
    }
  }
}
</script>
