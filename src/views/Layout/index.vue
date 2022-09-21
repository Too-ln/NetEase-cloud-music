<template>
  <div>
    <van-nav-bar 
    :title="activeTitle" 
    fixed
    />
    <div class="main"> 
      <router-view></router-view>
    </div>
    <van-tabbar v-model="active" route>
      <van-tabbar-item replace to="/layout/home" icon="home-o">首页</van-tabbar-item>
      <van-tabbar-item replace to="/layout/search" icon="search">搜索</van-tabbar-item>
    </van-tabbar>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  data() {
    return {
      activeTitle:this.$route.meta.title,
    }
  },
  watch:{
    $route(){
      this.activeTitle=this.$route.meta.title
    }
  },
  setup() {
    const active = ref(0);
    return { active };
  },
  
}
</script>

<style scoped>
  .main{
    padding-top:46px;
    padding-bottom: 50px;
  }
</style>

