<template>
  <div>
    <table id="fifthTable">
      <thead>
        <tr>
          <th>Train Name</th>
          <th>From</th>
          <th style="text-align:center">Time in way</th>
          <th style="text-align:right">To</th>
          <th>Price</th>
          <th>Number of places</th>
        </tr>
      </thead>
      <tbody>
        <tr class="tb1" v-bind:key="schedule.TravelInstanceID" v-for="schedule in schedules">
          <td>
            <div class="trainInfo">
              <div class="trainNum">{{ schedule.TrainName }}</div>
              <div class="trainDir">Train type: {{schedule.TrainType}}</div>
              <div>
                <a href="#"></a>
              </div>
            </div>
          </td>
          <td>
            <div class="Departure">
              <div :id="schedule.TravelInstanceID + 'dep'" class="time">{{schedule.DepartureTime}}</div>
              <div class="date">{{schedule.DepartureDate }}</div>
              <div class="clientTo">{{ stationFrom }}</div>
              <div class="text">(Local Time)</div>
            </div>
          </td>
          <td>
            <!-- <div v-if="schedules" class="tm">{{ timeInWay(schedule) }}</div> -->
            <div v-if="schedules" class="tm">23:23</div>

            <div class="line"></div>
          </td>
          <td>
            <div class="Departure" style="text-align:right">
              <div :id="schedule.TravelInstanceID + 'arriv'" class="time">{{schedule.ArrivalTime}}</div>
              <div class="date">{{ schedule.ArrivalDate}}</div>
              <div class="clientTo">{{ stationTo }}</div>
              <div class="text">(Local Time)</div>
            </div>
          </td>
          <td>
            <div class="type">
              <div>from:</div>
              <div style="font-weight:bold">11989 KZT</div>
            </div>
          </td>
          <td>
            <div>
              place:32
              <button
                class="button"
                v-on:click="$parent.showTravelInstance(schedule);TravelInstance(schedule.TravelInstanceID)"
              >Choose</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "FormsPage",
  props: ["schedules", "stationTo", "stationFrom"],
  data() {
    return {
      selected: [],
      NumVagon: [1, 2, 3, 4],
      firstName: null,
      lastName: null,
      agentName: null,
      email: null,
      pass: null,
      newPass: null,
      num: 1
    };
  },
  mounted() {
    this.timeInWay();
  },
  watch: {},
  methods: {
    TravelInstance(id) {
      console.log(id);
      axios
        .get("http://10.101.52.46:8080/databind/api/trains/" + id)
        .then(response => {
          // this.schedules = response.data;
          // this.showSchedule = true;
          var a = response.data;
          for(let i = 0; i < a.vagons.length;i++){
            if (a.vagons[i].Num_Of_Seats % 4 === 0){
              a.vagons[i].KupeNumber = a.vagons[i].Num_Of_Seats / 4;
            } else {
              a.vagons[i].KupeNumber = parseInt(a.vagons[i].Num_Of_Seats / 4) + 1;
            }
          }
          this.$emit("data", a);
        })
        .catch(e => {
          console.log(e);
        });
    },
    timeInWay(schedule) {
      // console.log(schedule.TravelInstanceID + "dep");
      // var depTime = document.getElementById(schedule.TravelInstanceID + "dep");
      // console.log(depTime);
      // var arrivTime = document.getElementById(
      //   schedule.TravelInstanceID + "arriv"
      // ).textContent;
      // arrivTime = arrivTime.split(":");
      // depTime = depTime.split(":");
      // console.log(depTime, arrivTime);
      // var difHour = arrivTime[0] - depTime[0] + 24;
      // var difMin = arrivTime[1] - depTime[1] + 24;
      // return difHour + ":" + difMin;
    }
  },
  computed: {}
};
</script>
<style lang="scss" scoped>
#fifthTable {
  font-family: "Open Sans", sans-serif;
  border: 3px solid #44475c;
  margin: 20px auto auto auto;
  background: #fff;
  position: relative;
  z-index: 20;
  opacity: 0.7; // Change opacity value to see MAP clear and add :hover below
  width: 70%;
}
#fifthTable:hover{
  opacity: 0.9;
}
table th {
  text-transform: uppercase;
  text-align: left;
  color: #fff;
  padding: 8px;
  min-width: 30px;
  background: #44475c;
}
table td {
  text-align: left;
  padding: 8px;
  border-collapse: collapse;
  background-color: rgba(68, 71, 92, 0.5);
  color: rgba(14, 2, 35, 0.9);
}
table td:last-child {
  border-right: none;
}
table tbody tr:nth-child(2n) td {
  background: #d4d8f9;
}
.arrow_down {
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB8AAAAaCAYAAABPY4eKAAAAAXNSR0IArs4c6QAAAvlJREFUSA29Vk1PGlEUHQaiiewslpUJiyYs2yb9AyRuJGm7c0VJoFXSX9A0sSZN04ULF12YEBQDhMCuSZOm1FhTiLY2Rky0QPlQBLRUsICoIN/0PCsGyox26NC3eTNn3r3n3TvnvvsE1PkwGo3yUqkkEQqFgw2Mz7lWqwng7ztN06mxsTEv8U0Aam5u7r5EInkplUol/f391wAJCc7nEAgE9Uwmkzo4OPiJMa1Wq6cFs7Ozt0H6RqlUDmJXfPIx+qrX69Ti4mIyHA5r6Wq1egND+j+IyW6QAUoul18XiUTDNHaSyGazKcZtdgk8wqhUKh9o/OMvsVgsfHJy0iWqVrcQNRUMBnd6enqc9MjISAmRP3e73T9al3XnbWNjIw2+KY1Gc3imsNHR0YV4PP5+d3e32h3K316TySQFoX2WyWR2glzIO5fLTSD6IElLNwbqnFpbWyO/96lCoai0cZjN5kfYQAYi5H34fL6cxWIZbya9iJyAhULBHAqFVlMpfsV/fHxMeb3er+Vy+VUzeduzwWC45XA4dlD/vEXvdDrj8DvURsYEWK3WF4FA4JQP9mg0WrHZbEYmnpa0NxYgPVObm5teiLABdTQT8a6vrwdRWhOcHMzMzCiXlpb2/yV6qDttMpkeshEzRk4Wo/bfoe4X9vb2amzGl+HoXNT29vZqsVi0sK1jJScG+Xx+HGkL4Tew2TPi5zUdQQt9otPpuBk3e0TaHmMDh1zS7/f780S0zX6Yni+NnBj09fUZUfvudDrNZN+GkQbl8Xi8RLRtHzsB9Hr9nfn5+SjSeWUCXC7XPq5kw53wsNogjZNohYXL2EljstvtrAL70/mVaW8Y4OidRO1/gwgbUMvcqGmcDc9aPvD1gnTeQ+0nmaInokRj0nHh+uvIiVOtVvt2a2vLv7Ky0tL3cRTXIcpPAwMDpq6R4/JXE4vFQ5FI5CN+QTaRSFCYc8vLy1l0rge4ARe5kJ/d27kYkLXoy2Jo4C7K8CZOsEBvb+9rlUp1xNXPL7v3IDwxvPD6AAAAAElFTkSuQmCC");
}
.arrow_up {
  background-image: url("data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAB4AAAAaCAYAAACgoey0AAAAAXNSR0IArs4c6QAAAwpJREFUSA21Vt1PUmEYP4dvkQ8JFMwtBRocWAkDbiqXrUWXzU1rrTt0bdVqXbb1tbW16C9IBUSmm27cODdneoXjputa6069qwuW6IIBIdLvdaF4OAcOiGeDc87zPs/vd57P96WpFq7p6enbGo1mjKZpeTabjU1MTCRagGnOZHFxcXxtbe1XKpUq7+zslJeXl//Mz8+Hy+Uy3RxSE9qTk5M3otFooVQqgef4Wl9f343FYoEmoISrxuNxFX5f9vb2jhn/PxUKhfLS0tIPfFifUESRUMV8Pv/M6XReRm5rTGQyGeXxeGxYe1ezeBpBOBx2rKysbO7v79d4Wy3Y2Nj4GQqFbgnhaugxwiuGJx99Pp9FLBbXxYTXvTqd7v3MzIy6riIWGxJnMpl7AwMD14xGYyMsSq1WUyQdUqn0eSPlusQIsbGrq+vl4OCgvhFQZd1utyv1en0gEolcqsi47nWJlUrlG5fLZVcoFFy2nDKSDpIWlUoVTCQSEk4lCHmJMZ2GTCbTiMVikfIZ88l7enoos9l8dXt7+z6fDicxSJUokqDX6xXcl2wCROoc0vQCWL3sNfLOSdzR0fHY4XC4tVotl40gmVwup9xuN4OQv+UyqCFGH9rg7SOGYVRcBs3IEG4J0nVnamrqOtvuBDGGgQg9+wHFcVEi4a0LNkbdd6TrPKo8ODc311mteIIYjT/a398/jK+s1jnVM0kXoufCFvq0GuiIGEVgQIhfoygM1QrteEa9dAL7ITiYCt4RMabOK5AyKKzKWtvupLcRciu8D5J0EuDDPyT/Snd39yh6VtY2NhYQSR9G79Ds7OxdskRjEyAufvb7/cPoO5Z6e1+xtVKrq6vfcFzyi/A3ZrPZ3GdNSlwgo5ekE4X2RIQGf2C1WlufFE0GBeGWYQ8YERWLxQtnUVB830MKLZfL9RHir8lkssCn2G751tZWEWe03zTKm15YWPiEiXXTYDB0Ig/t7yd8PRws4EicwWHxO4jHD8/C5HiTTqd1BwcHFozKU89origB+y/kmzgYpgOBQP4fGmUiZmJ+WNgAAAAASUVORK5CYII=");
}
.arrow {
  float: right;
  width: 12px;
  height: 15px;
  background-repeat: no-repeat;
  background-size: contain;
  background-position-y: bottom;
}
.trainInfo {
  display: flex;
  flex-direction: column;
  padding: 5px;
}
.type {
  display: flex;

  flex-direction: row;
}
.trainNum {
  font-weight: bold;
  font-size: large;
}
.time {
  font-size: large;
  font-weight: bold;
}
.Departure {
  padding-right: 1px;
  display: flex;
  flex-direction: column;
  padding: 5px;
}

