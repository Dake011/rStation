<template>
    <div class="container bootstrap snippet">
        <div class="row menu-items">
            <ProfileMenu></ProfileMenu> 
            <div class="menu-data row col-sm-9">
                <div class="station-form">
                    <input type="button" @click="$router.push('/add-routes')" class="btn btn-danger add-station" value="Add Route">
                    <input type="button" @click="$router.push('/pick-routes')" class="btn btn-danger add-station" value="Pick Route">
                    <div class="alert-box"> 
                        <span>Please click on a stations on Map below to make a Route.</span>
                    </div>
                    <div class="google-map" id="map"></div>
                    <form class="form" id="registrationForm">
                        <div class="row" v-for="coords in clickedCoords" :key="coords.city">
                            <div class="form-group col-4">
                                <div class="col-sm-12">
                                    <label for="stationName"><h4>Station Name</h4></label>
                                    <input :class="{ 'active': hasfocus == 1 }" @focusin="focusIn(1)" @focusout="focusOut(1)" :value="coords.city" type="text" id="stationName" class="form-control prefix" name="stationName" placeholder="Station name" title="enter your first name if any." :disabled="viewMode">
                                </div>
                            </div>
                            <div class="form-group col-4">                        
                                <div class="col-sm-12">
                                    <label for="stationLatitute"><h4>Arrival Time</h4></label>
                                    <input :class="{ 'active': hasfocus == 2 }" @focusin="focusIn(2)" @focusout="focusOut(2)" type="text" id="stationLatitute" class="form-control" name="stationLatitute" placeholder="" title="enter your last name if any." :disabled="viewMode">
                                </div>
                            </div>        
                            <div class="form-group col-4">              
                                <div class="col-sm-12">
                                    <label for="stationLongitude"><h4>Departure Time</h4></label>
                                    <input :class="{ 'active': hasfocus == 3 }" @focusin="focusIn(3)" @focusout="focusOut(3)" type="text" id="stationLongitude" class="form-control" name="stationLongitude" placeholder="" title="enter your phone number if any." :disabled="viewMode">
                                </div>
                            </div>
                        </div>
                        <div class="row" v-if="clickedCoords.length > 0">
                            <div class="form-group col-4">
                                <div class="col-sm-12">
                                    <label for="stationName"><h4>Choose a Train</h4></label>
                                    <select class="browser-default custom-select">
                                        <option selected>Chose a train</option>
                                        <option v-for="train in trains" :key="train" :selected="test === train">{{train}}</option>
                                    </select>
                                </div>
                            </div>
                            <div class="form-group col-4">                        
                                <div class="col-sm-12">
                                    <label for="stationLatitute"><h4>Choose start day</h4></label>
                                    <input type="date" id="stationName" class="form-control prefix" placeholder="Station name">
                                </div>
                            </div>        
                            <div class="form-group col-4">              
                                <div class="col-sm-12">
                                    <label for="stationLongitude"><h4>Choose days interval</h4></label>
                                    <input type="number" id="stationName" class="form-control prefix" placeholder="Station name">
                                </div>
                            </div>
                        </div>
                        <div class="row" v-if="clickedCoords.length > 0">
                            <div class="col-1"></div>
                            <div class="col-4 custom-control custom-checkbox">
                                <input v-model="addOpposite" @change="addOppositeDir(addOpposite)" type="checkbox" class="custom-control-input" id="defaultUnchecked">
                                <label class="custom-control-label" for="defaultUnchecked">Add Train for Opposite direction</label>
                            </div>
                        </div>
                    </form>
                    <div class="alert-box" v-if="addOpposite"> 
                        <span>Another direction.</span>
                    </div>
                    <form class="form" id="registrationForm" v-if="addOpposite">
                        <div class="row" v-for="coords in clickedCoordsOpposite" :key="coords.city">
                            <div class="form-group col-4">
                                <div class="col-sm-12">
                                    <label for="stationName"><h4>Station Name</h4></label>
                                    <input :class="{ 'active': hasfocus == 1 }" @focusin="focusIn(1)" @focusout="focusOut(1)" :value="coords.city" type="text" id="stationName" class="form-control prefix" name="stationName" placeholder="Station name" title="enter your first name if any." :disabled="viewMode">
                                </div>
                            </div>
                            <div class="form-group col-4">                        
                                <div class="col-sm-12">
                                    <label for="stationLatitute"><h4>Arrival Time</h4></label>
                                    <input :class="{ 'active': hasfocus == 2 }" @focusin="focusIn(2)" @focusout="focusOut(2)" :value="coords.arrTime" type="text" id="stationLatitute" class="form-control" name="stationLatitute" placeholder="" title="enter your last name if any." :disabled="viewMode">
                                </div>
                            </div>        
                            <div class="form-group col-4">              
                                <div class="col-sm-12">
                                    <label for="stationLongitude"><h4>Departure Time</h4></label>
                                    <input :class="{ 'active': hasfocus == 3 }" @focusin="focusIn(3)" @focusout="focusOut(3)" :value="coords.depTime"  type="text" id="stationLongitude" class="form-control" name="stationLongitude" placeholder="" title="enter your phone number if any." :disabled="viewMode">
                                </div>
                            </div>
                        </div>
                        <div class="row" v-if="clickedCoordsOpposite.length > 0">
                            <div class="form-group col-4">
                                <div class="col-sm-12">
                                    <label for="stationName"><h4>Choose a Train</h4></label>
                                    <select class="browser-default custom-select">
                                        <option selected>Chose a train</option>
                                        <option v-for="train in trains" :key="train" :selected="test === train">{{train}}</option>
                                    </select>
                                </div>
                            </div>
                            <div class="form-group col-4">                        
                                <div class="col-sm-12">
                                    <label for="stationLatitute"><h4>Choose start day</h4></label>
                                    <input type="date" id="stationName" class="form-control prefix" placeholder="Station name">
                                </div>
                            </div>        
                            <div class="form-group col-4">              
                                <div class="col-sm-12">
                                    <label for="stationLongitude"><h4>Choose days interval</h4></label>
                                    <input type="number" id="stationName" class="form-control prefix" placeholder="Station name">
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
                <input type="button" class="btn btn-danger" @click="saveRoute()" value="Add Route">
                <input type="button" class="btn btn-default" @click="resetRoute()" value="Reset">
            </div>
        </div>
    </div>
