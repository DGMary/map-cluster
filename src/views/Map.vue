<template>
  <div class="map">
    <h1>This is an map page</h1>
  </div>
</template>
<script>
// import MarkerClusterer from '@google/markerclusterer';
import MarkerClusterer from "@googlemaps/markerclustererplus";
import gmapsInit from "../utils/gmaps";
import locations from "../utils/location.js";

var styles = [
  [
    MarkerClusterer.withDefaultStyle({
      width: 35,
      height: 35,
      url: "../images/people35.png",
      textColor: "red",
      textSize: 20,
    }),
    MarkerClusterer.withDefaultStyle({
      width: 45,
      height: 45,
      url: "../images/people45.png",
      textColor: "#ff0000",
      textSize: 11,
    }),
    MarkerClusterer.withDefaultStyle({
      width: 55,
      height: 55,
      url: "../images/people55.png",
      textColor: "#ffffff",
      textSize: 12,
    }),
  ], 
  [
    {
      width: 10,
      height: 10,
      className: "custom-clustericon-1",
    },
    {
      width: 15,
      height: 15,
      className: "custom-clustericon-2",
    },
    {
      width: 20,
      height: 20,
      className: "custom-clustericon-3",
    },
  ],
];

// const image = "https://tehnoezh.ua//local/templates/tehnoezh-modern/images/map-marker.svg";
const image =  {
  path: "M16.734,0C9.375,0,3.408,5.966,3.408,13.325c0,11.076,13.326,20.143,13.326,20.143S30.06,23.734,30.06,13.324C30.06,5.965,24.093,0,16.734,0z M16.734,19.676c-3.51,0-6.354-2.844-6.354-6.352c0-3.508,2.844-6.352,6.354-6.352c3.508-0.001,6.352,2.845,6.352,6.353C23.085,16.833,20.242,19.676,16.734,19.676z",
  strokeColor: "black",
  strokeOpacity: 1,
  strokeWeight: 1,
  fillColor: "darkblue",
  fillOpacity: 1,
  scale: 1.0
};
console.log(image);

export default {
  name: "Map",
  data: () => ({
  }),
  async mounted() {
    try {
      const google = await gmapsInit();
      const geocoder = new google.maps.Geocoder();
      const map = new google.maps.Map(this.$el);
      geocoder.geocode({ address: `Austria` }, (results, status) => {
        if (status !== `OK` || !results[0]) {
          throw new Error(status);
        }
        map.setCenter(results[0].geometry.location);
        map.fitBounds(results[0].geometry.viewport);
      });
      const markerClickHandler = marker => {
        map.setZoom(13);
        map.setCenter(marker.getPosition());
      };
      const markers = locations
        .map((location) => {
          const marker = new google.maps.Marker({ ...location, map , icon: image, });
          marker.addListener(`click`, () => markerClickHandler(marker));
          return marker;
        });
      // eslint-disable-next-line no-new
      new MarkerClusterer(map, markers, {
        styles: styles[1],
        clusterClass: "custom-clustericon",
      });
    } catch (error) {
      // eslint-disable-next-line no-console
      console.error(error);
    }
  },
};
</script>
<style>
html,
.map {
  width: 500px;
  height: 500px;
  margin:0 auto;
}
.custom-clustericon span {
  font-size: 14px;
  font-weight: bold;
}
.custom-clustericon-1:after {
    background-color: red;
    box-shadow: 0 0 8px rgba(0,0,0,.3);
}

.custom-clustericon-2:after {
  background-color:green;
  box-shadow: 0 0 8px rgba(0,0,0,.3);
}
.custom-clustericon-3:after {
  background-color: darkblue;
  box-shadow: 0 0 8px rgba(0,0,0,.3);
}
.custom-clustericon:before {
    content: '';
    position: relative;
    display: block;
    width: 250%;
    height: 250%;
    box-sizing: border-box;
    margin-left: -75%;
    margin-top: -75%;
    border-radius: 50%;
    background: #fff;
    animation: pulse-ring 1.25s cubic-bezier(0.215, 0.61, 0.355, 1) infinite;
  }
  
.custom-clustericon:after {
    content: '';
    position: absolute;
    left: 0; 
    top: 0;
    display: block;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    animation: pulse-dot 1.25s cubic-bezier(0.455, 0.03, 0.515, 0.955) -.4s infinite;
}
@keyframes pulse-ring {
  0% {
    transform: scale(.63);
  }
  80%, 100% {
    opacity: 0;
  }
}

@keyframes pulse-dot {
  0% {
    transform: scale(.8);
  }
  50% {
    transform: scale(1);
  }
  100% {
    transform: scale(.8);
  }
}

</style>

