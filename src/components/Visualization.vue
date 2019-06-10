<template>
    <div id="visualization">
        <h1>{{ msg }}</h1>
        <l-map ref="map"
               :zoom="zoom"
               :center="center"
               style="height: 200px;">
            <l-tile-layer :url="url"
                          :attribution="attribution"></l-tile-layer>
            <l-geo-json :geojson="statesData.geojson"
                        :options="statesData.options"></l-geo-json>
            <l-marker :lat-lng="marker"></l-marker>
        </l-map>
    </div>
</template>

<script>
    import L from 'leaflet';
    import { LMap, LTileLayer, LMarker, LGeoJson } from 'vue2-leaflet';
    import data from '../assets/geojson/us-states';

    delete L.Icon.Default.prototype._getIconUrl;
    L.Icon.Default.mergeOptions({
        iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
        iconUrl: require('leaflet/dist/images/marker-icon.png'),
        shadowUrl: require('leaflet/dist/images/marker-shadow.png')
    });

    function styleFunction(feature) {
        return {
            fillColor: getColor(feature.properties.density),
            weight: 2,
            opacity: 1,
            color: 'white',
            dashArray: '3',
            fillOpacity: 0.7
        }
    }

    function getColor(populationDensity) {
        return populationDensity > 1000 ? '#800026' :
            populationDensity > 500  ? '#BD0026' :
            populationDensity > 200  ? '#E31A1C' :
            populationDensity > 100  ? '#FC4E2A' :
            populationDensity > 50   ? '#FD8D3C' :
            populationDensity > 20   ? '#FEB24C' :
            populationDensity > 10   ? '#FED976' :
                                    '   #FFEDA0';
    }

    export default {
        name: 'Visualization',
        props: {
            msg: String
        },
        components: {
            LMap,
            LTileLayer,
            LMarker,
            LGeoJson
        },
        data () {
            return {
                loading: false,
                zoom: 5,
                center: L.latLng(43.092641, -89.532142),
                url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png',
                attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                marker: L.latLng(43.092641, -89.532142),
                statesData: {
                    geojson: data.statesData,
                    options: {
                        style: function(feature) {
                            return styleFunction(feature)
                        }
                    }
                }
            }
        }
    }
</script>

<style scoped lang="scss">
    @import "~leaflet/dist/leaflet.css";
</style>