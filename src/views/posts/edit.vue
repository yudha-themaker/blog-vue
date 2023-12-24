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

const id_user = localStorage.getItem('id_user');
//get token
const token = localStorage.getItem('token')
const withToken = {
    headers: {
        Authorization: `Bearer ${token}`
    }
};


//define state
const title = ref("");
const content = ref("");
const errors = ref([]);

//onMounted
onMounted(async () => {

    console.log('ini coba : ' + route.params.id);
    await api.get(`/api/posts/${route.params.id}`, withToken)
        .then(response => {

            //set response data to state
            title.value = response.data.data.title
            content.value = response.data.data.content
        });
})

const handleFileChange = (e) => {
};

const updatePost = async () => {
    let formData = new FormData();

    formData.append("id_user", id_user);
    formData.append("title", title.value);
    formData.append("content", content.value);

    await api.post(`/api/posts/${route.params.id}`, formData, withToken)
        .then(() => {
            router.push({ path: "/posts" });
        })
        .catch((error) => {
            errors.value = error.response.data;
        });
};
</script>

<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <div class="card border-0 rounded shadow">
                    <div class="card-body">
                        <form @submit.prevent="updatePost()">
                            <div class="mb-3">
                                <label class="form-label fw-bold">Title</label>
                                <input type="text" class="form-control" v-model="title" placeholder="Title Post">
                                <div v-if="errors.title" class="alert alert-danger mt-2">
                                    <span>{{ errors.title[0] }}</span>
                                </div>
                            </div>
                            <div class="mb-3">
                                <label class="form-label fw-bold">Content</label>
                                <textarea class="form-control" v-model="content" rows="5" placeholder="Content Post"></textarea>
                                <div v-if="errors.content" class="alert alert-danger mt-2">
                                    <span>{{ errors.content[0] }}</span>
                                </div>
                            </div>
                            <button type="submit" class="btn btn-md btn-primary rounded-sm shadow border-0">Update</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>