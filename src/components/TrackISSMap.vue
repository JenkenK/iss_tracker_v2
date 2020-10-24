<template>
  <section id="map-window">
    <MglMap
      v-if="coordinates"
      :accessToken="accessToken"
      :mapStyle.sync="mapStyle"
      @load="onMapLoaded"
      :center="coordinates"
      :zoom="4"
    >
      <MglMarker v-if="this.issPosition" :coordinates="this.coordinates">
        <img
          id="iss-marker"
          slot="marker"
          src="https://cdn4.iconfinder.com/data/icons/astronomy-and-space-outline-style/512/spase-05-512.png"
        />
      </MglMarker>
      <MglNavigationControl position="top-right" />
      <MglGeolocateControl position="top-right" />
      <MglScaleControl position="bottom-right" />
    </MglMap>
  </section>
</template>

<script>
import Mapbox from "mapbox-gl";
import {
  MglMap,
  MglNavigationControl,
  MglGeolocateControl,
  MglScaleControl,
  MglMarker,
} from "vue-mapbox";

export default {
  name: "Map",
  components: {
    MglMap,
    MglNavigationControl,
    MglGeolocateControl,
    MglScaleControl,
    MglMarker,
  },
  data() {
    return {
      accessToken:
        "pk.eyJ1IjoiamVua2VuayIsImEiOiJja2dtY3V6c3cxMXVqMzBuYW5pYzU3NjRiIn0.7_xgcQPBezjhQB1vsFn70w", // your access token. Needed if you using Mapbox maps
      mapStyle: "mapbox://styles/mapbox/streets-v11", // your map style
      coordinates: undefined,
    };
  },
  created() {
    this.mapbox = Mapbox;
    this.map = null;
  },
  methods: {
    onMapLoaded(event) {
      this.map = event.map;
    },
  },
  watch: {
    issPosition: function (val) {
      this.coordinates = [val.longitude, val.latitude];
    },
  },
  props: ["issPosition"],
};
</script>

<style scoped>
#map-window {
  height: 100vh;
  width: 100vw;
  margin-left: auto;
  margin-right: auto;
}
#iss-marker {
  height: 100px;
}
</style>