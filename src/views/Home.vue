<template>
  <div class="home">
    <div class="banner" style="">
      <h2>IP Address Tracker</h2>
      <form>
        
        <input type="text" placeholder="Seach for any IP address or domain" v-model="locator" />
        <button @click="checkLocation"><img src="@/assets/icon-arrow.svg" alt="" /></button>
        <div class="preload" v-show="showPreload"></div>
        
      </form>
    </div>
     <!-- <div id="mapid"></div> -->
    <div class="map">
      <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      style="height: 100%"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
      <l-marker :lat-lng="withPopup" >
        <l-popup>
          
        </l-popup>
        <l-icon
          :icon-anchor="staticAnchor"
          class-name="someExtraClass"
        >
          <div class="headline">
            {{ customText }}
          </div>
          <img src="@/assets/icon-location.svg">
        </l-icon>
      </l-marker>
      <!-- <l-marker :lat-lng="withTooltip">
      </l-marker> -->
    </l-map>
    </div>
    <div class="map-desc">
      <div class="map-content">
        <p>ip address</p>
        <h2>{{this.userDetails.ip}}</h2>
      </div>
      <div class="map-content">
        <p>location</p>
        <h2>{{this.userDetails.location.city}}, {{this.userDetails.location.country}} {{this.userDetails.location.geonameId}}</h2>
      </div>
      <div class="map-content">
        <p>timezone</p>
        <h2>{{this.userDetails.location.timezone}}</h2>
      </div>
      <div class="map-content">
        <p>isp</p>
        <h2>{{this.userDetails.isp}}</h2>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { LMap, LTileLayer, LMarker, LPopup, LIcon } from "vue2-leaflet";
import { latLng  } from "leaflet";
export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LIcon,
    // icon
    // LTooltip
  },
  data() {
    return {
      locator: '',
      city: '',
      ip: '',
      userDetails: [],
      showPreload: false,
     zoom: 15,
      center: null,
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      withPopup: null,
      withTooltip: null,
      currentZoom: 1.5,
      currentCenter: null,
      showParagraph: false,
      mapOptions: {
        zoomSnap: 1
      },
      showMap: true,
      icon: "@/assets/icon.arrow.svg"
    };
  },
  methods: {
    // User IP Address
    userLocation() {
      this.axios.get("https://api.ipify.org").then(response => {
        this.ip = response.data.ip
        // console.log(this.ip);
      });
    },
    // User Map
    userMap(){
      var api_key = 'at_0I0xMEJB0mGgKKIH5Uh704NeDlHyn';
      var api_url = 'https://geo.ipify.org/api/v1?';
      var url = api_url + 'apiKey=' + api_key + '&ipAddress=' + this.ip;
      this.axios.get(url).then(response => {
        this.userDetails = response.data
        this.center = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withPopup = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withTooltip = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.currentCenter = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        console.log(this.userDetails);
      });
    },
    // Map Properties
     zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    // Checking for Location
    checkLocation(e){
      e.preventDefault()
      this.showPreload = true
      var api_key = 'at_0I0xMEJB0mGgKKIH5Uh704NeDlHyn';
      var api_url = 'https://geo.ipify.org/api/v1?';
      var url = api_url + 'apiKey=' + api_key + '&ipAddress=' + this.locator;
      this.axios.get(url).then(response => {
        this.showPreload = false
        this.userDetails = response.data
        this.center = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withPopup = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.withTooltip = latLng(this.userDetails.location.lat, this.userDetails.location.lng);
        this.currentCenter = latLng(this.userDetails.location.lat, this.userDetails.location.lng);

      });
    }
  },
  mounted() {
    // this.userLocation()
    // this.userMap()
    // this.map = L.map('map')
  },
  created(){
    this.userLocation()
    this.userMap()  
  },
  updated(){
    // this.checkLocation()
  }
};
</script>
<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
.banner {
  background-image: url("../assets/pattern-bg.png");
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  width: 100%;
  height: 38vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.banner h2 {
  color: white;
  padding-top: 1em;
}
.banner form {
  width: 40%;
  margin-top: 20px;
  display: flex;
  flex-direction: row;
  position: relative;
}
.banner input {
  border: none;
  width: 100%;
  height: 50px;
  padding-left: 20px;
  border-top-left-radius: 15px;
  border-bottom-left-radius: 15px;
  font-size: 16px;
  border-style: none;
  font-weight: 400;
  font-family: "Rubik", sans-serif;
  
}
.preload{
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border-top: 4px solid hsl(0, 0%, 17%);
  border-right: 4px solid hsl(0, 0%, 17%);
  border-bottom: 4px solid hsl(0, 0%, 17%);
  border-left: 4px solid hsl(0, 0%, 59%);
  position: absolute;
  right: 50px;
  margin-top: 10px;
  animation: roll 1s linear infinite;
}
@keyframes roll{
  from{
    transform: rotate(0deg);
  }
  to{
    transform: rotate(360deg)
  }
}
.banner button {
  background: hsl(0, 0%, 17%);
  padding: 10px 15px;
  border-top-right-radius: 15px;
  border-bottom-right-radius: 15px;
  border: none;
  cursor: pointer;
}
.banner input:focus,
.banner button:focus {
  outline: none;
}
.map {
  width: 100%;
  height: 62vh;
}
.map-desc {
  width: 80%;
  background: #fff;
  border-radius: 15px;
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10 !important;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.map-desc .map-content {
  width: 24%;
  padding: 0 25px;
  text-align: left;
  margin: 30px 0px;
  border-right: 1px solid hsl(0, 0%, 59%);
}
.map-desc .map-content:last-child {
  border-right: none;
}
.map-desc .map-content p {
  font-family: "Rubik", sans-serif;
  text-transform: uppercase;
  font-weight: bold;
  color: hsl(0, 0%, 59%);
  font-size: 12px;
  letter-spacing: 2px;
  margin-bottom: 15px;
}
.map-desc .map-content h2 {
  font-family: "Rubik", sans-serif;
  text-transform: capitalize;
  font-size: 20px;
}
.vue2leaflet-map{
  z-index: 0 !important;
} 

@media(max-width: 576px){
  .banner form {
  width: 90%;
  /* margin-top: 20px; */
  /* display: flex; */
  /* flex-direction: row; */
}
  .map-desc {
  width: 90%;
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10 !important;
  display: flex;
  flex-direction: column;
}
.map-desc .map-content {
  width: 100%;
  padding: 0 25px;
  margin: 10px 0px;
  text-align: center;
}
.map-desc .map-content h2 {
  font-size: 16px;
}
}
@media(max-width: 375px){
  .banner form {
  width: 90%;
  /* margin-top: 20px; */
  /* display: flex; */
  /* flex-direction: row; */
}
  .map-desc {
  width: 90%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 10 !important;
  display: flex;
  flex-direction: column;
}
.map-desc .map-content {
  width: 100%;
  padding: 0 25px;
  margin: 10px 0px;
  text-align: center;
}
.map-desc .map-content h2 {
  font-size: 16px;
}
}
</style>
