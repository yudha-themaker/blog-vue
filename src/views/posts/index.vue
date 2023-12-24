<script setup>
//import ref and onMounted
import { ref, onMounted } from 'vue';

//import router
import { useRouter, useRoute } from 'vue-router';

//init router
const router = useRouter();

//init route
const route = useRoute();


//import api
import api from '../../api';

//define state
const posts = ref([]);

const islogin = localStorage.getItem('islogin');


//get token
const id_user = localStorage.getItem('id_user')
const token = localStorage.getItem('token')
const withToken = {
    headers: {
        Authorization: `Bearer ${token}`
    }
};
const name = localStorage.getItem('name')

const fetchDataPosts = async () => {
    await api.get(`/api/posts-by/${id_user}`, withToken)
        .then(response => {
            posts.value = response.data.data
        });
}

onMounted(() => { 
    if (!token) { 
        router.push({ path: "/login" });
    }  
    fetchDataPosts();
});

const deletePost = async (id) => {
    await api.delete(`/api/posts/${id}`, withToken)
    .then(() => {
            fetchDataPosts();
        })
};
</script>

<template>
    <div class="container mt-5 mb-5">
        <div class="row">
            <div class="col-md-12">
                <router-link :to="{ name: 'posts.create' }" class="btn btn-md btn-success rounded shadow border-0 mb-3">ADD POST</router-link>
                <div class="card border-0 rounded shadow">
                    <div class="card-body">
                        <table class="table table-bordered align-middle">
                            <thead class="bg-danger text-white text-center">
                                <tr>
                                    <th scope="col">Title</th>
                                    <th scope="col">Content</th>
                                    <th scope="col">Author</th>
                                    <th scope="col">Like</th>
                                    <th scope="col">Dislike</th>
                                    <th scope="col">Comment</th>
                                    <th scope="col">Published</th>
                                    <th scope="col" style="width:15%">Actions</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-if="posts.length == 0">
                                    <td colspan="4" class="text-center">
                                        <div class="alert alert-danger mb-0">
                                            Data Belum Tersedia!
                                        </div>
                                    </td>
                                </tr>
                                <tr v-else v-for="(post, index) in posts" :key="index">
                                    <td>{{ post.title }}</td>
                                    <td>{{ post.content }}
                                        <p v-for="(comment, index) in post.post_comment" :key="index">{{ comment.message }}</p>
                                    </td>
                                    <td class="text-center"><small>{{ post.author }}</small></td>
                                    <td class="text-center">{{ post.post_like == 0 ? '' : post.post_like }}</td>
                                    <td class="text-center">{{ post.post_dislike == 0 ? '' : post.post_dislike }}</td>
                                    <td class="text-center">{{ post.comments == 0 ? '' : post.comments }}</td>
                                    <td class="text-center"><small>{{ post.published_date }}</small></td>
                                    <td class="text-center">
                                        <router-link :to="{ name: 'posts.edit', params:{id: post.id} }" class="btn btn-sm btn-primary rounded-sm shadow border-0 me-2">EDIT</router-link>
                                        <button @click.prevent="deletePost(post.id)" class="btn btn-sm btn-danger rounded-sm shadow border-0">DELETE</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>