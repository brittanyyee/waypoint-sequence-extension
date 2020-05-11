<template>
    <div id="app">
        <LeafletMap
            ref="map"
            apiKey="-MbyC0p21GbQh1Z3oSOjXk-W9v97QlSi97HWX4RKkOk"
            lat="35.0645875"
            lng="-89.9674225">
        </LeafletMap>
    </div>
</template>

<script>
import HereMap from './components/HereMap.vue'
import LeafletMap from './components/LeafletMap.vue'

export default {
    name: 'app',
    components: {
        HereMap,
        LeafletMap
    },
    async mounted() {
        let map = this.$refs.map;
        let waypoints = [
            { lat: 35.0645875, lng: -89.9674225 },
            { lat: 33.6352459, lng: -84.3958838 },
            { lat: 35.84216, lng: -78.60133 },
            { lat: 38.62333, lng: -90.22159 },
            { lat: 39.321879, lng: -94.71489 },
            { lat: 39.83817, lng: -83.08471 },
            { lat: 41.90359, lng: -87.6505 },
            { lat: 42.44765, lng: -83.25618 },
            { lat: 38.93662, lng: -94.52892 },
            { lat: 44.45841, lng: -88.09338 }
          ];
        waypoints.forEach(waypoint => {
            map.dropMarker(waypoint);
        });
        let sequence = await map.calculateRouteSequence(waypoints);
        map.drawRoute(waypoints);
    }
}
</script>

<style>
    body {
        margin: 0;
    }
</style>
