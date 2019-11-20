<template>
    <div class="gd-booking js_container_booking_info" data-seats_position="жоғарғы,төмеңгі,орташа" style="display: block;">
      <!-- car select -->
      <div class="content__block content__block--no-margin">
         <div class="content__lead row">
           <div class="col-md-9">Please pick a carriage and seat(s)</div> 
           <div class="col-md-3 go-back-train" v-on:click="$parent.showAllSchedule()"><i class="fas fa-caret-left"></i> Go back to all Options</div>
         </div>
         <div class="js-train js_toggle second" data-car-class="second" style="display: block;">
            <div class="train train--kz js-car_select_item car_select_item second active" data-car-class="second">
               <div class="col-4 col-l-12">
                  Kupe - <strong>
                  Talgo                </strong>
                  <div class="train__price price hidden KZT" rel="0.00" style="display: block;">
                        {{ price }} KZT
                  </div>
               </div>
               <div class="col-8 col-l-12 car_select_radio_btn second">
                  <div class="train__item train__item--first"></div>
                  <div class="train__item-wrapper js-car_select_one_btn" v-for="vagon in vagons.slice(1)" :key="vagon.id">
                     <input type="radio" data-action="gd-car_select_radio" name="car_select_radio_btn_c7520bbc92fe3ab3330098c6d96849f7" data-best-offer="false" class="form-radio" data-car-details="{&quot;conditioner&quot;:&quot;&quot;}" data-car-number="1" data-user-fields="first_name,last_name,document_type,document_number">
                     <label for="car2" :id="'vagon' + vagon.id" class="train__item" v-on:click="selectVagon(vagon.id)">
                     <span class="train__item-num">{{ vagon.id }}</span>
                     <span class="train__item-text">available</span>
                     <span class="train__item-seats">{{ vagon.seats.length }}</span>
                     </label>
                  </div>
               </div>
            </div>
         </div>
      </div>
      <div class="js-car_container">
         <form data-lang="kk" data-block-multy-request="true" novalidate="novalidate" v-if="selectedVagonID != 0">
            <div class="content__block js-train-info js_seat_select" data-uil="car_schema" style="display: block;">
               <div class="train-info-text row">
                  <div class="col-4 col-l-12">
                     <div class="info-text info-text--arrow">Select seat(s) in a carriage</div>
                  </div>
                  <div class="col-8 col-l-12">
                     <strong>Seat mark info:</strong>
                     <ul class="train-info__list">
                        <li><span class="train-info__item train-info__item--available">Available</span></li>
                        <li><span class="train-info__item train-info__item--selected">Selected</span></li>
                        <li><span class="train-info__item train-info__item--unavailable">Unavailable</span></li>
                        <li><span class="train-info__item train-info__item--entrance">Entrance</span></li>
                     </ul>
                  </div>
               </div>
               <!-- train scheme -->
               <div class="custom-tooltip js-cheme-description">
                  <div class="train-scheme-holder js-insert insert wagon-scheme">
                     <div class="car-seats">
                        <div class="car-seats__tabs js-tabs">
                           <div class="car-seats__tabs-content">
                              <div class="wagon-scheme">
                                 <div class="wagon ktg">
                                    <div class="wagon-block">
                                       <div class="wagon-block__front">
                                          <div class="car-num">{{ selectedVagonID }}</div>
                                          <div class="item-wc"><span>WC</span></div>
                                          <div class="item-guard"><span></span></div>
                                          <div class="item-empty bottom top-corner"></div>
                                          <div class="item-empty bottom"></div>
                                       </div>
                                       <div class="wagon-block__middle">
                                          <div class="passengers_top">
                                             <div class="item-kupe" v-for="index in vagons[selectedVagonID].kupeNumber" :key="index">
                                                <div class="item-table"></div>
                                                    <a v-on:click="selectSeat(index*4 - 2)" tooltip="Upper" v-bind:class="{'available_seat': vagons[selectedVagonID].seats.includes(index*4 - 2), 'unavailable_seat': !vagons[selectedVagonID].seats.includes(index*4 - 2), 'selected_seat': selectedSeats.includes('vagon' + selectedVagonID + '_seat' + (index*4 - 2))}" class="one-seat one_sit tl top" :id="'vagon' + selectedVagonID + '_seat' + (index*4 - 2)">{{ index*4 - 2 }}</a>
                                                    <a v-on:click="selectSeat(index*4 - 3)" tooltip="Lower" v-bind:class="{'available_seat': vagons[selectedVagonID].seats.includes(index*4 - 3), 'unavailable_seat': !vagons[selectedVagonID].seats.includes(index*4 - 3), 'selected_seat': selectedSeats.includes('vagon' + selectedVagonID + '_seat' + (index*4 - 3))}" class="one-seat one_sit bl bottom" :id="'vagon' + selectedVagonID + '_seat' + (index*4 - 3)">{{ index*4 - 3 }}</a>
                                                    <a v-on:click="selectSeat(index*4)" tooltip="Upper" v-bind:class="{'available_seat': vagons[selectedVagonID].seats.includes(index*4), 'unavailable_seat': !vagons[selectedVagonID].seats.includes(index*4 ), 'selected_seat': selectedSeats.includes('vagon' + selectedVagonID + '_seat' + (index*4))}" class="one-seat one_sit tr top" :id="'vagon' + selectedVagonID + '_seat' +  index*4">{{ index*4 }}</a>
                                                    <a v-on:click="selectSeat(index*4 - 1)" tooltip="Lower" v-bind:class="{'available_seat': vagons[selectedVagonID].seats.includes(index*4 - 1), 'unavailable_seat': !vagons[selectedVagonID].seats.includes(index*4 - 1), 'selected_seat': selectedSeats.includes('vagon' + selectedVagonID + '_seat' + (index*4 - 1))}" class="one-seat one_sit br bottom" :id="'vagon' + selectedVagonID + '_seat' + (index*4 - 1)">{{ index*4 - 1 }}</a>
                                             </div>
                                          </div>
                                          <!-- passengers_top -->
                                          <div class="passengers_bottom"></div>
                                       </div>
                                       <!-- wagon-block__middle -->
                                       <div class="wagon-block__back">
                                          <div class="item-wc item-wc-back"><span>WC</span></div>
                                          <div class="item-empty"></div>
                                          <div class="item-empty"></div>
                                       </div>
                                    </div>
                                    <!-- wagon-block -->
                                 </div>
                                 <!-- wagon -->
                              </div>
                           </div>
                        </div>
                     </div>
                  </div>
               </div>
                <div class="user-select-info" v-if="selectedSeats.length != 0">
                    You have selected {{ selectedSeats.length }} seat(s). Total cost is {{ selectedSeats.length*price }}.
                    <!-- <div v-for="index in selectedSeats" :key="index">
                        <span>Vagon: {{ index[5] }} and seat(s): {{index[11]}}.</span>
                    </div>
                    <span>The total price is {{ selectedSeats.length * 11989 }}.</span> -->
                </div>
               <div class="user-fields-block">
                    <div class="text-center passenger-info" v-if="selectedSeats.length != 0">
                        <h6>Passenger Info</h6>
                    </div>
                    <form novalidate="true" @submit.prevent="checkForm">
                        <div class="user-input-form" v-for="(item, index) in selectedSeats" :key="item">
                            <div class="form-row top-form">
                                <div class="col-md-4">
                                    <p>Passenger {{ index + 1 }}:</p>
                                </div>
                                <div class="col-md-7"></div>
                                <div class="col-md-1">
                                    <span v-on:click="deleteSeat(item);" class="btn"><i class="fas fa-trash"></i></span>
                                </div>
                            </div>
                            <div class="form-row middle-form">
                                <div class="col-md-3">
                                        <mdb-input
                                        class="mdForm"
                                        size="sm"
                                        v-model="passengers[index].firstName"
                                        type="text"
                                        icon="user"
                                        label="First name"
                                        required
                                        invalidFeedback="Please provide a valid name."
                                        validFeedback="Look's good."
                                    />
                                </div>
                                <div class="col-md-3">
                                    <mdb-input
                                        class="mdForm"
                                        size="sm"
                                        v-model="passengers[index].lastName"
                                        label="Last name"
                                        icon="user"
                                        type="text"
                                        required
                                        invalidFeedback="Please provide a valid name."
                                        validFeedback="Look's good."
                                    />
                                </div>
                                <div class="col-md-3">
                                    <mdb-input
                                        class="mdForm"
                                        size="sm"
                                        v-model="passengers[index].email"
                                        label="email"
                                        icon="credit-card"
                                        type="text"
                                        invalidFeedback="Please provide a valid password."
                                        validFeedback="Look's good."
                                    />
                                </div>
                                <div class="col-md-3">
                                    <mdb-input
                                        class="mdForm"
                                        size="sm"
                                        v-model="passengers[index].passportNum"
                                        label="№ passport"
                                        icon="passport"
                                        type="email"
                                        required
                                        invalidFeedback="Please provide a valid mail."
                                        validFeedback="Look's good."
                                    />
                                </div>
                            </div>
                        </div>
                        <div class="form-group bottom-form" v-if="selectedSeats.length != 0">
                            <div class="custom-control custom-checkbox">
                                <input type="checkbox" class="custom-control-input" id="customControlValidation1" required>
                                <label class="custom-control-label" for="customControlValidation1">Agree to <a href="https://www.symantec.com/content/en/us/about/downloads/symantec-online-services-agreement-2016-11-en.pdf">terms and conditions</a></label>
                            </div>
                        </div>
                        <div class="text-center bottom-form-btn" v-if="selectedSeats.length != 0">
                            <mdb-btn gradient="blue" type="submit" rounded class="btn-block z-depth-1a">Proceed</mdb-btn>
                        </div>
                    </form>
               </div>
            </div>
         </form>
         </div>
      </div>
