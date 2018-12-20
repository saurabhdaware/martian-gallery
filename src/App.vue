<template>
  <div id="app">
    <Navbar/>
    <router-view/>
  </div>
</template>

<script>
import Navbar from '@/components/sub-components/Navbar';
export default {
  name: 'App',
  components:{
    Navbar,
  }
}
</script>

<style>
html,body{
  background-color:#111;
  padding:0px;
  width:100%;
  margin:0px;
}
a{text-decoration:none;}
#app {
  font-family: 'Electrolize', sans-serif;
  color: #fff;
  width:100%;
}
</style>
