<script setup>
    import {ref, watch, inject, onMounted} from 'vue'
    import axios from 'axios'
    defineEmits(['changePostStateTodetails'])

    const props =  defineProps(['postListCategory'])
    const categoryList = ref(null)
    const categoryListLoader = ref(false)
    const formatDate = inject('formatDate')
    const getCategoryName = inject('getCategoryName')

    const getCategoryList = async (newValue) => {
        categoryListLoader.value = true
        let URL = newValue === 'home' ? 'https://basic-blog.teamrabbil.com/api/post-newest' : 'https://basic-blog.teamrabbil.com/api/post-list/'+newValue

        let catListRes = await axios.get(URL)
        if(catListRes.status === 200){
            categoryList.value = catListRes['data']
            categoryListLoader.value = false
        }
    }
  
    watch(() => props.postListCategory, async (newValue) => {
      try {
        await getCategoryList(newValue)
      }catch (e) {
        alert(e)
      }
    })

    onMounted(async () => {
        try{
            await getCategoryList(props.postListCategory)
        }
        catch(e){
            alert(e)
        }
    })
</script>

<template>
    <div class="content col-xs-8">
        <div v-if="categoryListLoader" id="postlist-preloader-container">
            <div id="preloader-wrap">
                <div id="preloader"></div>
            </div>
        </div>
       <!-- ARTICLE 1 -->
       <article v-else v-for="listItem in categoryList" :key="listItem.id">
            <div class="post-image">
            <img :src="listItem.img" alt="post image 1">
                <div class="category"><a href="#">{{ getCategoryName(listItem.category_id).length > 0 ? getCategoryName(listItem.category_id)[0].name : '' }}</a></div>
            </div>
            <div class="post-text">
            <span class="date">{{formatDate(listItem.updated_at)}}</span>
                <h2><a @click.prevent="$emit('changePostStateTodetails', listItem.id)" href="#">{{ listItem.title }}</a></h2>
                <p class="text">{{ listItem.short }}<a @click.prevent="$emit('changePostStateTodetails', listItem.id)" href="#"><i class="icon-arrow-right2"></i></a></p>                                 
            </div>
        </article>
    </div>
</template>