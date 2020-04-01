<template>
  <div class="map">
    <div class="google-map" id="map" ref="map"></div>
  </div>
</template>

<script>
import firebase, { db } from "@/firebase";
export default {
  name: "GMap",
  data: () => ({
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
          ...this.center
        },
        zoom: 8,
        maxZoom: 15,
        minZoom: 3,
        streetViewControl: false
      });
      db.collection("geo-users")
        .get()
        .then(users => {
          users.docs.forEach(doc => {
            let data = doc.data();
            if (data.geolocation) {
              const { lat, lng } = data.geolocation;
              let marker = new window.google.maps.Marker({
                position: {
                  lat,
                  lng
                },
                map: this.map
              });
              marker.addListener("click", () => {
                this.$router.push({ name: "Profile", params: { id: doc.id } });
              });
            }
          });
        });
    },
    geolocate() {
      let user = firebase.auth().currentUser;
      navigator.geolocation.getCurrentPosition(
        position => {
          this.center = {
            lat: position.coords.latitude,
            lng: position.coords.longitude
          };
          let ref = db.collection("geo-users");
          ref
            .where("userId", "==", user.uid)
            .get()
            .then(snapshot => {
              snapshot.forEach(doc => {
                console.log(doc.data());
                ref.doc(doc.id).update({
                  geolocation: {
                    lat: position.coords.latitude,
                    lng: position.coords.longitude
                  }
                });
              });
            })
            .then(() => {
              this.renderMap();
            });
        },
        err => {
          console.log(err);
          this.renderMap();
        },
        { maximumAge: 60000, timeout: 3000 }
      );
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
  // watch: {
  //   center: function() {
  //     this.renderMap();
  //   }
  // },
  mounted() {
    if (navigator.geolocation) {
      this.geolocate();
    } else {
      this.renderMap();
    }
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
