<script setup>
//import ref and onMounted
import { ref, onMounted } from 'vue';
import { useRouter, useRoute } from 'vue-router';

//import api
import api from '../../api';
//init router
const router = useRouter();

//init route
const route = useRoute();

//define state
const post = ref([]);
const message = ref("");

//get token
const token = localStorage.getItem('token');
const withToken = {
    headers: {
        Authorization: `Bearer ${token}`
    }
};

const name = localStorage.getItem('name');
const id_user = localStorage.getItem('id_user');

//method fetchDataPosts
const addComment = async (id_post) => {
    let formData = new FormData();
    formData.append("id_post", id_post);
    formData.append("id_user", id_user);
    formData.append("message", message.value);

    await api.post(`/api/comment-add`, formData, withToken)
        .then(response => {
            router.push({ path: "/" });
        });
}

const fetchDataPosts = async () => {
    await api.get(`/api/posts/${route.params.id}`, withToken)
        .then(response => {
            post.value = response.data.data;
            console.log('heheh ' + post.value)
        });
}

//run hook "onMounted"
onMounted(() => {
    if (!token) {
        router.push({ path: "/login" });
    } else {
        console.log('ada token ' + token);
    }
    fetchDataPosts();
});
</script>

<template>
    <div class="container mt-5 mb-5">
        <div>
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
                                                <small style="color: grey">By. <i class="bi bi-person-fill"></i>  {{ comment.author }} |  <i class="bi bi-clock"></i>  {{ comment.date }}</small></small>
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
                    <div class="text-end me-2">
                    </div>
                    <hr>
                    <div class="mb-3">
                        <textarea v-model="message" class="form-control bg-body-tertiary" id="exampleFormControlTextarea1" rows="3" placeholder="Write a comment"></textarea>
                        <button @click="addComment(post.id)" class="btn btn-primary mt-4">Comment</button>
                    </div>
    
                </div>
            </div>
        </div>
    </div>
</template>