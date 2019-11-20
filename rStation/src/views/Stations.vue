<template>
    <div class="container bootstrap snippet">
        <div class="row menu-items">
            <ProfileMenu></ProfileMenu> 
            <div class="menu-data row col-sm-9">
                <div class="station-form">
                    <input type="button" @click="init(true)" class="btn btn-danger add-station" value="Add Station">
                    <input type="button" @click="init(false)" class="btn btn-danger add-station" value="Pick Station">
                    <form class="form" id="registrationForm">
                        <div class="alert-box" v-if="addStation"> 
                            <span>Please click on a station on Map below.</span>
                        </div>
                        <div class="row">
                            <div class="form-group col-4">
                                <div class="col-sm-12">
                                    <label for="stationName"><h4>Station name</h4></label>
                                    <input :class="{ 'active': hasfocus == 1 }" @focusin="focusIn(1)" @focusout="focusOut(1)" type="text" id="stationName" class="form-control prefix" name="stationName" placeholder="Station name" title="enter your first name if any." :disabled="viewMode">
                                </div>
                            </div>
                            <div class="form-group col-4">                        
                                <div class="col-sm-12">
                                    <label for="stationLatitute"><h4>Latitute</h4></label>
                                    <input :class="{ 'active': hasfocus == 2 }" @focusin="focusIn(2)" @focusout="focusOut(2)" type="text" id="stationLatitute" class="form-control" name="stationLatitute" placeholder="73.1234" title="enter your last name if any." :disabled="viewMode">
                                </div>
                            </div>        
                            <div class="form-group col-4">              
                                <div class="col-sm-12">
                                    <label for="stationLongitude"><h4>Longitude</h4></label>
                                    <input :class="{ 'active': hasfocus == 3 }" @focusin="focusIn(3)" @focusout="focusOut(3)" type="text" id="stationLongitude" class="form-control" name="stationLongitude" placeholder="52.12432" title="enter your phone number if any." :disabled="viewMode">
                                </div>
                            </div>
                        </div>
                        <div class="form-group row">
                                <div class="col-md-2">
                                    <button class="btn btn-lg btn-warning" type="button" v-if="viewMode" @click="editData(false)">Edit</button>
                                    <button class="btn btn-lg btn-success" type="button" v-else @click="editData(true)">Save</button>
                                </div>
                                <div class="col-md-2" v-if="!addStation">
                                    <button class="btn btn-lg btn-danger" type="button" @click="deleteData(true)">Delete</button>
                                </div>
                            </div>
                    </form>
                </div>
                <!-- <autocomplete-vue
                    :list="kzCities"
                    property="city"
                    placeholder="Choose Station..."
                    classPrefix="pick-station"
                    inputClass="pick-input"
                    threshold="2"
                ></autocomplete-vue> -->
                <div class="google-map" id="map"></div>
            </div>
        </div>
    </div>
</template>

<script>
import GoogleMapsLoader from 'google-maps'
import ProfileMenu from '../components/ProfileMenu.vue'
import AutocompleteVue from 'autocomplete-vue'
import json from '../assets/kz.json'

    export default {
        components:{
            ProfileMenu,
            'autocomplete-vue': AutocompleteVue
        },
        name: 'google-map',
        data() {
            return {
                kzCities: json,
                map: null,
                clickedRoutes: null,
                hasfocus: 0,
                viewMode: false,
                addStation: false,
                stationName: null,
                stationLatitute: null,
                stationLongitude: null
            }
        },
        computed: {
            mapMarkers: function () {
                return this.markers
            }
        },
        mounted: function () {
            this.init(false)
        },
        methods: {
            init(value){
                var stations = this.kzCities;
                this.addStation = value;
                var addStation = value;
                document.getElementById("stationName").value = '';
                document.getElementById("stationLatitute").value = '';
                document.getElementById("stationLongitude").value = '';
                GoogleMapsLoader.KEY = 'AIzaSyC89sEOJvI6sPySOghfkKsm7FsLqfZZL98';   // Google map api KEY ( Change to your's )
                GoogleMapsLoader.LIBRARIES = ['geometry', 'places'];                // Library for more map options
                GoogleMapsLoader.REGION = ['KZ'];  
                GoogleMapsLoader.load(function(google) {
                    var clickedStations = new google.maps.Marker();
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
                    
                    if(addStation){
                        /*   Click on map   */
                        google.maps.event.addListener(map, 'click', function(event) {
                            clickedStations.setMap(null);
                            var marker = new google.maps.Marker({
                                position: event.latLng, 
                                map: map,
                            });
                            clickedStations = marker;
                            marker.setAnimation(google.maps.Animation.BOUNCE);
                            new google.maps.Marker(marker)  
                            document.getElementById("stationLatitute").value = marker.position.lat();
                            document.getElementById("stationLongitude").value = marker.position.lng();
                        });
                    } 
            
                    /*   Click on marker   */
                    stations.forEach((station) => {
                        station.lat = parseFloat(station.lat);
                        station.lng = parseFloat(station.lng);
                        google.maps.event.addDomListener(station, 'click', function() {
                            document.getElementById("stationName").value = station.city;
                            document.getElementById("stationLatitute").value = station.lat;
                            document.getElementById("stationLongitude").value = station.lng;
                        });
                        const position = new google.maps.LatLng(station.lat, station.lng)
                        station.map = map,
                        station.position = position
                        new google.maps.Marker(station)  
                    })
                });
            },
            focusIn(value) {
                this.hasfocus = value;
            },
            focusOut(value) {
                this.hasfocus = 0;
            },
            editData(value){
                this.viewMode = value;   
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