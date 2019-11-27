<template>
    <div>
        <section class="form-elegant" v-if="!fPass">
            <mdb-row class="justify-content-md-center">
            <mdb-col class="mt-5" md="5">
                <mdb-card>
                <mdb-card-body class="mx-4 grey-text">
                    <div class="text-center">
                        <h3 class="dark-grey-text mb-5"><strong>Sign in</strong></h3>
                    </div>
                        <mdb-input v-model="email" label="Your email" icon="envelope" type="email"/>
                        <mdb-input v-model="pass" label="Your password" icon="lock" type="password" containerClass="mb-0"/>
                        <p class="font-small blue-text d-flex justify-content-end pb-3">Forgot <a class="blue-text ml-1" @click="fPass = !fPass"> Password?</a></p>
                    <div class="text-center mb-3">
                        <mdb-btn type="button" gradient="blue" rounded class="btn-block z-depth-1a" @click="loginForm()">Sign in</mdb-btn>
                    </div>
                    <p class="font-small dark-grey-text text-right d-flex justify-content-center mb-3 pt-2"> or Sign in with:</p>
                    <div class="row my-3 d-flex justify-content-center">
                        <mdb-btn type="button" color="white" rounded class="mr-md-3 z-depth-1a"><mdb-icon fab icon="facebook" class="blue-text text-center"/></mdb-btn>
                        <mdb-btn type="button" color="white" rounded class="mr-md-3 z-depth-1a"><mdb-icon fab icon="twitter" class="blue-text"/></mdb-btn>
                        <mdb-btn type="button" color="white" rounded class="z-depth-1a"><mdb-icon fab icon="google-plus" class="blue-text"/></mdb-btn>
                    </div>
                </mdb-card-body>
                <mdb-modal-footer class="mx-5 pt-3 mb-1">
                    <p class="font-small grey-text d-flex justify-content-end">Not a member? <a class="blue-text ml-1" @click="$router.push('/register')"> Sign Up</a></p>
                </mdb-modal-footer>
                </mdb-card>
            </mdb-col>
            </mdb-row>
        </section>
        <section class="form-elegant" v-else>
            <mdb-row class="justify-content-md-center">
            <mdb-col class="mt-5" md="5">
                <mdb-card>
                <mdb-card-body class="mx-4 grey-text" v-if="!fPassVerifyEmail">
                    <div class="text-center">
                        <h3 class="dark-grey-text mb-5"><strong>Password recover</strong></h3>
                    </div>
                        <mdb-input label="Your email" icon="envelope" type="email"/>
                        <p class="font-small blue-text d-flex justify-content-end pb-3"><a class="blue-text ml-1" @click="fPass = !fPass">Go Back</a></p>
                    <div class="text-center mb-3">
                        <mdb-btn type="button" gradient="blue" rounded class="btn-block z-depth-1a" @click="fPassVerifyEmail = !fPassVerifyEmail">Verify</mdb-btn>
                    </div>
                </mdb-card-body>
                <mdb-card-body class="mx-4 grey-text" v-else>
                    <div class="text-center">
                        <h3 class="dark-grey-text mb-5"><strong>Password recover</strong></h3>
                    </div>
                        <mdb-input class="h-25" label="We have send a new password to email." icon="check-square" type="email" disabled/>
                    <div class="text-center mb-3">
                        <mdb-btn type="button" gradient="blue" rounded class="btn-block z-depth-1a" @click="fPassVerifyEmail = !fPassVerifyEmail, fPass = false">Go Back</mdb-btn>
                    </div>
                </mdb-card-body>
                <mdb-modal-footer class="mx-5 pt-3 mb-1">
                    <p class="font-small grey-text d-flex justify-content-end">Not a member? <a class="blue-text ml-1" @click="$router.push('/register')"> Sign Up</a></p>
                </mdb-modal-footer>
                </mdb-card>
            </mdb-col>
            </mdb-row>
        </section>
    </div>
</template>

<script>
  import { mdbRow, mdbCol, mdbCard, mdbCardBody, mdbInput, mdbBtn, mdbIcon, mdbModal, mdbModalBody, mdbModalFooter } from 'mdbvue';
  import axios from 'axios'
  export default {
    name: 'FormsPage',
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
                fPass: false,
                fPassVerifyEmail: false,
                showModal: false,
                email: null,
                pass: null
            };
        },
    methods:{
        loginForm() {
            if (this.email.includes("@") && this.email.includes(".") && this.pass) {
                axios
                .post("http://10.101.52.46:8080/databind/api/login", {
                    mail: this.email,
                    password: this.pass
                })
                .then(response => {
                    localStorage.data = response.data.second;
                    localStorage.manager = response.data.first;
                    this.$router.push("/profile");
                    window.location.reload();
                })
                .catch(e => {
                    alert(e.response.data);
                });
            } else {
                alert("Fill in all fields! or Wrong email!");
            }
        },
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
