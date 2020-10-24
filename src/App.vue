<template>
  <main>
    <div id="iss-info">
      <h1>ISS Tracker</h1>
      <div>Time: {{ this.issTimeStamp }}</div>
      <div>Date: {{ this.issDate }}</div>
      <br />
      <div>
        Longitude:<span v-if="this.issPosition">
          {{ this.issPosition.longitude }}</span
        >
      </div>
      <div>
        Latitude:<span v-if="this.issPosition">
          {{ this.issPosition.latitude }}</span
        >
      </div>
      <br />
      <div>
        Altitude:<span v-if="this.issAltitude"> {{ this.issAltitude }}km</span>
      </div>
      <div>
        Velocity:<span v-if="this.issVelocity">
          {{ this.issVelocity }}km/h</span
        >
      </div>
    </div>
    <track-iss-map v-bind:issPosition="issPosition"></track-iss-map>
  </main>
</template>

<script>
import TrackISSMap from "./components/TrackISSMap.vue";

export default {
  name: "app",
  data() {
    return {
      hasData: false,
      issPosition: {
        longitude: 0,
        latitude: 0,
      },
      issUnixTimeStamp: null,
      issTimeStamp: null,
      issDate: null,
      issAltitude: null,
      issVelocity: null,
    };
  },
  mounted() {
    this.fetchData();
    setInterval(() => {
      this.fetchData();
    }, 3000);
  },
  methods: {
    fetchData: function () {
      fetch("https://api.wheretheiss.at/v1/satellites/25544")
        .then((res) => res.json())
        .then((json) => {
          this.issPosition = {
            longitude: json.longitude.toFixed(4),
            latitude: json.latitude.toFixed(4),
          };
          this.issAltitude = json.altitude.toFixed(2);
          this.issVelocity = json.velocity.toFixed(2);
          this.issUnixTimeStamp = json.timestamp;
          this.convertUnixTimeStamp();
        });
    },
    convertUnixTimeStamp: function () {
      const unixTime = this.issUnixTimeStamp;
      const months_arr = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec",
      ];
      const date = new Date(unixTime * 1000);
      const year = date.getFullYear();
      const month = months_arr[date.getMonth()];
      const day = date.getDate();
      const hours = date.getHours();
      const minutes = "0" + date.getMinutes();
      const seconds = "0" + date.getSeconds();
      const formattedTime =
        hours + ":" + minutes.substr(-2) + ":" + seconds.substr(-2);
      this.issTimeStamp = formattedTime;
      const formattedDate = day + "-" + month + "-" + year;
      this.issDate = formattedDate;
    },
  },
  computed: {},
  components: {
    "track-iss-map": TrackISSMap,
  },
};
</script>

<style scoped>
main {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  text-align: center;
  margin: 0;
}

#iss-info {
  position: absolute;
  background-color: white;
  top: 0;
  left: 0;
  padding: 20px 30px;
  z-index: 100;
}
</style>
