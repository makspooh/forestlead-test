<template>
  <v-app>

    <v-content>
      <router-view :key="$route.fullPath"></router-view>
    </v-content>
  </v-app>
</template>

<script>

export default {
  components: {
    // 
  },
  data () {
    return {
      //
    }
  }
}
</script>