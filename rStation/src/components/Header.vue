<template>
    <header class="header_area" id="header" :class="{ 'sticky': isSticky }" v-on:scroll="handleScroll">
        <div class="row h-100">
            <div class="col-12 h-100">
                <nav class="h-100 navbar navbar-expand-lg">
                    <a class="navbar-brand" @click="$router.push('/')"><img src="../assets/img/core-img/train1.png" alt=""></a>
                    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#dorneNav" aria-controls="dorneNav" aria-expanded="false" aria-label="Toggle navigation"><span class="fa fa-bars"></span></button>
                    <!-- Nav -->
                    <div class="collapse navbar-collapse" id="dorneNav">
                        <ul class="navbar-nav mr-auto" id="dorneMenu">
                            <li class="nav-item">
                                <a class="nav-link" @click="$router.push('/')">Home</a>
                            </li>
                            <li class="nav-item">
                                <a class="nav-link" @click="$router.push('/')">About</a>
                            </li>
                        </ul>
                        <!-- Search btn -->
                        <div class="dorne-signin-btn" v-if="!isUser">
                            <a @click="$router.push('/login')">Sign in</a>
                        </div>
                        <!-- Signin btn -->
                        <div class="dorne-signin-btn" v-if="!isUser">
                            <a @click="$router.push('/register')">Register</a>
                        </div>
                        <div class="dorne-signin-btn" v-if="isUser">
                            <mdb-dropdown class="user-dropdown">
                                <mdb-dropdown-toggle class="user-btn text-capitalize" slot="toggle">User Name<i class="fas fa-caret-down"></i><img src="http://ssl.gstatic.com/accounts/ui/avatar_2x.png" class="" alt="avatar"></mdb-dropdown-toggle>
                                <mdb-dropdown-menu>
                                <mdb-dropdown-item class="user-li" @click="$router.push('/profile')"><i class="fas fa-user-circle"></i>Profile</mdb-dropdown-item>
                                <mdb-dropdown-item class="user-li" @click="$router.push('/bookings')"><i class="fas fa-history"></i>Bookings</mdb-dropdown-item>
                                <div class="dropdown-divider"></div>
                                <mdb-dropdown-item class="user-li" @click="logOut()"><i class="fas fa-power-off"></i>Log out</mdb-dropdown-item>
                                </mdb-dropdown-menu>
                            </mdb-dropdown>
                        </div>
                    </div>
                </nav>
            </div>
        </div>
    </header>
</template>
<script>
import { mdbDropdown, mdbDropdownItem, mdbDropdownMenu, mdbDropdownToggle } from 'mdbvue';

export default {
    components: {
      mdbDropdown,
      mdbDropdownItem,
      mdbDropdownMenu,
      mdbDropdownToggle
    },
    data(){
        return {
            isSticky: false,
            i:0,
            isUser: false
        }
    },
    mounted: function () {
        this.isUser = false;
        if(localStorage.getItem('data') && localStorage.getItem('data').length > 20) this.isUser = true;
    },
    methods: {
        handleScroll(event) {
        // Any code to be executed when the window is scrolled
            if(window.scrollY === 0){
                this.isSticky = false
            }
            if(window.scrollY > 50){
                this.isSticky = true
            }
        },
        logOut(){
            localStorage.removeItem('data');
            this.isUser = false;
            this.$router.push('/login')
        }
    },
    created() {
        window.addEventListener('scroll', this.handleScroll);
    },
    destroyed() {
        window.removeEventListener('scroll', this.handleScroll);
    }
}
</script>
<style lang="scss" scoped>
.header_area {
    position: fixed;
    width: 100%;
    z-index: 9999;
    height: 100px;
    top: 0;
    left: 0;
    -webkit-transition-duration: 500ms;
    transition-duration: 500ms;
    border-bottom: 1px solid rgba(255, 255, 255, 0.4);
    background-color: rgba(14, 2, 35, 0.5);
}

.sticky{
    background-color: rgba(14, 2, 35, 0.9);
    height: 80px;
    z-index: 9999;
    border-bottom: 1px solid transparent;
    -webkit-transition-duration: 500ms;
    transition-duration: 500ms;
}

.navbar-brand {
	color: #fff;
	font-size: 24px;
	font-weight: 700;
    margin-right: 70px;
    img{
        margin-left: 30px;
        max-width: 140px;
        max-height: 140px;
    }
}

.navbar-brand:hover,
.navbar-brand:focus {
    color: #fff;
    font-weight: 700;
}

.navbar-expand-lg .navbar-nav .nav-link {
    padding-right: 15px;
    padding-left: 15px;
    color: #fff;
    font-size: 15px;
}

.navbar-nav .nav-link:hover,
.navbar-nav .nav-item.active .nav-link {
    color: #44475c;
}

.dorne-search-btn > a,
.dorne-signin-btn > a {
    color: #fff;
    display: inline-block;
    margin-right: 40px;
    font-size: 15px;
}
.dorne-signin-btn .user-dropdown{
    img{
        width: 24px;
        height: 24px;
        border-radius: 50%;
    }
    .user-btn{
        color: #fff;
        box-shadow: none;
        cursor: pointer;
        font-size: 15px;
    }
    .user-btn:hover{
        background: none;
        color: #44475c;
    }
    .user-li{
        padding: 6px 4px 6px 16px;
        font-size: 15px;
    }
    .user-li:hover{
        background:#44475c;
        color: #fff !important;
    }
    .fas{
        line-height: 1.8;
        margin-left: 4px;
        margin-right: 8px;
        font-size: 18px;
    }
}

.dorne-search-btn > a i {
    padding-right: 10px;
}

.dropdown-menu {
    border: none;
}

.dropdown-item {
    font-weight: 600;
    font-size: 14px;
}

.dropdown-item:hover {
    background-color: #7643ea;
    color: #fff;
}

.dropdown-toggle:after {
    border: none;
}
</style>


