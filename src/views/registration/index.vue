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


 const validation = ref([])

 const registrationFailed = ref(null)
 const name = ref("");
 const email = ref("");
 const password = ref("");

 const registration = async () => {
     let formData = new FormData();

     formData.append("name", name.value);
     formData.append("email", email.value);
     formData.append("password", password.value);
     formData.append("password_confirmation", password.value);

     await api.post('/api/register', formData)
         .then(response => {

             if (response.data.success) {
                 alert('Registration Successfully, please login..');
                 router.push({ path: "/login" });
             }
             registrationFailed.value = true

         }).catch((error) => {
             validation.value = error.response.data
         });
 };
</script>

<template>
    <div class="container-fluid mt-5">
        <div class="row justify-content-center">
            <div class="col-md-4">
                <div v-if="registrationFailed" class="alert alert-danger">
                    Periksa kembali data yang anda isi
                </div>
                <div class="card border-0 rounded shadow">
                    <div class="card-body text-center">
                        <h4 class="text-center">REGISTRATION</h4>
                        <hr>
                        <form @submit.prevent="registration">
    
                            <div class="form-group mt-2 text-start">
                                <input type="text" v-model="name" class="form-control" placeholder="Name">
                            </div>
                            <div v-if="validation.name" class="mt-2 alert alert-danger">
                                {{ validation.name[0] }}
                            </div>
    
                            <div class="form-group mt-2 text-start">
                                <input type="email" v-model="email" class="form-control" placeholder="Email Address">
                            </div>
                            <div v-if="validation.email" class="mt-2 alert alert-danger">
                                {{ validation.email[0] }}
                            </div>
    
    
                            <div class="form-group mt-2 text-start">
                                <input type="password" v-model="password" class="form-control" placeholder="Password">
                            </div>
                            <div v-if="validation.password" class="mt-2 alert alert-danger">
                                {{ validation.password[0] }}
                            </div>
                            <br>
                            <button type="submit" class="btn btn-primary btn-block">Register</button>
                            <br>
                            <router-link :to="{ name: 'auth.login' }" v-if="name == ''" class="btn btn-link"><small>Already have account?</small></router-link>
    
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>