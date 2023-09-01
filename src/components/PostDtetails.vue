<script setup>
import {ref, onMounted } from 'vue';
import axios from 'axios'

const props =  defineProps(['postId'])
const singlePostInfo = ref(null)
onMounted(async () => {
    console.log('https://basic-blog.teamrabbil.com/api/post-details/'+props.postId)
    try{
        let URL = 'https://basic-blog.teamrabbil.com/api/post-details/'+props.postId
        let postRes = await axios.get(URL)
        if(postRes.status === 200){
            singlePostInfo.value = postRes['data']
        }
    }
    catch(e){
        alert(e)
    }
})
</script>

<template>
    <section v-if="singlePostInfo" class="tada-container content-posts page post-full-width">

        <!-- *** CONTENT *** -->

        <div class="content col-xs-12">

        <!-- ARTICLE 1 -->

        <article>
            <div class="post-image">
                <img :src="singlePostInfo.postDetails.img" alt="post image 1"> 
                </div>
                <div class="post-text">
                    <h2>{{ singlePostInfo.postDetails.title }}</h2>
                </div>                 
                <div class="post-text text-content">                  
                <div class="text"><p>{{ singlePostInfo.postDetails.content }}</p>
                    
                    <div class="clearfix"></div>
                    </div>
                </div>
            
            </article>

        </div>

        <div class="clearfix"></div>

    </section>
    <p v-else>No data found</p>
</template>