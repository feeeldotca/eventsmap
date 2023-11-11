<template>
    <div id="map" style="height: 100%"></div>
  </template>
  
  <script>
  /* global google */
  import axios from 'axios';
  
  export default {
    name: 'MapView',
    mounted() {
      this.initMap().then(
        ()=>this.getCurrentLocation() )
    },
    data() {
      return {
        events_loc: { lat: 37.4220656, lng: -122.0840897 },
        map: null  // 添加地图实例的存储位置
      };
    },
    methods: {
      async initMap() {
        if (typeof google !== 'undefined') {
          this.map = new google.maps.Map(document.getElementById('map'), {
            center: this.events_loc,  // 使用events_loc作为初始中心点
            zoom: 8
          });

          return new Promise(resolve => {
          google.maps.event.addListenerOnce(this.map, 'idle', resolve);
        });
        }
      },
  
      getCurrentLocation() {
        if ("geolocation" in navigator) {
          navigator.geolocation.getCurrentPosition(position => {
            const currentPos = {
              lat: position.coords.latitude,
              lng: position.coords.longitude
            };
            this.map.setCenter(currentPos);
          });
        } else {
          console.log("Geolocation is not available.");
        }
      },
  
      addMarkers(sales) {
        sales.forEach(sale => {
          new google.maps.Marker({
            position: { lat: sale.lat, lng: sale.lng },
            map: this.map
          });
        });
      },
  
      saveLocation(location) {
        axios.post('/api/save-location', location)
          .then(response => {
            console.log(response); // 处理响应
          })
          .catch(error => {
            console.log(error);
          });
      }  
    }
  };
  </script>
  
  <style>
  /* 确保地图占满组件 */
  #map {
    width: 100%;
    height: 100%;
  }
  </style>
  