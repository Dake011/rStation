<template>
    <div style="position:fixed; width: 100%; height: 100%; z-index:1; top: 0;">
        <div class="google-map" id="map"></div>
        <input type="button" class="btn btn-danger" @click="saveRoad()" value="Add Road">
    </div>
</template>

<script>
import GoogleMapsLoader from 'google-maps'

    export default {
        name: 'google-map',
         props: ['kzCities'],
        data() {
            return {
                map: null
            }
        },
        computed: {
            mapMarkers: function () {
                return this.markers
            }
        },
        mounted: function () {
           this.init();
           localStorage.stationFrom = null,
           localStorage.stationTo = null
        },
        methods: {
            init(){
                let stationFrom = null, stationTo = null;
                if(localStorage.stationFrom){
                    stationFrom = localStorage.stationFrom;
                }
                if(localStorage.stationTo){
                    stationTo = localStorage.stationTo;
                }
                var stations = this.kzCities;
                GoogleMapsLoader.KEY = 'AIzaSyC89sEOJvI6sPySOghfkKsm7FsLqfZZL98';
                GoogleMapsLoader.LIBRARIES = ['geometry', 'places'];
                GoogleMapsLoader.load(function(google) {
                    var options = {
                        zoom: 5.3,
                        center:{ lat: 48.5, lng: 67},
                        mapTypeId: 'roadmap', //terrain, hybrid, satellite
                        scrollwheel: false,
                        fullscreenControl: false,
                        clickableIcons: false,
                        gestureHandling: 'greedy',
                        keyboardShortcuts: false,
                        mapTypeControl: false,
                        streetViewControl: false,
                        styles: [
                            {elementType: 'geometry', stylers: [{color: '#ebe3cd'},]},
                            {elementType: 'labels.text.fill', stylers: [{color: '#523735', }]},  // label
                            {elementType: 'labels.text.stroke', stylers: [{color: '#f5f1e6'}]},  // label border
                            {
                                featureType: 'administrative',      // region border
                                elementType: 'geometry.stroke',
                                stylers: [{color: '#98B4D4'},]
                            },
                            
                            {
                                featureType: 'administrative.land_parcel',
                                elementType: 'geometry.stroke',
                                stylers: [{color: '#dcd2be'}]
                            },
                            {
                                featureType: 'administrative.country',      // country border
                                elementType: 'geometry.stroke',
                                stylers: [{color: '#5B5EA6'},{weight: "3.01"}]
                            },
                            {
                                featureType: 'administrative.land_parcel',
                                elementType: 'labels.text.fill',
                                stylers: [{color: '#ae9e90'}]
                            },
                            {
                                featureType: 'landscape.natural',       // continent color
                                elementType: 'geometry',
                                stylers: [{color: '#DFCFBE'}]  
                            },
                            {
                                featureType: 'poi',
                                elementType: 'geometry',
                                stylers: [{color: '#dfd2ae'},]
                            },
                            {
                                featureType: 'poi',
                                elementType: 'labels.text.fill',
                                stylers: [{color: '#93817c'}]
                            },
                            {
                                featureType: 'poi.park',
                                elementType: 'geometry.fill',
                                stylers: [{color: '#a5b076'}]
                            },
                            {
                                featureType: 'poi.park',
                                elementType: 'labels.text.fill',
                                stylers: [{color: '#447530'}]
                            }, 
                            {
                                featureType: "road.highway",        // railway road
                                elementType: "geometry",
                                stylers: [{color: "#666666"},{weight: "1.51"},{visibility: "simplified"}]
                            },
                            {
                                featureType: 'water',
                                elementType: 'geometry.fill',
                                stylers: [{color: '#5cd6ff'}]
                            },
                            {
                                featureType: 'water',
                                elementType: 'labels.text.fill',
                                stylers: [{color: '#0066ff'}]
                            },
                        ]
                    }
                    const map = new google.maps.Map(document.getElementById('map'), options);
                    var colorArray = ['#e6194b', '#3cb44b', '#4363d8', '#f58231', '#911eb4', '#46f0f0', '#f032e6', '#bcf60c', '#fabebe', '#008080', '#e6beff', '#9a6324', '#fffac8', '#800000', '#aaffc3', '#808000', '#ffd8b1', '#000075', '#808080', '#ffffff', '#000000'];
                    var flightPlanCoordinates = [];
                    if(localStorage.allRoutes){
                        flightPlanCoordinates =  JSON.parse(localStorage.getItem('allRoutes'))
                    }
                    let i = 0;
                    flightPlanCoordinates.forEach((flightPlanCoordinate) =>{
                        let route = new Array();
                        let j = 0;
                        if(flightPlanCoordinate[0].city === stationFrom || flightPlanCoordinate[0].city === stationTo){
                            route.push(flightPlanCoordinate[0]);
                            j++;
                        }
                        for( let x = 1; x < flightPlanCoordinate.length; x++ ){
                            if(j===1){
                                route.push(flightPlanCoordinate[x]);
                            }
                            if(flightPlanCoordinate[x].city === stationFrom || flightPlanCoordinate[x].city === stationTo){
                                j++;
                            }
                            if(j === 2){
                                var flightPath = new google.maps.Polyline({
                                    path: route,
                                    geodesic: true,
                                    strokeColor: colorArray[i],
                                    strokeOpacity: 0.7,
                                    strokeWeight: 4
                                });
                                flightPath.setMap(map);
                                x = flightPlanCoordinate.length;
                                continue;
                            }
                        }
                        i++;
                    })
                    stations.forEach((station) => {
                        const position = new google.maps.LatLng(station.lat, station.lng)
                        station.map = map,
                        station.position = position,
                        new google.maps.Marker(station)  
                    })
                });
            }
        }
    }
</script>

<style scoped>
    .google-map {
        width: 100%;
        height: 100%;
        background: gray;
        position: absolute;
        z-index: 1;
        top: 0;
    }
</style>