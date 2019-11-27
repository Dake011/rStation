<template>
  <div class="home">
    <!-- Search Form Area -->
    <section class="dorne-welcome-area bg-img bg-overlay">
      <div class="container h-100">
        <div class="row h-100 align-items-center justify-content-center">
          <div class="col-12 col-md-10">
            <div id="booking" class="section">
              <div class="section-center">
                <div class="container">
                  <div class="row">
                    <div class="booking-form">
                      <div class="form-header">
                        <h1>Make your reservation</h1>
                      </div>
                      <form>
                        <div class="form-group">
                          <div class="row">
                            <div class="form-group col-4">
                              <label class="form-group">
                                <p class="enter-names enter-names-to">From:</p>
                                <select
                                  @change="selectFrom($event)"
                                  class="form-control"
                                  name="movies"
                                >
                                  <option value="Station From" disabled selected>Station From</option>
                                  <option
                                    v-for="city in kzCities"
                                    :key="city.StationID"
                                    :selected="stationFrom === city.name"
                                  >{{ city.name }}</option>
                                </select>
                              </label>
                            </div>
                            <div class="form-group col-1">
                              <div class="swap-icon" @click="swapStations()">
                                <i class="fas fa-exchange-alt"></i>
                              </div>
                            </div>
                            <div class="form-group col-4">
                              <label class="form-group">
                                <p class="enter-names enter-names-to">To:</p>
                                <select
                                  @change="selectTo($event)"
                                  class="form-control"
                                  name="movies"
                                >
                                  <option value="Station From" disabled selected>Station To</option>
                                  <option
                                    v-for="city in kzCities"
                                    :key="city.StationID"
                                    :selected="stationTo === city.name"
                                  >{{ city.name }}</option>
                                </select>
                              </label>
                            </div>
                            <div class="form-group form-group-date col-4">
                              <label class="form-group">
                                <p class="enter-names enter-names-to">Date:</p>
                                <input
                                  :value="Datee"
                                  @input="updateValue($event.target.value)"
                                  id="dateTime"
                                  class="form-control"
                                  type="date"
                                  required
                                />
                              </label>
                            </div>
                          </div>
                        </div>
                        <div class="form-btn">
                          <button
                            type="button"
                            @click="showSchedules()"
                            class="btn btn-danger"
                          >Search</button>
                        </div>
                      </form>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <div v-if="travelInstance">
      <TrainInfo :info="info" :depTime="depTime" :arrTime="arrTime" :schedules="schedules" ref="showAllSchedule"></TrainInfo>
    </div>
    <div class="kz-map">
      <MainMap :kzCities="kzCities"></MainMap>
    </div>
    <div v-if="showSchedule">
      <ScheduleTable
        @data="UpdateInfo"
        :schedules="schedules"
        :stationTo="stationTo"
        :stationFrom="stationFrom"
        ref="showTravelInstance"
      ></ScheduleTable>
    </div>
    <!-- <div v-if="!allSchedule">
      <ScheduleTable :schedules="passSchedule" ref="showTravelInstance"></ScheduleTable>
    </div>-->
  </div>
</template>
<script>
import MainMap from "../components/MainMap.vue";
import ScheduleTable from "../components/ScheduleTable.vue";
import TrainInfo from "../components/TrainInfo.vue";
import json from "../assets/kz.json";
import axios from "axios";

export default {
  components: {
    MainMap,
    ScheduleTable,
    TrainInfo
  },
  data() {
    return {
      kzCities: json,
      stationFrom: "",
      stationTo: "",
      Datee: null,
      showSchedule: false,
      allSchedule: true,
      travelInstance: false,
      passSchedule: [],
      schedules: [],
      info: {},
      clicked: 0,
      depTime: null,
      arrTime: null
    };
  },
  mounted() {
    if (localStorage.stationFrom && localStorage.stationTo) {
      this.stationFrom = localStorage.stationFrom;
      this.stationTo = localStorage.stationTo;
    }
    axios
      .get("http://10.101.52.46:8080/databind/api/stations")
      .then(response => {
        this.kzCities = response.data;

        // console.log("success");
      })
      .catch(e => {
        // console.log(e);
      });
  },
  methods: {
    UpdateInfo(a) {
      this.info = a;

      // console.log(this.info);
    },
    selectFrom(event) {
      this.stationFrom = event.target.value;
      localStorage.stationFrom = event.target.value;
    },
    selectTo(event) {
      this.stationTo = event.target.value;
      localStorage.stationTo = event.target.value;
    },
    swapStations() {
      if (this.stationFrom && this.stationFrom) {
        let temp = this.stationFrom;
        localStorage.stationFrom = this.stationTo;
        localStorage.stationTo = temp;
        this.stationFrom = this.stationTo;
        this.stationTo = temp;
      }
    },
    updateValue(val) {
      this.Datee = val;
    },
    showSchedules() {
      // console.log(this.stationFrom);
      //location.reload();
      if (this.stationFrom && this.stationTo && this.Datee) {
        this.showSchedule = true;

        axios
          .get(
            "http://10.101.52.46:8080/databind/api/schedules?from=" +
              this.stationFrom +
              "&to=" +
              this.stationTo +
              "&date=" +
              this.Datee
          )
          .then(response => {
            // console.log(response.data[0]);
            this.schedules = response.data;
            this.showSchedule = true;
            var output = this.schedules[0].ArrivalTime.split(" ");
            this.schedules[0].ArrivalTime = output[1];
            this.schedules[0].ArrivalDate = output[0];
            this.depTime = this.schedules[0].DepartureTime;
            this.arrTime = this.schedules[0].ArrivalTime;
            var output1 = this.schedules[0].DepartureTime.split(" ");
            this.schedules[0].DepartureTime = output1[1];
            this.schedules[0].DepartureDate = output1[0];

            console.log(this.schedules);
            this.clicked = 0;
            // console.log(response.data);
          })
          .catch(e => {
            console.log(e);
          });
      } else {
        alert("Fill in all inputs!");
      }
    },
    showTravelInstance(selectSchedule) {
      this.travelInstance = true;
      this.passSchedule.push({ selectSchedule });
      if (this.passSchedule.length > 1) this.passSchedule.shift();
      this.allSchedule = false;
    },
    showAllSchedule() {
      this.travelInstance = false;
      this.allSchedule = true;
      this.passSchedule.pop();
    }
  },
  computed: {}
};
</script>

