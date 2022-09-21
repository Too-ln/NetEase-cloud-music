<template>
  <div>
    <van-search shape="round" v-model="value" @input="inputFn" placeholder="请输入搜索关键词" />
    <div class="search_wrap" v-if="resultArr.length===0">
      <p class="hot_title">热门搜索</p>
      <div class="hot_name_wrap">
        <span class="hot_item" v-for="(obj,index) in hotArr" :key="index" @click="fn(obj.first)">{{obj.first}}</span>
      </div>
    </div>
    <div class="search_wrap" v-else>
      <p class="hot_title">最佳匹配</p>
      <van-list
        v-model="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
    <SongItem v-for="resultObj in resultArr" :key="resultObj.id" :name="resultObj.name" :author="resultObj.ar[0].name+' - '+resultObj.name" :id="resultObj.id"> </SongItem>>
      </van-list>
    </div>
  </div>

</template>

<script>
import { ref } from 'vue';
import {hotSearchAPI,searchResultAPI} from '@/api'
import SongItem from '@/components/SongItem.vue'

export default {
  data() {
    return {
      value:'',
      hotArr:[],
      resultArr:[],
      loading:false,
      finished:false,
      page:1,
      timer:null
    }
  },
  setup() {
    const value = ref('');
    return { value };
  },
  async mounted() {
    const hotSearch=await hotSearchAPI()
    this.hotArr=hotSearch.data.result.hots
  },
  methods:{
    async getListFn(){
      return await searchResultAPI({
        keywords:this.value,
        limit:20,
        offset:(this.page-1)*20
      })
    },

    async fn(val){
      this.page=1
      this.finished=false
      this.value=val
      const searchResult= await this.getListFn() 
      this.resultArr=searchResult.data.result.songs
      this.loading =false
    },

    async inputFn(){
      this.timer && clearInterval(this.timer)
      this.timer=setInterval(async()=>{
        this.page=1
        this.finished=false
        this.loading =false
        if(this.value.length ===0){
          this.resultArr=[];
          return
        }
        const searchResult= await this.getListFn() 
        if(!searchResult.data.result.songs){
          this.resultArr=[];
          return
        }
        this.resultArr=searchResult.data.result.songs
        this.loading =false
      },500)
      
    },

    async onLoad(){
      this.page++;
      const searchResult= await this.getListFn() 
      if(!searchResult.data.result.songs){
        this.finished=true
        this.loading =false
        return
      }
      this.resultArr=[...this.resultArr,...searchResult.data.result.songs]
      this.loading =false
    }

  },
  components:{ SongItem }

}
</script>

<style scoped>
/* 搜索容器的样式 */
.search_wrap {
  padding: 0.266667rem;
}

/*热门搜索文字标题样式 */
.hot_title {
  font-size: 0.32rem;
  color: #666;
}

/* 热搜词_容器 */
.hot_name_wrap {
  margin: 0.266667rem 0;
}

/* 热搜词_样式 */
.hot_item {
  display: inline-block;
  height: 0.853333rem;
  margin-right: 0.213333rem;
  margin-bottom: 0.213333rem;
  padding: 0 0.373333rem;
  font-size: 0.373333rem;
  line-height: 0.853333rem;
  color: #333;
  border-color: #d3d4da;
  border-radius: 0.853333rem;
  border: 1px solid #d3d4da;
}
.van-cell{
  border-bottom: 1px solid lightgray;
}
</style>