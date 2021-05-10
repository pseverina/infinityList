<template>
  <div class="app">
    <date></date>
    <list></list>
  </div>
</template>

<script>
import List from './components/List.vue'
import Date from './components/Date'

export default {
  name: 'App',

  components: {
    List,
    Date
  }
}
</script>

<style lang="scss" scoped>

.app {
  font-family: "Open Sans", sans-serif;
  max-width: 829px;
  margin: 0 auto;
}

</style>
