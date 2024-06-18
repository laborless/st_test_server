<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/st-logo-blue-vertical.png">
    <HelloWorld msg="Welcome to APL Test App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  }
}
</script>
