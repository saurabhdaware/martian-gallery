<template>
  <div class="home-component">
    {{msg}}<br><br>
    Sol: <input type="text" placeholder="sol" ref="sol"><br>
    Camera : 
    <select name="camera" id="cam" ref="camera">
      <option value="RHAZ">RHAZ</option>
      <option value="FHAZ">FHAZ</option>
      <option value="NAVCAM">NAVCAM</option>
    </select>
    <button v-on:click="getInSightData">Search Image</button><br>
    <div class="gallery-container">
      <div v-if="images.length > 1"><button v-on:click="playVideo">Play</button><br><img ref="video" src="" width=300></div>
      <table>
        <tr class="image" v-for="(image,index) in images" :key="index">
          <td>
            <img class="picture" :src="image.img_src">        
          </td>
          <td>Sol: {{image.sol}} || Earth Date: {{image.earth_date}}<br> Rover: {{image.rover.name}} || Rover Max Sol : {{image.rover.max_sol}} </td>
        </tr>
      </table>
    </div>
    <br>{{error}}
  </div>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      msg: 'Search for Images from Curiosity',
      images:[],
      error:'',
      i:0,
      videoInterval:null
    }
  },
  methods:{
    getInSightData:function(){
      this.images = [];
      this.error = '';
      let sol = this.$refs.sol.value || 15;
      let cam = this.$refs.camera.value || 'FHAZ';
      this.$axios.get(`https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?sol=${sol}&camera=${cam}&api_key=GEVMc5bOUBZay30b56wviMB2zkz8cIlGJ89wENJo`)
      .then((res)=>{
        this.images = res.data.photos;
        if(!this.images[0]){
          this.error = "NASA didnt feel like clicking picture on this day :(";
        }
      })
    },
    playVideo:function(){
      this.i = 0;
      this.videoInterval = setInterval(this.loopOverImages,700);
    },
    loopOverImages: function(){
        this.$refs.video.src=this.images[this.i].img_src;
        this.i++;
        if(this.i==this.images.length){
          clearInterval(this.videoInterval);
        }
      }
  },
  mounted() {
    this.getInSightData();
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.picture{
  height:200px;
}
a {
  color: #42b983;
}
.image > td:nth-child(2){
  padding-left:100px;
}
</style>
