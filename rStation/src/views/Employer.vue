<template>
  <div class="container bootstrap snippet">
    <div class="row menu-items">
      <ProfileMenu></ProfileMenu>
      <div class="menu-data row col-sm-9">
        <BookingModal :booking="temp" v-if="showModal" @close="showModal = false"></BookingModal>
        <table>
          <thead>
            <tr class="table-headers">
              <th>UUID</th>
              <th>First Name</th>
              <th>Surname</th>
              <th>Time slots</th>
              <th>Start time</th>
              <th>End time</th>
              <th>Station</th>
              <th>Salary</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr class="employer-row" v-for="employee in employees" :key="employee.EmployeeID">
              <td class="bookingId">{{employee.EmployeeID}}</td>
              <td>
                <input
                  :id="'firstname_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.firstname"
                />
              </td>
              <td>
                <input
                  :id="'surname_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.surname"
                />
              </td>
              <td>
                <input
                  style="max-width: 84px;"
                  :id="'workDays_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.Week_Days"
                />
              </td>
              <td>
                <input
                  class="intInput"
                  :id="'start_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.start_work"
                />
              </td>
              <td>
                <input
                  class="intInput"
                  :id="'end_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.end_work"
                />
              </td>
              <td>
                 <select :id="'station_' + employee.EmployeeID">
                    <option v-for="station in stations" :key="station.StationID" :selected="employee.Stations_StationID === station.StationID">{{station.name}}</option>
                </select>
              </td>
              <td>
                <input
                  class="intInput"
                  :id="'salary_' + employee.EmployeeID"
                  type="text"
                  v-model="employee.salary_per_hour"
                />
              </td>
              <td v-on:click="Delete(employee)" style="text-align:center;cursor:pointer">
                <i class="fas fa-trash"></i>
              </td>
            </tr>
            <tr>
              <td style="border:none;background: #fff;">
                <div style="text-align:left;position:relative">
                  <button class="button" v-if="showAddEmployer" v-on:click="AddEmployer()">Add New Employee</button>
                  <button class="button" v-else v-on:click="SaveEmployer()">Save New Employee</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!--/col-9-->
    </div>
    <div style="text-align:right;position:relative">
      <button class="button" v-on:click="SaveAll()">Save For Edit</button>
    </div>
  </div>
</template>
<script>
import ProfileMenu from "../components/ProfileMenu.vue";
import BookingModal from "../components/BookingModal.vue";
import { get, del } from "../helpers/api";
import axios from 'axios'