</template>
<script>
import { mdbInput, mdbBtn } from 'mdbvue';

export default {
    components: {
      mdbInput,
      mdbBtn
    },
    data() {
        return{
            selectedVagonID: 0,
            selectedSeats: [],
            passengers:[],
            price: 11989,
            vagons:[
                {
                    id: 0,
                    kupeNumber: 0,
                    seatNumber: 0,
                    seats: []
                },
                {
                    id: 1,
                    kupeNumber: 12,
                    seatNumber: 20,
                    seats: [1,2,3,6,7,8,12]
                },
                {
                    id: 2,
                    kupeNumber: 5,
                    seatNumber: 24,
                    seats: [1,3,4,6]
                },
                {
                    id: 3,
                    kupeNumber: 6,
                    seatNumber: 24,
                    seats: [1,3,4,6]
                },
                {
                    id: 4,
                    kupeNumber: 3,
                    seatNumber: 24,
                    seats: [1,3,4,6]
                }
            ]
        }
    },
    methods:{
        selectVagon(vagonID){
            if(this.selectedVagonID) document.getElementById('vagon' + this.selectedVagonID).style.backgroundColor = "#ffffff";
            this.selectedVagonID = vagonID;
            document.getElementById('vagon' + vagonID).style.backgroundColor = "#a4a4db";
        },
        selectSeat(seatID){
            if(!document.getElementById('vagon' + this.selectedVagonID + '_seat' + seatID).classList.contains('unavailable_seat')){
                if(this.selectedSeats.includes('vagon' + this.selectedVagonID + '_seat' + seatID)){
                    this.selectedSeats.splice(this.selectedSeats.indexOf('vagon' + this.selectedVagonID + '_seat' + seatID), 1);
                } else {
                    if(this.selectedSeats.length < 5){
                        this.passengers.push({
                            firstName: '',
                            lastName: '',
                            email: '',
                            passportNum: '',
                        })
                        this.selectedSeats.push('vagon' + this.selectedVagonID + '_seat' + seatID);
                    } else {
                        alert("You can't pick more that 5 seats.");
                    }
                }   
            }
        },
        deleteSeat(seat){
            this.selectedSeats.splice(this.selectedSeats.indexOf(seat), 1);
        }
    }
}
</script>
<style scoped lang="scss">
/********** User ticket form **********/
.passenger-info{
    background: #3a3a92;
    color: #fff;
    padding: 6px 0;
    h6{
        margin-bottom: 0;
    }
}
.user-select-info{
    margin-left: 20px;
}
.user-input-form{
    padding: 0 20px;
    border-bottom: 2px solid rgba(58, 58, 146,0.7);
    .top-form{
        height: 48px;
        p{
            padding: 8px 18px;
            margin: 0;
            height: 42px;
        }
        .btn{
            padding: 6px 18px;
            background: #ba1433;
            color: #fff;
        }
    }
    .middle-form{
        margin: auto 0;
        .col-md-3{
            max-height: 48px;
            .mdForm{
                margin: 0 auto;
                color: #b0b5cc;
            }
        }
    }
}
.bottom-form{
    margin: 8px 20px;
    a{
        color: #29a3ff;
    }
    a:hover{
        text-decoration: underline;
    }
}
.bottom-form-btn{
    margin: 0 24px 24px auto;
    width: 120px;
    button{
        padding: 6px 12px;
        background: linear-gradient(40deg, #377cc3, #3a3a92) !important;
    }
}
/********** Seat Tooltip **********/

[tooltip]:before {
    /* needed - do not touch */
    z-index: 30;
    content: attr(tooltip);
    position: absolute;
    opacity: 0;
    /* customizable */
    transition: all 0.15s ease;
    padding: 0;
    width: 50px;
    color: #333;
    border-radius: 20%;
}

[tooltip]:hover:before {
    /* needed - do not touch */
    opacity: 1;
    /* customizable */
    background: yellow;
    margin-top: 0;
    margin-left: 12px;    
}
[tooltip]:not([tooltip-persistent]):before {
    pointer-events: none;
}
.gd-booking{
    position: absolute;
    left: 0; 
    right: 0; 
    margin: 15% auto auto auto;
    width: 70%;
    z-index: 20;
}
.content__block--no-margin {
  margin: 0;
}
.content__block {
  position: relative;
  margin-bottom: 10px;
  padding: 0 10px;
  background-color: #ffffff;
  clear: both;
  -webkit-box-shadow: 0 1px 1px rgba(0,0,0,0.15);
  -moz-box-shadow: 0 1px 1px rgba(0,0,0,0.15);
  box-shadow: 0 1px 1px rgba(0,0,0,0.15);
}
.content__block:after {
  content: "";
  clear: both;
  display: block;
  overflow: hidden;
  width: 100%;
}
.content__lead {
  font-size: 21px;
  font-weight: bold;
  padding: 20px;
  color: #4a4a4a;
  .go-back-train{
    background: rgba(68, 71, 92, 0.5);
    border-radius: 4px;
    cursor: pointer;
    font-size: 18px;
    line-height: 1.8;
  }
  .go-back-train:hover{
    color: #000;
    text-decoration: underline;
  }
}
.row {
  margin-left: -10px;
  margin-right: -10px;
  clear: both;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.train {
  font-size: 14px;
  line-height: 1;
  margin-left: -10px;
  margin-right: -10px;
  padding: 25px 10px 15px;
  border-top: 1px solid #dbdbdb;
  background-color: #f8f8f8;
  clear: both;
}
.train__price {
  display: block;
  font-weight: bold;
  color: #b543a0;
  margin-top: 7px;
}
.train__item {
  display: block;
  position: relative;
  width: 75px;
  height: 40px;
  font-size: 10px;
  text-align: center;
  text-decoration: none;
  margin: 5px 0 5px 6px;
  padding: 6px 0 6px 25px;
  color: #414141;
  border: 1px solid #aebad2;
  background-color: #ffffff;
  -webkit-box-shadow: 0 1px 2px rgba(0,0,0,0.14);
  -moz-box-shadow: 0 1px 2px rgba(0,0,0,0.14);
  box-shadow: 0 1px 2px rgba(0,0,0,0.14);
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  -ms-border-radius: 4px;
  
  border-radius: 4px;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.train__item--first {
  width: 57px;
  margin-left: 0;
  background: none;
  pointer-events: none;
  border-top-left-radius: 50px;
  border-bottom-left-radius: 10px;
  background-color: #fdfdfd;
  overflow: hidden;
  -webkit-box-shadow: 0 0 0 rgba(0,0,0,0);
  -moz-box-shadow: 0 0 0 rgba(0,0,0,0);
  box-shadow: 0 0 0 rgba(0,0,0,0);
}

.train__item--first, .train__item-wrapper {
  display: inline-block;
  vertical-align: top;
}

.form-checkbox, .form-radio {
  position: absolute;
  display: inline-block;
  width: 13px;
  height: 13px;
  margin-right: -15px;
  -moz-opacity: 0;
  -khtml-opacity: 0;
  -webkit-opacity: 0;
  opacity: 0;
  filter: alpha(opacity=0);
}
.train__item:not(.train__item--first) {
  cursor: pointer;
}
.train label {
  cursor: pointer;
}
.train__item-num {
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  width: 25px;
  height: 25px;
  font-size: 12px;
  padding: 6px 2px;
  color: #ffffff;
  background-color: #aebad2;
  border-top-right-radius: 9px;
  border-bottom-right-radius: 9px;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

.train__item:not(.train__item--first) {
  cursor: pointer;
}
.train__item-text {
  color: #393939;
  -moz-opacity: .5;
  -khtml-opacity: .5;
  -webkit-opacity: .5;
  opacity: .5;
  filter: alpha(opacity=50);
}

.train__item:not(.train__item--first) {
  cursor: pointer;
}.train__item-seats {
    display: block;
    font-size: 14px;
    font-weight: bold;
    color: #393939;
}
  
.train__item--first:after {
  content: '';
  width: 8px;
  height: 6px;
  position: absolute;
  left: -3px;
  bottom: 9px;
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  -ms-border-radius: 3px;
  -o-border-radius: 3px;
  border-radius: 3px;
}
.train__item--first:before, .train__item--first:after {
  border: 1px solid #aebad2;
  background-color: #f7f7f7;
}

.train__item--first:before, .train__item--first:after {
  border: 1px solid #aebad2;
  background-color: #f7f7f7;
}
.train__item--first:before {
  width: 35px;
  height: 20px;
  bottom: 20px;
  border-bottom-right-radius: 10px;
}
.train__item:before {
  content: '';
  position: absolute;
  display: inline-block;
  height: 17px;
  width: 4px;
  border-left: 1px solid #aebad2;
  border-right: 1px solid #aebad2;
  top: 0;
  bottom: 0;
  left: -6px;
  margin: auto;
}
.train-info-text {
  font-size: 13px;
  color: #393939;
  padding: 15px 10px;
  border-top: 1px solid #efefef;
  clear: both;
}
.train-info-train {
  text-transform: lowercase;
}
.train-info-text {
  font-size: 13px;
  color: #393939;
  padding: 15px 10px;
  border-top: 1px solid #efefef;
  clear: both;
}
.train-info-text .info-text{
  font-size: 13px;
}
.info-text--arrow {
  font-size: 13px;
  font-weight: normal;
  font-style: italic;
  font-stretch: normal;
  text-align: center;
  color: #555d6f;
  padding: 0;
  background: none;
}
.info-text {
  display: inline-block;
  padding: 7px 15px;
  font-size: 13px;
  line-height: 15px;
  font-weight: normal;
  font-style: normal;
  text-align: center;
  color: #488947;
  background-color: #eef9ed;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.train-info__list {
  display: inline-block;
  list-style: none;
  padding: 0;
}
.train-info__list li {
  display: inline-block;
  padding: 0 5px 0 0;
}
.train-info__item {
  display: block;
  line-height: 20px;
}
.train-info__item--available:before, .train-info__item--selected:before, .train-info__item--unavailable:before, .train-info__item--entrance:before {
  content: '';
  display: inline-block;
  width: 16px;
  height: 16px;
  margin: 0 7px;
  background-color: #e9eaed;
  vertical-align: text-top;
  -webkit-border-radius: 2px;
  -moz-border-radius: 2px;
  -ms-border-radius: 2px;
  -o-border-radius: 2px;
  border-radius: 2px;
}
.train-info__item--available:before {
  background-color: #3a3a92;
}
.train-info__item--selected:before {
  background-color: #b543a0;
}
.train-info__item--unavailable:before {
  background-color: #aebad2;
}
.train-info__item--entrance:before {
  background-color: #000;
  border-radius: 50%;
}
.wagon-scheme {
  overflow: auto;
  clear: both;
}

.wagon-scheme {
  background-color: #ffffff;
  width: auto;
}
.wagon {
  display: block;
  width: 100%;
  overflow: auto;
  font-family: Arial,"Helvetica Neue",Helvetica,sans-serif;
  clear: both;
  // text-align: center;
}

.wagon-block {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  -o-border-radius: 10px;
  border-radius: 10px;
  -webkit-box-shadow: 0 4px 8px rgba(0,0,0,0.3);
  -moz-box-shadow: 0 4px 8px rgba(0,0,0,0.3);
  box-shadow: 0 4px 8px rgba(0,0,0,0.3);
  position: relative;
  display: inline-block;
  min-height: 110px;
  margin: 15px -800px 15px 32px;
  background: #ffffff;
  border: 3px solid #aebad2;
  overflow-x: auto;
  white-space: nowrap;
}
.ktg * {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.ktg .wagon-block__front {
  width: 190px;
}

.wagon-block__front, .wagon-block__back, .wagon-block__middle {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  -o-border-radius: 10px;
  border-radius: 10px;
  height: 104px;
  min-width: 52px;
  background: url("https://static.tickets.kz/img/gd/pattern.png?84863");
  background-repeat: repeat;
}
.wagon-block__front, .wagon-block__back, .wagon-block__middle {
  float: left;
  position: relative;
  min-height: 1px;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.wagon .car-num {
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  -ms-border-radius: 3px;
  -o-border-radius: 3px;
  border-radius: 3px;
  display: block;
  width: 30px;
  height: 24px;
  line-height: 25px;
  font-size: 16px;
  color: #ffffff;
  font-weight: bold;
  text-align: center;
  position: absolute;
  left: 8px;
  top: 0;
  bottom: 0;
  margin: auto;
  background-color: #b543a0;
  z-index: 1;
}

.ktg .wagon-block__front .item-wc{
  right: 64px;
  border-right: 0;
  padding-bottom: 5px;
} 
.ktg .wagon-block__front:before {
  top: -13px;
}
.ktg .wagon-block__front:before, .ktg .wagon-block__front:after {
  display: block;
  position: absolute;
  left: 20px;
  content: '';
  width: 10px;
  height: 10px;
  background: url("https://image.flaticon.com/icons/png/512/60/60995.png") no-repeat center;
  border-radius: 5px;
  z-index: 1;
}
.item-wc {
  display: table;
  position: absolute;
  width: 52px;
  height: 40px;
  color: #aebad2;
  font-size: 14px;
  line-height: 16px;
  text-align: center;
  border: 1px solid #aebad2;
  border-top: none;
  background: #ffffff;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.item-wc-back{
  border-top-right-radius: 10px;
  border:none;
  border-bottom: 1px solid #aebad2;
}
.item-guard {
  position: absolute;
  display: block;
  width: 64px;
  height: 60px;
  right: 0;
  background: #ffffff;
  border-left: 1px solid #aebad2;
  border-bottom: 1px solid #aebad2;
  border-right: 0;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
.item-guard:before {
  content: '';
  position: absolute;
  display: block;
  width: 15px;
  height: 21px;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  margin: auto;
  background: url("https://static.tickets.kz/img/gd/icon-guard.png?84863") 0 0 no-repeat;
}


.ktg .item-kupe:first-of-type {
  border-left-width: 3px;
}

.ktg .item-kupe {
  position: relative;
  float: left;
  width: 106px;
  height: 76px;
  border: 2px solid #ced5e2;
  margin-right: 0;
  border-top-width: 0;
  border-right-width: 3px;
  border-left-width: 0;
  background: #fff;
}
.ktg .item-kupe:after {
  width: 40px;
}
.item-sv:after, .item-kupe:after {
  content: '';
  position: absolute;
  top: 100%;
  display: block;
  width: 10px;
  height: 2px;
  left: 0;
  right: 0;
  margin: auto;
  background: #ffffff;
  z-index: 1;
}
.ktg .item-table {
  margin-top: 4px;
}

.item-table {
  display: block;
  width: 22px;
  height: 32px;
  margin: auto;
  border: 1px solid #aebad2;
  background-color: #d6dce8;
  border-top-left-radius: 1px;
  border-top-right-radius: 1px;
  border-bottom-left-radius: 7px;
  border-bottom-right-radius: 7px;
}
.ktg .item-kupe .tl{
  top: 4px;
  border-top-left-radius: 0;
  border-top-right-radius: 3px;
  border-bottom-right-radius: 0;
}
.ktg .item-kupe .tr{
  margin-right: 2px;
  top: 4px;
  border-top-left-radius: 3px;
  border-top-right-radius: 0;
}

.wagon .bl, .wagon .tl {
  left: 2px;
  border-bottom-right-radius: 3px;
}
.wagon .one-seat, .wagon .one_sit {
  position: absolute;
  display: block;
  width: 25px;
  height: 33px;
  line-height: 34px;
  text-align: center;
  color: #ffffff !important;
  font-size: 12px;
  text-decoration: none;
}
.available_seat{
    background: #3a3a92;
}
.available_seat:hover{
    background: #0f0f24; 
}
.unavailable_seat {
    background: #aebad2;
    cursor: default;
}
.selected_seat {
    background: #b543a0;
}


.ktg .item-kupe .tr, .ktg .item-kupe .br {
  right: 0;
}
.wagon .br {
  margin-right: 2px;
  bottom: 5px;
}
.br {
  bottom: 1px;
  right: 0;
  -webkit-border-radius: 0 0 0 3px;
  -moz-border-radius: 0 0 0 3px;
  border-radius: 0 0 0 3px;
}
.ktg .passengers_bottom {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 17px;
  border-left: 3px solid #ced5e2;
  border-right: 3px solid #ced5e2;
}
</style>


