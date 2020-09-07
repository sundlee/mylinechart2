	<template>
  <div id="app">
    <home></home>
  </div>
</template>

<script>
import Home from '@/views/Home';

export default {
  components: { Home },
  name: 'app'
}
</script>

<style lang="scss">
#app {
  position: relative;
}
</style>

