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
              <th>Name</th>
              <th>Time slots</th>
              <th>Station</th>
              <th>Salary</th>
              <th>Week hours</th>
              <th></th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr class="employer-row" v-for="employer in employers" :key="employer">
              <td class="bookingId">{{employer.EmployerId}}</td>
              <td>
                <input
                  :id="'name_' + employer.EmployerId"
                  type="text"
                  v-model="employer.EmployerName"
                />
              </td>

              <td>
                <input
                  :id="'workDays_' + employer.EmployerId"
                  type="text"
                  v-model="employer.EmployerWorkDays"
                />
              </td>
              <td>
                <input
                  :id="'station_' + employer.EmployerId"
                  type="text"
                  v-model="employer.WorkingStation"
                />
              </td>
              <td>
                <input
                  class="intInput"
                  :id="'salary_' + employer.EmployerId"
                  type="text"
                  v-model="employer.Salary"
                />
              </td>
              <td>
                <input
                  class="intInput"
                  :id="'workHours_' + employer.EmployerId"
                  type="text"
                  v-model="employer.OverallHours"
                />
              </td>
              <td v-on:click="Edit(employer)" style="text-align:center">
                <i class="fas fa-edit"></i>
              </td>
              <td v-on:click="Delete(employer)" style="text-align:center">
                <i class="fas fa-trash"></i>
              </td>
            </tr>
            <tr>
              <td style="border:none;background: #fff;">
                <div style="text-align:left;position:relative">
                  <button class="button" v-on:click="AddEmpolyer()">Add Employee</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!--/col-9-->
    </div>
    <div style="text-align:right;position:relative">
      <button class="button" v-on:click="Reset()">Reset</button>
      <button class="button" v-on:click="Save()">Save</button>
    </div>
  </div>
</template>
<script>
import ProfileMenu from "../components/ProfileMenu.vue";
import BookingModal from "../components/BookingModal.vue";
import { get, del } from "../helpers/api";

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
      employers: [
        {
          EmployerId: "01892340",
          EmployerName: "Kopbosinov Jaxsylyk",
          EmployerWorkDays: "M,W,T  12:00-19:00",
          WorkingStation: "Astana",
          Salary: 120000,
          OverallHours: 32
        },
        {
          EmployerId: "01892341",
          EmployerName: "Jaxsylyk Kopbosinov",
          EmployerWorkDays: "M,W,T 12:00-19:00",
          WorkingStation: "Almaty",
          Salary: 130000,
          OverallHours: 36
        }
      ]
    };
  },
  mounted: function () {
    this.disabler();
    this.getEmployers();
  },
  methods: {
    disabler(){
      var temp = document.getElementsByClassName('employer-row');
      for(let x = 0; x < temp.length; x++){
        var inputs = temp[x].querySelectorAll('input');
        for(let i = 0; i < inputs.length; i++){
          inputs[i].disabled = true;
        }
      }
    },
    AddEmpolyer(){
      if(this.canAddEmployer){
        this.canAddEmployer = false;
        this.employers.push({
            EmployerId: "",
            EmployerName: "",
            EmployerWorkDays: "",
            WorkingStation: "",
            Salary: 0,
            OverallHours: 0
        })
      } else {
        alert("Please, fill all fields.");
      }
    },
    Edit(employer){
      document.getElementById('name_' + employer.EmployerId).disabled = false;
      document.getElementById('workDays_' + employer.EmployerId).disabled = false;
      document.getElementById('station_' + employer.EmployerId).disabled = false;
      document.getElementById('salary_' + employer.EmployerId).disabled = false;
      document.getElementById('workHours_' + employer.EmployerId).disabled = false;
    },
    Save() {
      //axios
      window.location.reload();
    },
    Reset(){
      window.location.reload();
    },
    Delete(emp) {
      var _this = this;
      del(
        this,
        "api/products",
        {
          params: {
            id: id
          }
        },
        function(response) {
          console.log(response);
          console.log("deleted");
          _this.getList();
        },
        function(error) {}
      );
    },
    getEmployers() {
      let _this = this;
      get(
        this,
        "/api/product/locality",
        {
          params: {
            locality_id: product.locality_id
          }
        },
        function(response) {},
        function(error) {
          console.log("got error", error);
        }
      );
    }
  },
  computed: {
    OverallHours() {
      return;
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
      padding: 8px 6px;
      position: relative;
      input{
        padding: 2px;
      }
      .intInput{
        max-width: 72px;
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