<style lang="scss" scoped>
.pick-station {
  z-index: 20;
  position: absolute;
  overflow: hidden;
  background: #fff;
  border: rgba(0, 0, 0, 0.8);
  border-radius: 0.25rem;
  width: 100%;
}
.home {
  width: 100%;
  height: 945px;
  position: relative;
  z-index: 1;
}
.col-4 {
  flex: 0 0 30%;
  max-width: 30%;
}
.bg-overlay:after,
.bg-overlay-9:after {
  content: "";
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  z-index: 10;
}

.booking-form {
  position: relative;
  max-width: 800px;
  width: 100%;
  margin: auto;
  margin-top: 138px;
  padding: 30px;
  overflow: hidden;
  background: #fff;
  // background-image: url('../assets/background.jpg');
  background-size: cover;
  border-radius: 5px;
  z-index: 20;
  opacity: 0.7; // Change opacity value to see MAP clear and add :hover below
  max-height: 300px;
}
.booking-form:hover {
  opacity: 0.9;
}
.booking-form::before {
  content: "";
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  top: 0;
  background-color: rgba(68, 71, 92, 1);
  z-index: -1;
}

.booking-form .form-header {
  text-align: center;
  position: relative;
}

.booking-form .form-header h1 {
  font-weight: 700;
  text-transform: capitalize;
  font-size: 42px;
  margin: 0px;
  color: #fff;
}

.booking-form .form-group {
  position: relative;
  margin: 0;
  width: 100%;
  p {
    color: #cc9966;
    font-size: 18px;
    font-weight: 500;
  }
  .swap-icon {
    margin-top: 44px;
    .fas {
      padding-top: 8px;
      padding-left: 8px;
      width: 40px;
      height: 40px;
      font-size: 24px;
      color: #fff;
      background: red;
      border-radius: 4px;
      cursor: pointer;
    }
    .fas:hover {
      background: #fff;
      color: red;
    }
  }
}

.booking-form .form-control {
  height: 40px;
  padding: 0px 16px;
  border: none;
  color: #000;
  -webkit-box-shadow: 0px 0px 0px 2px transparent;
  box-shadow: 0px 0px 0px 2px transparent;
  -webkit-transition: 0.2s;
  transition: 0.2s;
}

.booking-form .form-control::-webkit-input-placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.booking-form .form-control:-ms-input-placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.booking-form .form-control::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.booking-form .form-control:focus {
  -webkit-box-shadow: 0px 0px 0px 2px #ff8846;
  box-shadow: 0px 0px 0px 2px #ff8846;
}

.booking-form input[type="date"].form-control + .form-label {
  opacity: 1;
  top: 10px;
}

.booking-form select.form-control {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}

.booking-form select.form-control:invalid {
  color: rgba(255, 255, 255, 0.5);
}

.booking-form select.form-control + .select-arrow {
  position: absolute;
  right: 15px;
  top: 50%;
  -webkit-transform: translateY(-50%);
  transform: translateY(-50%);
  width: 32px;
  line-height: 32px;
  height: 32px;
  text-align: center;
  pointer-events: none;
  color: rgba(255, 255, 255, 0.5);
  font-size: 14px;
}

.booking-form select.form-control + .select-arrow:after {
  content: "\279C";
  display: block;
  -webkit-transform: rotate(90deg);
  transform: rotate(90deg);
}

.booking-form select.form-control option {
  color: #000;
}

.booking-form .form-label {
  position: absolute;
  top: -10px;
  left: 25px;
  opacity: 0;
  color: #ff8846;
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.3px;
  height: 15px;
  line-height: 15px;
  -webkit-transition: 0.2s all;
  transition: 0.2s all;
}

.booking-form .form-group.input-not-empty .form-control {
  padding-top: 16px;
}

.booking-form .form-group.input-not-empty .form-label {
  opacity: 1;
  top: 10px;
}

.booking-form .form-btn {
  text-align: right;
  button {
    color: #fff;
  }
}

.booking-form .submit-btn:hover,
.booking-form .submit-btn:focus {
  opacity: 0.9;
}
</style>
