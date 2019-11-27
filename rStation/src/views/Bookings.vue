<template>
    <div class="container bootstrap snippet">
        <div class="row menu-items">
            <ProfileMenu></ProfileMenu> 
            <div class="menu-data row col-sm-9">
                <BookingModal :booking="temp" v-if="showModal" @close="showModal=false"></BookingModal>
                <table>
                    <thead>
                        <tr class="table-headers">
                            <th>TicketID</th>
                            <th>From Station</th>
                            <th>To Station</th>
                            <th>Name</th>
                            <th>Mail</th>
                            <th>ID number</th>
                            <th>DeparturesAt</th>
                        </tr>
                    </thead>
                    <tbody v-for="booking in bookings" :key="booking.TicketID">
                        <tr>
                            <td class="bookingId" @click="openModal(booking)">{{booking.TicketID}}</td>
                            <th class="mobile-header">From_StationID</th><td>{{stations.find(x => x.StationID === booking.From_StationID).name}}</td>
                            <th class="mobile-header">To_StationID</th><td>{{stations.find(x => x.StationID === booking.To_StationID).name}}</td>
                            <th class="mobile-header">Name</th><td>{{booking.Name + ' ' + booking.Surname[0]}}.</td>
                            <th class="mobile-header">Passanger_mail</th><td>{{booking.Passanger_mail}}</td>
                            <th class="mobile-header">CreatedAt</th><td>{{booking.CreatedAt}}</td>
                            <th class="mobile-header">DeparturesAt</th><td>{{booking.DeparturesAt.slice(0, 10)}}</td>
                        </tr>
                    </tbody>
                </table>
            </div><!--/col-9-->
        </div>
    </div>
</template>
<script>
import ProfileMenu from '../components/ProfileMenu.vue'
import BookingModal from '../components/BookingModal.vue'
import axios from 'axios'

export default {
    components:{
        ProfileMenu,
        BookingModal
    },
    data() {
        return {
                showModal: false,
                token: null,    
                bookings: [],
                stations: []
            };
        },
    mounted: function(){
        this.token = localStorage.data

        axios.get("http://10.101.52.46:8080/databind/api/stations").then(response => {
            this.stations = response.data
        }).catch(e => {
            console.log(e);
        });

        axios.get("http://10.101.52.46:8080/databind/api/users/tickets", {
            headers:{
                "Authorization": this.token
            }
        }).then(response => {
            this.bookings = response.data
        }).catch(e => {
            console.log(e);
        });
    },
    methods: {
        openModal(data) {
            this.showModal = true
            this.temp = data
        },
    },
    computed: {
       
    }      
}
</script>
<style lang="scss" scoped>
    .container{
        margin: auto;
        margin-top: 138px;
        max-width: 88% !important;
        background: #fff;
        border: 1px solid rgba(0,0,0,0.3);
        padding: 16px;
        border-radius: 8px;
    }
    .menu-data{
        table{
            .bookingId{
                cursor:pointer;
            }
            .bookingId:hover{
                background-color: #00ff99;
            }
            width: 100%;
            border-collapse: collapse;
            border-radius: 6px;
            text-align: left;
            overflow: hidden;
            td, th{
                border-top: 1px solid #ECF0F1;
                padding: 10px;
            }
            
            td{
                border-left: 1px solid #ECF0F1;
                border-right: 1px solid #ECF0F1;
            }
            
            th{
                background-color: #00ff99;
            } 
            tr:nth-of-type(even){
                background-color: lighten(#00ff99, 35%);
            }     
        }
        .mobile-header{
            display: none;
        }

        @media only screen and (max-width: 760px){
            p{
                display: block;
                font-weight: bold;
            }
            
            table{
                tr{
                    td:not(:first-child), th:not(:first-child), td:not(.total-val){
                        display: none;
                    }
                    
                    &:nth-of-type(even) td:first-child{
                        background-color: lighten(#4ECDC4, 35%);
                    }
                    &:nth-of-type(odd) td:first-child{
                        background-color: white;
                    }
                    
                    &:nth-of-type(even) td:not(:first-child){
                        background-color: white;
                    }
                    
                    th:first-child{
                        width: 100%;
                        display:block;
                    }
                    
                    th:not(:first-child){
                        width: 40%;
                        transition: transform 0.4s ease-out;
                        transform:translateY(-9999px);
                        position: relative;
                        z-index: -1;
                    }
                    
                    td:not(:first-child){
                        transition: transform 0.4s ease-out;
                        transform:translateY(-9999px);
                        width: 60%;
                        position: relative;
                        z-index: -1;
                    }
                    
                    td:first-child{
                        display: block;
                        cursor: pointer;
                    }
                    
                    &.total th{
                        width: 25%;
                        display: inline-block;
                    }
                    
                    td.total-val{
                        display: inline-block;
                        transform: translateY(0);
                        width: 75%;
                    }
                }
            }
        }

        @media only screen and (max-width: 300px){
            table{
                tr{
                    th:not(:first-child){
                        width: 50%;
                        font-size: 14px;
                    }
                    
                    td:not(:first-child){
                        width: 50%;
                        font-size: 14px;
                    }
                }
            }
        }
    }
</style>


