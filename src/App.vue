<template>
  <div id="app">
    <Navbar/>
    <router-view/>
    <div class="footer">
      Made with <span class="fa fa-heart"></span> using <img src="./assets/logo.png" height=20> at 3:00 AM
      <a class="hide-mobile" target="_blank" href="https://saurabhdaware.in" style="float:right"><span class="fa fa-copyright"></span> {{new Date().getFullYear()}} Saurabh Daware</a>
    </div>
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
::-webkit-scrollbar{
  width:2px;
  height:2px;
}
::-webkit-scrollbar-track{
  width:2px;
  height:2px;
}

::-webkit-scrollbar-thumb{
  background: #777;
}
.footer{
  text-align: left;
  background-color: #000;
  color:#777;
  padding:20px;
  font-size:11pt;
  font-weight:bold;
}
@media (max-width:768px) {
  .hide-mobile{
    display:none;
  }
}
</style>
