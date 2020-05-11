<template>
  <div>
    <div ref="map" id="map"></div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "LeafletMap",
  data() {
    return {
      platform: {},
      map: {},
      sequenceMarkerGroup: {}
    }
  },
  props: {
    apiKey: String,
    lat: String,
    lng: String,
  },
  
  created() {
      this.sequenceMarkerGroup = new L.featureGroup();
  },
  mounted() {
    this.map = new L.Map(this.$refs.map, {
      center: [this.lat, this.lng],
      zoom: 10,
      layers: [
        L.tileLayer(
          "https://2.base.maps.ls.hereapi.com/maptile/2.1/maptile/newest/normal.day/{z}/{x}/{y}/512/png8?apiKey=-MbyC0p21GbQh1Z3oSOjXk-W9v97QlSi97HWX4RKkOk",
          {
            apiKey: this.apiKey,
          }
                        )
                    ]
                }
            )
        },
        methods: {
            calculateRouteSequence(waypoints) {
                let destinations = {};
                for(let i = 0; i < waypoints.length; i++) {
                    destinations["destination" + (i + 1)] = `${waypoints[i].lat},${waypoints[i].lng}`
                }
                return axios(
                    {
                        url: "https://wse.ls.hereapi.com/2/findsequence.json",
                        method: "GET",
                        params: {
                            mode: "fastest;car",
                            start: `${this.lat},${this.lng}`,
                            end: `${this.lat},${this.lng}`,
                            maneuverattributes: 'bt,di,le,tt',
                            routeattributes: 'sm,wp',
                            apiKey: this.apiKey,
                            ...destinations
                        }
                    }
                ).then(result => {
                    return result.data.results[0].waypoints;
                })
            },
            drawRoute(sequence) {
                let waypoints = {};
                for(let i = 0; i < sequence.length; i++) {
                    waypoints["waypoint" + i] = `${sequence[i].lat},${sequence[i].lng}`;
                }
                axios(
                    {
                        url: "https://route.ls.hereapi.com/routing/7.2/calculateroute.json",
                        method: "GET",
                        params: {
                            apiKey: this.apiKey,
                            mode: "fastest;car",
                            maneuverattributes: 'bt,di,le,tt',
                            routeattributes: 'sm,wp',
                            representation: "display",
                            ...waypoints
                        }
                    }
                ).then(result => {
                    let shape = result.data.response.route[0].shape;
                    let positions = shape.map(point => point.split(","));
                    L.polyline(positions).addTo(this.map).snakeIn();
                });
            },
           dropMarker(position) {
                L.marker(position).addTo(this.map);
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #map {
        width: 100vw; 
        height: 100vh; 
        background-color: pink;
    }
</style>
