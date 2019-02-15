<template>
  <div id="app">
    <MainComponent/>
  </div>
</template>

<script>
import MainComponent from './components/MainComponent'

export default {
  name: 'App',
  components: {
    MainComponent
  }
}
</script>

<style>
  @import "assets/css/foundation.min.css";
  @import "assets/css/app.css";
</style>