export default {
  components: {
    ProfileMenu,
    BookingModal
  },
  data() {
    return {
      showModal: false,
      saveUpdates: false,
      canAddEmployer: true,
      showAddEmployer: true,
      stations: [],
      employees: []
    };
  },
  mounted: function () {
    this.token = localStorage.data
    axios.get("http://10.101.52.46:8080/databind/api/stations").then(response => {
        this.stations = response.data
    }).catch(e => {
        console.log(e);
    });

    axios.get("http://10.101.52.46:8080/databind/api/managers/employees",{
      headers:{
          "Authorization": this.token
      }
    }).then(response => {
      this.employees = response.data
    }).catch(e => {
        console.log(e);
    });      
  },
  methods: {
    AddEmployer(){
      this.showAddEmployer = false
      if(this.canAddEmployer){
        this.canAddEmployer = false;
        this.employees.push({
          EmployeeID: 0,
          firstname: "",
          surname: "",
          Week_Days: "",
          start_work: "",
          end_work: "",
          Stations_StationID: 0,
          salary_per_hour: 0
        })
      }
    },
    SaveEmployer(){
      var firstname = document.getElementById("firstname_0").value;
      var surname = document.getElementById("surname_0").value;
      var workDays = document.getElementById("workDays_0").value;
      var start = document.getElementById("start_0").value;
      var end = document.getElementById("end_0").value;
      var station = document.getElementById("station_0").value;
      var salary = document.getElementById("salary_0").value;
      if(firstname && surname && workDays && start && end && station && salary){
          axios.post("http://10.101.52.46:8080/databind/api/managers/employees", {
              firstname: firstname,
              surname: surname,
              Week_Days: workDays,
              start_work: start,
              end_work: end,
              Stations_StationID: this.stations.find(x => x.name === station).StationID,
              salary_per_hour: salary
          }, {
              headers:{
                  "Authorization": this.token
              }
          }).then(response => {
              window.location.reload()
          }).catch(e => {
              alert(e.response.data);
          });
      } else {
          alert('Fill all the fields.')
      }
    },
    SaveAll() {
      var check = 0;
      var employees = this.employees;
      for(let i = 0; i < employees.length; i++){
        var firstname = document.getElementById("firstname_" + employees[i].EmployeeID).value;
        var surname = document.getElementById("surname_" + employees[i].EmployeeID).value;
        var workDays = document.getElementById("workDays_" + employees[i].EmployeeID).value;
        var start = document.getElementById("start_" + employees[i].EmployeeID).value;
        var end = document.getElementById("end_" + employees[i].EmployeeID).value;
        var station = document.getElementById("station_" + employees[i].EmployeeID).value;
        var salary = document.getElementById("salary_" + employees[i].EmployeeID).value;
        if(firstname && surname && workDays && start && end && station && salary){
            axios.put("http://10.101.52.46:8080/databind/api/managers/employees/" + employees[i].EmployeeID, {
                firstname: firstname,
                surname: surname,
                Week_Days: workDays,
                start_work: start,
                end_work: end,
                Stations_StationID: this.stations.find(x => x.name === station).StationID,
                salary_per_hour: salary
            }, {
                headers:{
                    "Authorization": this.token
                }
            }).then(response => {
              check++;
              if(check == this.employees.length){
                window.location.reload()
              } 
            }).catch(e => {
                alert(e.response.data);
            });
        } else {
            alert('Fill all the fields.')
        }
      }
    },
    Delete(employee) {
      axios.delete("http://10.101.52.46:8080/databind/api/managers/employees/" + employee.EmployeeID, {
        headers:{
            "Authorization": this.token
        }
      }).then(response => {
        window.location.reload()
      }).catch(e => {
          console.log(e);
      });
    }
  }
};
</script>
<style lang="scss" scoped>
.container {
  margin: auto;
  margin-top: 138px;
  max-width: 88% !important;
  background: #fff;
  border: 1px solid rgba(0, 0, 0, 0.3);
  padding: 16px;
  border-radius: 8px;
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

.menu-data {
  table {
    width: 100%;
    border-collapse: collapse;
    border-radius: 6px;
    text-align: left;
    overflow: hidden;
    .bookingId {
      cursor: pointer;
    }
    .bookingId:hover {
      background-color: #00ff99;
    }
    .employer-row{
      border-bottom: 1px solid rgba(0, 0, 0, 0.3);
    }
    td, th {
      border-top: 1px solid #ecf0f1;
      padding: 6px 4px;
      position: relative;
      input{
        padding: 2px;
      }
      .intInput{
        max-width: 48px;
      }
      input:disabled{
        background: none;
        border: none;
      }
      input:enabled{
        border: 1px solid rgba(0, 0, 0, 0.3);
        border-radius: 3px;
      }
    }

    td {
      border-left: 1px solid #ecf0f1;
      border-right: 1px solid #ecf0f1;
    }
    
    th {
      background-color: #00ff99;
    }
    tr:nth-of-type(even) {
      background-color: lighten(#00ff99, 35%);
    }
  }
  .mobile-header {
    display: none;
  }

  @media only screen and (max-width: 760px) {
    p {
      display: block;
      font-weight: bold;
    }

    table {
      tr {
        td:not(:first-child),
        th:not(:first-child),
        td:not(.total-val) {
          display: none;
        }

        &:nth-of-type(even) td:first-child {
          background-color: lighten(#4ecdc4, 35%);
        }
        &:nth-of-type(odd) td:first-child {
          background-color: white;
        }

        &:nth-of-type(even) td:not(:first-child) {
          background-color: white;
        }

        th:first-child {
          width: 100%;
          display: block;
        }

        th:not(:first-child) {
          width: 40%;
          transition: transform 0.4s ease-out;
          transform: translateY(-9999px);
          position: relative;
          z-index: -1;
        }

        td:not(:first-child) {
          transition: transform 0.4s ease-out;
          transform: translateY(-9999px);
          width: 60%;
          position: relative;
          z-index: -1;
        }

        td:first-child {
          display: block;
          cursor: pointer;
        }

        &.total th {
          width: 25%;
          display: inline-block;
        }

        td.total-val {
          display: inline-block;
          transform: translateY(0);
          width: 75%;
        }
      }
    }
  }

  @media only screen and (max-width: 300px) {
    table {
      tr {
        th:not(:first-child) {
          width: 50%;
          font-size: 14px;
        }

        td:not(:first-child) {
          width: 50%;
          font-size: 14px;
        }
      }
    }
  }
}
</style>
