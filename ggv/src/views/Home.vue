<template>
  <div id="homeContent">
    <img id="logoImg2" src="../assets/images/main.jpg" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue';

export default {
  name: 'Home',
  components: {
    HelloWorld,
  },
  create: function () {
    console.log('home initiate');
  },
};
</script>

<style>
#homeContent {
  text-align: center;
  padding: 40px;
}

#logoImg2 {
  width: 800px;
}
</style>
