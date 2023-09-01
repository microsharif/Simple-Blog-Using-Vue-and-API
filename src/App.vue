<script setup>
  import {ref, onMounted, provide} from 'vue'
  import axios from 'axios'
  import PostList from './components/PostList.vue'
  import PostDtetails from './components/PostDtetails.vue'

  const postCategories = ref(null)
  const recentPosts = ref(null)
  const postListCategory = ref(null)
  const gotPostDetials = ref(false)
  const postDetailsId = ref(null)
  const pagePreLoader = ref(true)

  const formatDate  = (dateString) => {
    const dateObject = new Date(dateString);

    const month = dateObject.toLocaleString('en-US', { month: 'long' });
    const day = dateObject.getDate();
    const year = dateObject.getFullYear();

    const formattedDate = `${month} ${day}, ${year}`;
    return formattedDate;
  }

  const getCategoryName = (catId) => {
    let name = postCategories.value.filter((category) => {
      return category.id == catId 
    })
    return name
  }

  const changePostStateTocat = (catId) => {
    postListCategory.value = catId 
    gotPostDetials.value = false
  }

  const changePostStateTodetails = (posId) => {
    gotPostDetials.value = true
    postListCategory.value = ''
    postDetailsId.value = posId
  }

  onMounted(async () => {
    try{

      let catRes = await axios.get('https://basic-blog.teamrabbil.com/api/post-categories')
      if(catRes.status === 200){
        postCategories.value = catRes['data']
        console.log(postCategories.value)
        postListCategory.value = 'home'
      }

      let recentPost = await axios.get('https://basic-blog.teamrabbil.com/api/post-newest')
      if(recentPost.status === 200){
        recentPosts.value = recentPost['data']
      }

      pagePreLoader.value = false

    }
    catch(e){
      alert(e)
    }
  })

  provide('formatDate', formatDate)
  provide('getCategoryName', getCategoryName)
</script>

<template>
  <div v-if="pagePreLoader" id="preloader-container">
    <div id="preloader-wrap">
      <div id="preloader"></div>
    </div>
  </div>
    
  <header class="tada-container">
    <!-- MENU DESKTOP -->
  
    <nav class="menu-desktop menu-sticky">
      <ul class="tada-menu">
        <li><a :class="postListCategory === 'home' ? 'menu-active-class' : ''" @click.prevent="changePostStateTocat('home')" href="#">হোম</a></li>
        <li v-for="category in postCategories" :key="category.id"><a :class="postListCategory === category.id ? 'menu-active-class' : ''" @click.prevent="changePostStateTocat(category.id)" href="#">{{ category.name }}</a></li>
      </ul>
    
    </nav>
      
    <!-- MENU MOBILE -->
    <div class="menu-responsive-container"> 
      <div class="open-menu-responsive">|||</div> 
      <div class="close-menu-responsive">|</div>              
      <div class="menu-responsive">   
        <ul class="tada-menu">
          <li><a @click.prevent="changePostStateTocat('home')" href="#">হোম</a></li>
          <li v-for="category in postCategories" :key="category.id"><a @click.prevent="changePostStateTocat(category.id)" href="#">{{ category.name }}</a></li>
        </ul>                        
      </div>
    </div> <!-- # menu responsive container -->     
  </header>

	<section v-if="!gotPostDetials" class="tada-container content-posts">

    <!-- *** CONTENT *** -->


    <!-- *** SIDEBAR *** -->
    <PostList :postListCategory = "postListCategory" @changePostStateTodetails = "changePostStateTodetails"></PostList>
    <div class="sidebar col-xs-4">

      <!-- LATEST POSTS -->
                        
      <div class="widget latest-posts">
        <h3 class="widget-title">সাম্প্রতিক পোস্ট</h3>
        <div class="posts-container">
        
          <div v-for="recentpost in recentPosts" :key="recentpost.id" class="item">
            <img :src="recentpost.img" alt="post 1" class="post-image">
            <div class="info-post">
              <h5><a @click.prevent="changePostStateTodetails()" href="#">{{ recentpost.title }}</a></h5>
              <span class="date">{{ formatDate(recentpost.updated_at) }}</span>	
            </div> 
            <div class="clearfix"></div>   
          </div>
            
          <div class="clearfix"></div>
        </div>
      </div>

      <!-- FOLLOW US -->
                        
      <div class="widget follow-us">
        <h3 class="widget-title">
          আমাদের অনুসরণ করুন
        </h3>
        <div class="follow-container">
              <a href="#"><i class="icon-facebook5"></i></a>
              <a href="#"><i class="icon-twitter4"></i></a>
              <a href="#"><i class="icon-google-plus"></i></a>
              <a href="#"><i class="icon-vimeo4"></i></a>
              <a href="#"><i class="icon-linkedin2"></i></a>                
          </div>
        <div class="clearfix"></div>
      </div>            
        
    </div> <!-- #SIDEBAR -->      

    <div class="clearfix"></div>        
  </section>

  <PostDtetails v-else :postId ="postDetailsId"></PostDtetails>
    
  <footer class="tada-container">
      
      <!-- FOOTER BOTTOM -->
      
      <div class="footer-bottom">
        <span class="copyright">Copyright © 2016. All Rights Reserved</span>
        <span class="backtotop">TOP <i class="icon-arrow-up7"></i></span>
          <div class="clearfix"></div>
      </div>
      
  </footer>

</template>

<style scoped>
.menu-active-class{
	background:#9c8156;
  color: #fff;
}
</style>
