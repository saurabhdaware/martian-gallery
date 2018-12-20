<template>
  <div class="home-component">
    <div class="cover w3-display-container">
      <span class="w3-display-middle">
        This projects fetches Images from NASA API and creates a video out of similar images, You can see the source code by clicking on the octocat from top right corner :D <br>
        <a class="search-images-button w3-btn w3-card-4" href="#search"><span class="fa fa-search"></span> Search Images</a>
      </span> 
      <span class="w3-display-bottomright w3-padding" style="" v-if="images[0]">Sol {{images[0].rover.max_sol}} <img width=20 src="../assets/logo.png"></span>
    </div>
    
    <div id="search" class="gallery-container">
      {{msg}}<br><br>
      <div class="w3-row search-container">
        <div class="w3-third">Sol <input class="w3-input sol-input" type="number" placeholder="sol" ref="sol"></div>
        <div class="w3-third">
          Camera
          <select class="w3-input camera-input" name="camera" id="cam" ref="camera">
            <option value="RHAZ">RHAZ</option>
            <option value="FHAZ">FHAZ</option>
            <option value="NAVCAM">NAVCAM</option>
          </select>
        </div>
        <div class="w3-third"><br><button class="fetchdata-button w3-btn" v-on:click="getInSightData">Fetch Data</button></div>
      </div>
      
      <div class="gallery">
      <br>{{error}}
      <div v-show="vid.length != 1" v-if="vid.length >= 1" v-for="(vid,index) in groups" :key="index">
        <button v-on:click="playVideo(vid,index)">Play {{vid}} {{index}}</button><br><img ref="video" :src="images[Number(vid[0])].img_src" height=300>
      </div>
      <table>
        <tr class="image" v-for="(image,index) in images" :key="index">
          <td>
            <img class="picture" ref="pictures" :data-key="index" :src="image.img_src">        
          </td>
          <td>Sol: {{image.sol}} || Earth Date: {{image.earth_date}}<br> Rover: {{image.rover.name}} || Rover Max Sol : {{image.rover.max_sol}} </td>
        </tr>
      </table>
      </div>
    </div>
  </div>
</template>

<script>
Array.prototype.allIndexesOf = function(value){
  let indexes = [];
  for(let i in this){
    if(value == this[i]){
      indexes.push(i);
    }
  }

  return indexes;
}
export default {
  name: 'Home',
  data () {
    return {
      msg: 'Search for Images from Curiosity',
      images:[],
      error:'',
      i:0,
      videoInterval:null,
      imageDimenstions:[],
      groups:[]
    }
  },
  methods:{
    getInSightData:function(){
      this.images = [];
      this.imageDimenstions = [];
      this.groups = [];
      this.error = '';
      let sol = this.$refs.sol.value || 15;
      let cam = this.$refs.camera.value || 'FHAZ';
      this.$axios.get(`https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?sol=${sol}&camera=${cam}&api_key=GEVMc5bOUBZay30b56wviMB2zkz8cIlGJ89wENJo`)
      .then((res)=>{
        this.images = res.data.photos;
        if(!this.images[0]){
          this.error = "NASA didnt feel like clicking picture on this day :(";
        }

        return;
      })
      .then(this.imageLoader);
    },
    createVideo:function(){
      this.imageDimenstions = this.$refs.pictures.map(image=>image.clientHeight + 'x' + image.clientWidth);
      let uniqueDimensions = [...new Set(this.imageDimenstions)];
      for(let dimension of uniqueDimensions){
        this.groups.push(this.imageDimenstions.allIndexesOf(dimension)); // Custom function defined at the start of script tag in this file
      }
      console.log(this.imageDimenstions);
    },
    playVideo:function(arr,index){
      console.log(arr);
      console.log(this.$refs.video);
      let frame = 0;
      let vidInterval = setInterval(()=>{
        this.$refs.video[index].src = this.images[Number(arr[frame])].img_src;
        frame++;
        if(frame == arr.length) clearInterval(vidInterval);
      },700);
    },
    imageLoader:function(){
      let count = 0;
      let vue = this;
      document.querySelectorAll('.picture').forEach(image=>image.addEventListener('load',function(){
        count++
        if(count == vue.images.length){
          vue.createVideo();
        }
      }))
    }
  },
  mounted() {
    this.getInSightData();
  }
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
.cover{
  background:url('../assets/cover.jpg');
  height:400px;
  width:100%;
  background-size:cover;
  background-position:center;
}
.search-images-button{
  background-color:#111;
  color:#aaa;
}
.search-images-button:hover{
  color:#ddd;
}
.gallery-container{
  padding:40px;
}
.search-container > div{
  padding:10px;
}
.fetchdata-button{
  background:#ddd;
  color:#333;
}
.fetchdata-button:hover{
  color:#000;
}
</style>