</template>

<script>
import GoogleMapsLoader from 'google-maps'
import ProfileMenu from '../components/ProfileMenu.vue'
import json from '../assets/kz.json'

    export default {
        components:{
            ProfileMenu,
        },
        name: 'google-map',
        data() {
            return {
                kzCities: json,
                map: null,
                clickedRoutes: null,
                clickedCoords: [],
                clickedCoordsOpposite: [],
                hasfocus: 0,
                viewMode: false,
                stationName: null,
                stationLatitute: null,
                stationLongitude: null,
                trains:[1,2,3],
                test: null,
                addOpposite: false
            }
        },
        computed: {
            mapMarkers: function () {
                return this.markers
            }
        },
        mounted: function () {
            this.init()
        },
        methods: {
            init(){
                var vm = this;
                var stations = this.kzCities;
                let clickedCoords = new Array();
                GoogleMapsLoader.KEY = 'AIzaSyC89sEOJvI6sPySOghfkKsm7FsLqfZZL98';   // Google map api KEY ( Change to your's )
                GoogleMapsLoader.LIBRARIES = ['geometry', 'places'];                // Library for more map options
                GoogleMapsLoader.REGION = ['KZ'];  
                GoogleMapsLoader.load(function(google) {
                    var options = {
                        zoom: 5,
                        center:{ lat: 48.8, lng: 66.9},     // Center of Map
                        mapTypeId: 'roadmap',               // terrain, hybrid, satellite
                        scrollwheel: false,                 // via mouse scroll
                        fullscreenControl: true,           // full page by Click
                        gestureHandling: 'greedy',          // Instruction
                        keyboardShortcuts: false,           // Add keyboard control options
                        mapTypeControl: false,              // Change type of Map
                        streetViewControl: false,           // 3D View ( Street Cross )s

                        /*      Custom Map styling      */
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
                    flightPlanCoordinates.forEach((flightPlanCoordinate) => {
                        var flightPath = new google.maps.Polyline({
                            path: flightPlanCoordinate,
                            geodesic: true,
                            strokeColor: colorArray[i],
                            strokeOpacity: 0.7,
                            strokeWeight: 4
                        });
                        flightPath.setMap(map);
                        var poly = new google.maps.Polyline({
                            strokeColor: '#000000',
                            strokeOpacity: 1.0,
                            strokeWeight: 2
                        });
                        poly.setMap(map);
                        i++;
                    })

                    /*   Click on marker   */
                    stations.forEach((station) => {
                        station.lat = parseFloat(station.lat);
                        station.lng = parseFloat(station.lng);
                        google.maps.event.addDomListener(station, 'click', function() {
                            if (clickedCoords.filter(e => e.city === station.city).length == 0){
                                clickedCoords.push({
                                    lat: station.lat,
                                    lng: station.lng,
                                    city: station.city
                                });
                                vm.clickedCoords.push({
                                    city: station.city,
                                    arrTime: '',
                                    depTime: ''
                                })
                                vm.clickedCoordsOpposite.unshift({
                                    city: station.city,
                                    arrTime: '',
                                    depTime: ''
                                })
                            }
                            var flightPath = new google.maps.Polyline({
                                path: clickedCoords,
                                geodesic: true,
                                strokeColor: '#FF0000',
                                strokeOpacity: 1.0,
                                strokeWeight: 4
                            });
                            flightPath.setMap(map);                        
                        });
                        const position = new google.maps.LatLng(station.lat, station.lng)
                        station.map = map,
                        station.position = position
                        new google.maps.Marker(station)  
                    })
                });
                this.clickedRoutes = clickedCoords;
            },
            saveRoute(){
                var allRoutes = new Array();
                var routes = new Array();
                this.clickedRoutes.forEach((route) => {
                    routes.push({
                        lat: route.lat,
                        lng: route.lng,
                        city: route.city
                    });
                })
                if(localStorage.allRoutes){
                    allRoutes =  JSON.parse(localStorage.getItem('allRoutes'))
                } 
                allRoutes.push(routes);
                localStorage.setItem('allRoutes', JSON.stringify(allRoutes));
                location.reload();
            },
            resetRoad(){
                window.location.reload()
            },
            focusIn(value) {
                this.hasfocus = value;
            },
            focusOut(value) {
                this.hasfocus = 0;
            },
            editData(value){
                this.viewMode = value;   
            },
            addOppositeDir(a){
                console.log(a)
            }
        }
    }
</script>

<style scoped lang="scss">
    .google-map {
        width: 100%;
        height: 540px;
        background: rgba(0,0,0,0.1);
        border: 4px solid rgba(0,0,0,0.1);
        border-radius: 6px;
        margin: 40px 0 0 0;
    }
    .container{
        margin: auto;
        margin-top: 138px;
        max-width: 88% !important;
        background: #fff;
        border: 1px solid rgba(0,0,0,0.3);
        padding: 16px;
        border-radius: 8px;
        .menu-data{
            padding: 0;
            .pick-station{
                z-index: 20;
                overflow: hidden;
                background: #fff;
                border: rgba(0,0,0,0.8);
                .pick-input{
                    border: 1px solid red;
                }
            }
            .station-form{
                width: 100%;
                .add-station{
                    z-index: 20;
                }
                .alert-box{
                    border: 1px solid #009B77;
                    border-radius: 6px;
                    background: #00e6b0;
                    width: 100%;
                    padding: 12px 18px;
                }
                .form-group{
                    position: relative;
                    margin-top: 1.5rem;
                    margin-bottom: 1.5rem;
                    label h4{
                        font-size: 14px;
                        font-weight: 400;
                        margin: 0;
                    }
                    .form-control{
                        background-color: transparent;
                        border: none;
                        border-bottom: 1px solid #ced4da;
                        border-radius: 0;
                        outline: none;
                        padding-left: 0.2rem;
                        height: auto;
                        margin: 0 0 0.5rem 0;
                        border-radius: 0;
                        font-size: 14px;
                    }
                    .active{
                        border-color: #4285f4;
                    }
                    button.btn{
                        padding: 0.4rem 1.4rem;
                        font-size: 14px;
                    }
                }
                ::placeholder{
                    opacity: 0.3;
                }
            }
        }
    }

</style>