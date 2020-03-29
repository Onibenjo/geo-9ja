<template>
  <div class="map">
    <div class="google-map" id="map" ref="map"></div>
  </div>
</template>

<script>
import firebase from "@/firebase";
export default {
  name: "GMap",
  data: () => ({
    lat: -25,
    lng: 130,
    center: { lat: -2, lng: 130 },
    map: null,
    markers: [],
    places: [],
    currentPlace: null
  }),
  methods: {
    renderMap() {
      this.map = new window.google.maps.Map(this.$refs["map"], {
        center: {
          //   lat: this.lat,
          //   lng: this.lng
          ...this.center
        },
        zoom: 6,
        maxZoom: 15,
        minZoom: 3,
        streetViewControl: false
      });
      new window.google.maps.Marker({
        position: {
          ...this.center
        },
        map: this.map
      });
    },
    getMap(cb) {
      function checkForMap() {
        if (this.map) cb(this.map);
        else setTimeout(checkForMap, 200);
      }
      checkForMap();
    },
    geolocate() {
      navigator.geolocation.getCurrentPosition(position => {
        this.center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };
        // this.renderMap();
      });
    },
    setPlace(place) {
      this.currentPlace = place;
    },
    addMarker() {
      if (this.currentPlace) {
        const marker = {
          lat: this.currentPlace.geometry.location.lat(),
          lng: this.currentPlace.geometry.location.lng()
        };
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;
      }
    }
  },
  watch: {
    center: function() {
      this.renderMap();
    }
  },
  mounted() {
    this.renderMap();
    this.geolocate();
    console.log(firebase.auth().currentUser);
  }
};
</script>

<style>
.google-map {
  width: 100%;
  height: 100%;
  margin: 0 auto;
  background: #fff;
  position: absolute;
  top: 0;
  left: 0;
  z-index: -1;
}
</style>
