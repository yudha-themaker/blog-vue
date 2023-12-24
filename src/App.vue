<script setup>
//import ref
import { ref, onMounted } from "vue";

//import router
import { useRouter, useRoute } from 'vue-router';

//import api
import api from "./api";

//init router
const router = useRouter();

//init route
const route = useRoute();

const errors = ref([]);

const name = localStorage.getItem('name');
const islogin = localStorage.getItem('islogin');

//get token
const token = localStorage.getItem('token')
const withToken = {
    headers: {
        Authorization: `Bearer ${token}`
    }
};

const logout = async () => {
    islogin = false;
    localStorage.setItem('islogin', false);
    localStorage.removeItem('islogin');
    localStorage.removeItem('token');
    localStorage.removeItem('name');

    router.push({ path: "/login" });
}
</script>

<template>
    <div class="">
        <nav class="navbar navbar-expand-lg bg-danger" data-bs-theme="dark">
            <div class="container">
                <router-link :to="{ name: 'home' }" class="navbar-brand">HOME</router-link>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navbarSupportedContent">
                    <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                        <li class="nav-item">
                            <router-link :to="{ name: 'posts.index' }" class="nav-link active" aria-current="page">POSTS</router-link>
                        </li>
                    </ul>
                    <ul class="navbar-nav ms-auto mb-2 mb-lg-0" role="search">
                        <span v-if="islogin" class="text-light"> Welcome, {{ name }}</span>
                    </ul>
                </div>
                <router-link :to="{ name: 'registration' }" v-if="!islogin" class="btn btn-secondary btn-sm ms-3">REGISTRATION</router-link>
    
                <div>
                    <router-link :to="{ name: 'auth.login' }" v-if="islogin" @click="logout" class="btn btn-dark btn-sm ms-3">LOGOUT</router-link>
                </div>
                <div>
                    <router-link :to="{ name: 'auth.login' }" v-if="!islogin" class="btn btn-primary btn-sm ms-3">LOGIN</router-link>
    
                </div>
    
            </div>
        </nav>
    
        <router-view></router-view>
    
    </div>
</template>
