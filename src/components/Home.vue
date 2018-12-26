<template>
  <div class="home-component">
    <div class="cover w3-display-container">
      <span class="w3-display-middle">
        This projects fetches Images from NASA API and creates a video out of similar images, You can see the source code by clicking on the octocat from top right corner :D <br>
        <a class="search-images-button w3-btn w3-card-4" href="#search"><span class="fa fa-search"></span> Search Images</a>
      </span> 
      <span class="w3-display-bottomright w3-padding" style="">Sol {{Math.floor(((new Date().getTime()/1000) - (new Date('8/6/2012').getTime()/1000))/((60*60*24)+((39*60)+35.244)))}} <img width=20 src="../assets/logo.png"></span>
    </div>
    
    <div id="search" class="gallery-container">
      {{msg}}<br><br>
      <div class="w3-row search-container">
        <div class="w3-third">Sol <small style="color:#555;">(Martian days since the landing min:0 max:{{(images[0])?images[0].rover.max_sol:'3000'}})</small> <input class="w3-input sol-input" type="number" placeholder="sol" ref="sol"></div>
        <div class="w3-third">
          Camera
          <select class="w3-input camera-input" name="camera" id="cam" ref="camera">
            <option value="RHAZ">RHAZ - Rear Hazard</option>
            <option value="FHAZ">FHAZ - Front Hazard</option>
            <option value="NAVCAM">NAVCAM - Navigation</option>
            <option value="MAST">MAST - Terrain Images</option>
          </select>
        </div>
        <div class="w3-third"><br><button class="fetchdata-button w3-btn" v-on:click="getInSightData">Fetch Data</button></div>
      </div>
      
      <div class="gallery">
        <br>{{error}}<br>
        <div class="info" v-if="images[0]">
          Sol: {{images[0].sol}}<br>
          Earth Date: {{images[0].earth_date.split('-').reverse().join('-')}}<br>
          Camera: {{images[0].camera.name}} ({{images[0].camera.full_name}})<br>
          Rover: {{images[0].rover.name}}
        </div>
        <br>
        <div v-if="!groups[0] && !error">Loading Video ....</div>
        <div class="video-container" v-show="vid.length > 1" v-if="vid.length >= 1" v-for="(vid,index) in groups" :key="index">
          <div class="overlay" ref="overlay"></div>
          <span ref="play" style="cursor:pointer;" class="play-button" v-on:click="playVideo(vid,index)">&nbsp; <span class="fa fa-play"></span>&nbsp; </span>
          <img ref="video" class="video" :src="images[Number(vid[0])].img_src.replace('http://','https://')">
        </div>
        <!-- <button class="w3-btn" style="background-color:#ddd;color:#333" v-on:click="()=>{imageLoader();endUpdated = false;}" v-if="endUpdated">Load More Videos</button> -->
        <br><br>
        <h3>Images</h3>
        <div class="image-container" v-for="(image,index) in (images.length<=30)?images:images.slice(0,end)" :key="index+'-'+end">
          <a target="_blank" :href="image.img_src.replace('http://','https://')"><img class="picture" ref="pictures" :data-key="index" :src="image.img_src.replace('http://','https://')"></a>
        </div>  
        <br><button style="background-color:#ddd;color:#333" v-if="images.length > 30 && end <= images.length" v-on:click="()=>{end=end+30;endUpdated = true;getInSightData()}" class="w3-btn">Load More</button>
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
      groups:[],
      end:30,
      endUpdated:false,
      skipWaitingForVideo:false
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
      this.imageDimenstions = [];
      this.groups = [];
      this.imageDimenstions = this.$refs.pictures.map(image=>image.clientHeight + 'x' + image.clientWidth);
      let uniqueDimensions = [...new Set(this.imageDimenstions)];
      for(let dimension of uniqueDimensions){
        this.groups.push(this.imageDimenstions.allIndexesOf(dimension)); // Custom function defined at the start of script tag in this file
      }
      console.log(this.imageDimenstions);
    },
    playVideo:function(arr,index){
      console.log(arr);
      console.log(this.$refs);
      this.$refs.overlay[index].style.display = 'none';
      this.$refs.play[index].style.display = 'none';
      let frame = 0;
      let vidInterval = setInterval(()=>{
        this.$refs.video[index].src = this.images[Number(arr[frame])].img_src.replace('http://','https://');
        frame++;
        if(frame == arr.length){ 
          clearInterval(vidInterval);
          this.$refs.overlay[index].style.display = 'block';
          this.$refs.play[index].style.display = 'inline-block';
        };
      },700);
    },
    imageLoader:function(){
      let count = 0;
      let vue = this;
      for(let image of this.$refs.pictures){
        image.onload = null;
      }
      this.$refs.pictures.slice(this.end-30,this.end).forEach(image=>image.onload = function(){
        count++;
        if(count == vue.images.length || count == vue.end ){
          vue.createVideo();
        }
      })
    }
  },
  mounted() {
    this.getInSightData();
    setTimeout(()=>this.skipWaitingForVideo = true,20000)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.picture{
  height:300px;
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
.play-button{
  position:absolute;
  top:40%;
  left:40%;
  font-size:18pt;
  padding:10px;
  border-radius:50%;
  background-color:#111;
}
.video-container{
  position:relative;
  display:inline-block;
  margin:10px;
}
.overlay{
  position:absolute;
  left:0;top:0;
  height:100%;
  width:100%;
  background-color: #222;
  opacity:.5;
}
.video{
  height:300px;
  width:auto;
}
.image-container{
  display:inline-block;
  padding:10px;
}
@media (max-width:768px){
  .gallery-container{
    padding:25px;
  }
  .play-button{
    font-size:12pt;
    left:45%;
  }
  .video{
    height:auto;
    width:100%;
  }
  .video-container{
    width:90%;
  }
  .image-container{
    width:100%;
    padding:20px 15px;
    text-align:center;
  }
  .picture{
    height:auto;
    width:100%;
  }
}
</style>
