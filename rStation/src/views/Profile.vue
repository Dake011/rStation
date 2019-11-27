<template>
    <div class="container bootstrap snippet">
        <div class="row menu-items">
            <ProfileMenu></ProfileMenu> 
            <div class="menu-data row col-sm-9">
                <div class="col-sm-4"><!--left col-->
                    <div class="text-center profile-img">
                        <img src="http://ssl.gstatic.com/accounts/ui/avatar_2x.png" class="avatar img-circle img-thumbnail" alt="avatar">
                        <input type="file" class="text-center">
                    </div><br>   
                </div>
                <div class="col-sm-8">
                    <div class="tab-pane active" id="home">
                        <form class="form" id="registrationForm">
                            <div class="form-group">
                                <div class="col-sm-10">
                                    <label for="first_name"><h4>First name</h4></label>
                                    <input v-model="first_name" :class="{ 'active': hasfocus == 1 }" @focusin="focusIn(1)" @focusout="focusOut(1)" type="text" class="form-control prefix" name="first_name" id="first_name" placeholder="first name" title="enter your first name if any." :disabled="viewMode">
                                </div>
                            </div>
                            <div class="form-group">                        
                                <div class="col-sm-10">
                                    <label for="last_name"><h4>Last name</h4></label>
                                    <input v-model="last_name" :class="{ 'active': hasfocus == 2 }" @focusin="focusIn(2)" @focusout="focusOut(2)" type="text" class="form-control" name="last_name" id="last_name" placeholder="last name" title="enter your last name if any." :disabled="viewMode">
                                </div>
                            </div>        
                            <div class="form-group">              
                                <div class="col-sm-10">
                                    <label for="password"><h4>Password</h4></label>
                                    <input v-model="password" :class="{ 'active': hasfocus == 3 }" @focusin="focusIn(3)" @focusout="focusOut(3)" type="text" class="form-control" name="password" id="password" placeholder="password" title="enter your password." :disabled="viewMode">
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-sm-10">
                                    <label for="email"><h4>Email</h4></label>
                                    <input v-model="email" :class="{ 'active': hasfocus == 4 }" @focusin="focusIn(4)" @focusout="focusOut(4)" type="email" class="form-control" name="email" id="email" placeholder="you@email.com" title="enter your email." disabled>
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-sm-10">
                                    <input type="button" class="btn btn-warning" v-if="viewMode" @click="editData()" value="Edit">
                                    <input type="button" class="btn btn-danger" v-else @click="saveData()" value="Save">
                                    <input type="button" class="btn btn-default" v-if="!viewMode" @click="resetData()" value="Reset">
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import ProfileMenu from '../components/ProfileMenu.vue'
import axios from 'axios'
export default {
    components:{
        ProfileMenu
    },
    data() {
        return {
                first_name: "",
                last_name: "",
                email: "", 
                password: "",
                hasfocus: 0,
                viewMode: true,
                token: null
            };
        },
    mounted(){
        this.token = localStorage.data
        axios.get('http://10.101.52.46:8080/databind/api/users/me', {
            headers: {
                "Authorization" :  this.token
            }
            
        })
        .then(response => {
            this.first_name = response.data.firstname;
            this.last_name = response.data.surname;
            this.email = response.data.mail;
            this.password = response.data.password;
        })
        .catch(e => {
            console.log(e);
        })
        
    },
    methods: {
        focusIn(value) {
            this.hasfocus = value;
            
        },
        focusOut(value) {
            this.hasfocus = 0;
        },
        editData(){
            this.viewMode = false;
        },
        saveData(){
            if(this.email && this.password && this.first_name && this.last_name){
                this.viewMode = true
                axios.put('http://10.101.52.46:8080/databind/api/users/me',{
                    mail: this.email,
                    password: this.password,
                    firstname: this.first_name,
                    surname: this.last_name
                },{
                    headers:{
                        "Authorization": this.token
                    }
                })
                .then(response => {
                    this.$router.push('/profile');
                })
                .catch(e => {
                    console.log(e);
                })
            } else {
                alert("Forms may not be empty.");
            }
        },
        resetData(){
            this.password = '';
            this.first_name = '';
            this.last_name = '';
        }
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
        background:rgba(0,0,0,0.1);
        border-radius: 6px;
        .list-group{
            width: 60%;
            text-align: center;
            margin: auto;
        }
        .profile-img{
            margin-top: 36px;
            .img-circle{
                border-radius: 50%;
            }
            input[type="file"]{   
                margin-top: 12px;         
                cursor: pointer;
            }
        }
        #registrationForm{
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
</style>