<template>
  <section class="form-elegant">
    <mdb-row class="justify-content-md-center">
      <mdb-col class="mt-5" md="5">
        <mdb-card>
          <mdb-card-body class="mx-4 grey-text">
            <div class="text-center">
              <h3 class="dark-grey-text mb-5"><strong>Sign up</strong></h3>
            </div>
            <form novalidate="true" @submit.prevent="checkForm">
              <mdb-input
                v-model="firstName"
                type="text"
                icon="user"
                label="First name"
                required
                invalidFeedback="Please provide a valid name."
                validFeedback="Look's good."
              />
              <mdb-input
                v-model="lastName"
                label="Last name"
                icon="user"
                type="text"
                required
                invalidFeedback="Please provide a valid name."
                validFeedback="Look's good."
              />
              <mdb-input
                v-model="agentName"
                label="Agent name (Optional)"
                icon="store-alt"
                type="text"
              />
              <mdb-input
                v-model="email"
                label="Your email"
                icon="envelope"
                type="email"
                required
                invalidFeedback="Please provide a valid mail."
                validFeedback="Look's good."
              />
              <mdb-input
                v-model="pass"
                label="Your password"
                icon="lock"
                type="password"
                required
                invalidFeedback="Please provide a valid password."
                validFeedback="Look's good."
              />
              <mdb-input
                v-model="newPass"
                label="Your password"
                icon="lock"
                type="password"
                required
                invalidFeedback="Please provide a valid password."
                validFeedback="Look's good."
              />
              <div class="text-center mb-3">
                <mdb-btn
                  gradient="blue"
                  type="submit"
                  rounded
                  class="btn-block z-depth-1a"
                  >Register</mdb-btn
                >
              </div>
            </form>
          </mdb-card-body>
          <mdb-modal-footer class="mx-5 pt-3 mb-1">
            <p class="font-small grey-text d-flex justify-content-end">
              Have an account?
              <a class="blue-text ml-1" @click="$router.push('/login')">
                Sign In</a
              >
            </p>
          </mdb-modal-footer>
        </mdb-card>
      </mdb-col>
    </mdb-row>
  </section>
</template>

<script>
import {
  mdbRow,
  mdbCol,
  mdbCard,
  mdbCardBody,
  mdbInput,
  mdbBtn,
  mdbIcon,
  mdbModal,
  mdbModalBody,
  mdbModalFooter
} from "mdbvue";
import axios from "axios";
export default {
  name: "FormsPage",
  components: {
    mdbRow,
    mdbCol,
    mdbCard,
    mdbCardBody,
    mdbInput,
    mdbBtn,
    mdbIcon,
    mdbModal,
    mdbModalBody,
    mdbModalFooter
  },
  data() {
    return {
      firstName: null,
      lastName: null,
      agentName: null,
      email: null,
      pass: null,
      newPass: null
    };
  },
  methods: {
    checkForm(event) {
      event.target.classList.add("was-validated");
        if(
            this.firstName &&
            this.lastName &&
            this.email.includes("@") &&
            this.email.includes(".") &&
            this.pass &&
            this.newPass
        ){
            axios.post("http://10.101.52.46:8080/databind/api/register", {
                mail: this.email,
                password: this.pass,
                firstname: this.firstName,
                surname: this.lastName
            }).then(response => {
                localStorage.data = response.data;
                this.$router.push("/profile");
            })
            .catch(e => {
                console.log(e);
            });
        }
        }
    }
  }
</script>

<style lang="scss" scoped>
    .form-elegant {
        margin: auto;
        margin-top: 90px;
        .font-small {
            font-size: 0.8rem; 
        }
        .z-depth-1a {
            -webkit-box-shadow: 0 2px 5px 0 rgba(55, 161, 255, 0.26), 0 4px 12px 0 rgba(121, 155, 254, 0.25);
            box-shadow: 0 2px 5px 0 rgba(55, 161, 255, 0.26), 0 4px 12px 0 rgba(121, 155, 254, 0.25); 
        }
        .z-depth-1-half, .btn:hover {
            -webkit-box-shadow: 0 5px 11px 0 rgba(85, 182, 255, 0.28), 0 4px 15px 0 rgba(36, 133, 255, 0.15);
            box-shadow: 0 5px 11px 0 rgba(85, 182, 255, 0.28), 0 4px 15px 0 rgba(36, 133, 255, 0.15); 
        }
    }
</style>