.tm {
  text-align: center;
  font-size: medium;
}

.line {
  border-bottom: 2px solid #44475c;
  padding-bottom: 2px;
  position: relative;
}

.line:before,
.line:after {
  position: absolute;
  bottom: -6px;
  left: 0;
  height: 10px;
  width: 10px;
  background: #44475c;
  content: "";
  border-radius: 5px;
}

.line:after {
  right: 0;
  left: auto;
}
.button {
  background-color: #44475c; /* Green */
  border: none;
  color: white;
  padding: 4px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 13px;
  margin: 4px 2px;
  cursor: pointer;
}
.travelInstance {
  position: absolute !important;
  z-index: 999;
  width: 80% !important;
  padding: 0;
  transition: 0.3s ease all;
  margin-left: 10%;
}
.card {
  border: 2px solid #44475c;
  margin: 20px auto auto auto !important;
}
.form-elegant {
  margin: 20px auto auto auto !important;

  .font-small {
    font-size: 0.8rem;
  }
  .z-depth-1a {
    -webkit-box-shadow: 0 2px 5px 0 rgba(55, 161, 255, 0.26),
      0 4px 12px 0 rgba(121, 155, 254, 0.25);
    box-shadow: 0 2px 5px 0 rgba(55, 161, 255, 0.26),
      0 4px 12px 0 rgba(121, 155, 254, 0.25);
  }
  .z-depth-1-half,
  .btn:hover {
    -webkit-box-shadow: 0 5px 11px 0 rgba(85, 182, 255, 0.28),
      0 4px 15px 0 rgba(36, 133, 255, 0.15);
    box-shadow: 0 5px 11px 0 rgba(85, 182, 255, 0.28),
      0 4px 15px 0 rgba(36, 133, 255, 0.15);
  }
}
</style>
