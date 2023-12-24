<script setup>
//import ref
import { ref, onMounted } from "vue";

//import router
import { useRouter, useRoute } from 'vue-router';

//import api
import api from "../../api";

//init router
const router = useRouter();

//init route
const route = useRoute();

//state validation
const validation = ref([])

const islogin = localStorage.getItem('islogin');


onMounted(() => {
    localStorage.removeItem('islogin');
    localStorage.removeItem('token');
    localStorage.removeItem('name');


});

const loginFailed = ref(null)
const email = ref("");
const password = ref("");

const login = async () => {
    let formData = new FormData();
    formData.append("email", email.value);
    formData.append("password", password.value);

    await api.post('/api/login', formData)
        .then(response => {
            if (response.data.success) {
                localStorage.setItem('token', response.data.token);
                localStorage.setItem('id_user', response.data.data.id);
                localStorage.setItem('name', response.data.data.name);
                localStorage.setItem('email', response.data.data.email);
                localStorage.setItem('islogin', true);
                router.push({ path: "/posts" });
            }
            loginFailed.value = true

        }).catch((error) => {
            validation.value = error.response.data
        });
};
</script>

<template>
    <div class="container-fluid mt-5">
        <div class="row justify-content-center">
            <div class="col-md-4">
                <div v-if="loginFailed" class="alert alert-danger">
                    Email atau Password Anda salah.
                </div>
                <div class="card border-0 rounded shadow">
                    <div class="card-body text-center">
                        <h4 class="text-center">LOGIN</h4>
                        <hr>
                        <form @submit.prevent="login">
                            <div class="form-group mt-2 text-start">
                                <!-- <label>Email address</label> -->
                                <input type="email" v-model="email" class="form-control" placeholder="Email Address">
                            </div>
                            <div v-if="validation.email" class="mt-2 alert alert-danger">
                                {{ validation.email[0] }}
                            </div>
                            <div class="form-group mt-2 text-start">
                                <!-- <label>Passwor d</label> -->
                                <input type="password" v-model="password" class="form-control" placeholder="Password">
                            </div>
                            <div v-if="validation.password" class="mt-2 alert alert-danger">
                                {{ validation.password[0] }}
                            </div>
                            <br>
                            <button type="submit" class="btn btn-primary btn-block">LOGIN</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>