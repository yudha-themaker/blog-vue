<script setup>
//import ref
import { ref, onMounted } from "vue";

//import router
import { useRouter, useRoute } from 'vue-router';

//import api
import api from "../api";

//init router
const router = useRouter();

//init route
const route = useRoute();

//define state
const posts = ref([]);
const errors = ref([]);

const id_user = localStorage.getItem('id_user');
const islogin = localStorage.getItem('islogin');


const token = localStorage.getItem('token');
const withToken = {
    headers: {
        Authorization: `Bearer ${token}`
    }
};

const fetchDataPosts = async () => {
    await api.get('/api/posts', withToken)
        .then(response => {
            posts.value = response.data.data;

        });
}

//run hook "onMounted"
onMounted(() => {
    if (!token) {
        router.push({ path: "/login" });
    }

    fetchDataPosts();
});

const name = ref('no name');
const count = ref(0);

let formData = new FormData();

const addLikePost = async (id_post, id_user) => {
    // alert('hekkk' + id_post + ' dan '+ id_user);
    let formData = new FormData();
    formData.append("id_post", id_post);
    formData.append("id_user", id_user);

    await api.post(`/api/posts-like`, formData, withToken)
        .then(() => {
            fetchDataPosts();
        })
        .catch((error) => {
            errors.value = error.response.data;
        });
}
const addDislikePost = async (id_post, id_user) => {
    let formData = new FormData();
    formData.append("id_post", id_post);
    formData.append("id_user", id_user);

    await api.post(`/api/posts-dislike`, formData, withToken)
        .then(() => {
            fetchDataPosts();
        })
        .catch((error) => {
            errors.value = error.response.data;
        });
}


const likeThisComment = async (id_comment, id_user) => {
    let formData = new FormData();
    formData.append("id_comment", id_comment);
    formData.append("id_user", id_user);
    console.log('ini like this comment '+ id_comment + ' da ' + id_user)
    
    await api.post(`/api/comment-like`, formData, withToken)
    .then(() => {
            fetchDataPosts();
        })
        .catch((error) => {
            errors.value = error.response.data;
        });
}
const dislikeThisComment = async (id_comment, id_user) => {

    let formData = new FormData();
    formData.append("id_comment", id_comment);
    formData.append("id_user", id_user);

    await api.post(`/api/comment-dislike`, formData, withToken)
        .then(() => {
            fetchDataPosts();
        })
        .catch((error) => {
            errors.value = error.response.data;
        });
} 
</script>

<template>
    <div class="p-5 mb-4 rounded-0 bg-info">
        <div class="container-fluid py-5">
            <h1 class="display-5 fw-bold">Welcome to Vue-Blog</h1>
            <p class="col-md-8 fs-5">created by Muhammad Yudha </p>
        </div>
    </div>
    
    <div class="container mt-5 mb-5">
        <div v-if="posts.length == 0">
            <div class="alert alert-danger mb-0">
                Data Belum Tersedia!
            </div>
        </div>
        <div v-else v-for="(post, index) in posts" :key="index">
            <div class="card mb-4 shadow-sm">
                <div class="card-header">
                    <strong>{{ post.title }}</strong> <br>
                    <small> <i class="bi bi-person-fill"></i> Author :  {{ post.author }} |  <i class="bi bi-clock"></i> Published : {{ post.published_date_format }}</small>
                </div>
                <div class="card-body">
                    <p class="card-text">{{ post.content }}</p>
    
                    <hr>
                    <div class="row">
                        <div class="col">
                            <div class="btn-group" role="group" aria-label="Basic outlined example">
                                <button type="button" @click="addLikePost(post.id, id_user)" class="btn btn-outline-secondary btn-sm"> {{ post.post_like}} <span class="{{ likeColor }}"><i class="bi bi-hand-thumbs-up-fill"></i></span>Like</button>
                                <button type="button" @click="addDislikePost(post.id, id_user)" class="btn btn-outline-secondary btn-sm">{{ post.post_dislike}} <span class="text-secondary"><i class="bi bi-hand-thumbs-down-fill"></i></span> Dislike</button>
                            </div>
                        </div>
                        <div class="col me-2 text-end">
                            <button type="button" class="btn btn-outline-secondary btn-sm"> {{ post.comments}}  <i class="bi bi-chat-dots"></i> Comments</button>
                        </div>
                    </div>
                    <hr>
                    <div v-for="(comment, index) in post.post_comment" :key="index" class="">
                        <div class="container">
                            <div class="row">
                                <div class="col">
                                    <div class="card mb-2 bg-body-tertiary rounded-3">
                                        <div class="card-body mx-3">
                                            <small>{{ comment.message }}
                                                    <br>
                                                    <hr>
                                                    <small style="color: grey">By. <i class="bi bi-person-fill"></i>  {{ comment.author }} |  <i class="bi bi-clock"></i>  {{ comment.date }}</small>
                                            </small>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-md-auto">
                                    <div class="btn-group-sm" role="group" aria-label="Basic outlined example">
                                        <button @click="likeThisComment(comment.id, id_user)" class="btn btn-sm btn-outline-secondary rounded-5 me-2"> {{ comment.likes }} <i class="bi bi-hand-thumbs-up"></i> </button>
                                        <button @click="dislikeThisComment(comment.id, id_user)" class="btn btn-sm btn-outline-secondary rounded-5"> {{ comment.dislikes }} <i class="bi bi-hand-thumbs-down"></i> </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
    
                        <div class="text-center me-2"> 
                        <router-link :to="{ name: 'posts.comment', params:{id: post.id} }" class="btn btn-md btn-primary rounded-sm shadow border-0 me-0 mt-2"><i class="bi bi-pen"></i> Write Comment </router-link>

                    </div>
                </div>
            </div>
        </div>
    
    
    
    
    </div>
</template>