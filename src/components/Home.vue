<template>
  <div class="hello">
    <div class="fixed">
  <van-search
  v-model="value"
  shape="round"
  background="#ee0a24"
  placeholder="请输入搜索关键词"
  @click="goSearch"
  />
    </div>
  <van-swipe :autoplay="3000">
    <van-swipe-item v-for="(image, index) in images" :key="index">
      <img v-lazy="picIp+image.pictures" />
      </van-swipe-item>
      </van-swipe>
      <div class="list">
  <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
  <div class="card" v-for="item in list" :key="item.newsId" @click="getDetail(item.newsId)">
  <div class="card-left">
    <div class="title">{{item.title}}</div>
    <div class="time">
      <span>{{item.time| moment}}</span>
      <span>{{item.source}}</span>
      <span>10评论</span>
    </div>
  </div>
  <div class="card-right">
    <img :src="picIp+item.pictures" alt="">
  </div>
  </div>
  </van-list>
  </van-pull-refresh>
</div>
  </div>

</template>

<script>
import Vue from 'vue';
import { Lazyload } from 'vant';

Vue.use(Lazyload);
export default{
  name: 'Home',
  data() {
    return{
      value: '',
      picIp: 'http://localhost:8000/pic/',
      images:[],
      list:[],
      loading:false,
      finished:false,
      refreshing:false,
      page:1
    }
  },
  created(){
    this.getSwipeList();
  },
  methods:{
    getSwipeList(){
      this.axios.get("news/getSwipe").then((response) => {
        //console.log(response.data)
        this.images=response.data.records;
      })
    },
    goSearch(){
      this.$router.push("/Search");
      },
    onLoad(){
      if(this.refreshing){
        this.list=[];
        this.refreshing=false;
      }
      this.axios.get("news/getPage?current="+this.page).then((response)=>{
        this.list=this.list.concat(response.data.records);
        this.loading=false;
        if(this.page>=response.data.pages){
          this.finished=true;
        }
        this.page++;
      })
    },
    onRefresh(){
      this.finished=false;
      this.loading=true;
      this.page=1;
      this.onLoad();
    },
    getDetail(newsId){
      this.$router.push("/Detail/"+newsId)
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.van-swipe{
  height:250px;
  margin-top: 50px;
}
.van-swipe img{
  height:100%;
  width: 100%;
}
.list{
background-color: #eee;
}
.card{
display :flex;
margin: 5px 1px;
background-color:#fff;
}
.card-left{
flex: 8;
text-align: left;
padding: 5px;
}
.card-left .title{
font-size: 17px;
color:#111;
}
.card-left .time{
font-size: 12px;
color:#aaa;
margin-top: 50px;
}
.card-left .time span{
margin: 0 2px;
}
.card-right{
flex:2;
}
.card-right img{
height:90px;
width:120px;
}

</style>
