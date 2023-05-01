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
  <van-tabs @click="onClick">
    <van-tab v-for="(kind,index) in kindList" :title="kind.content" :key="index">
  </van-tab>
    </van-tabs>
    </div>
  <div class="list kind">
    <div class="card" v-for="item in list" :key="item.newsid" @click="getDetail(item.newsid)">
  <div class="card-left">
  <div class="title">{{item.title}}
    </div>
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
    </div>
  </div>
  </template>


<script>
export default{
  name: 'Kind',
  data() {
    return{
      picIp:'http://192.168.56.1:8080/pic/',
      value:'',
      kindList:[],
      list:[]
    }
  },
  created(){
    this.axios.get("kind/list").then((response)=>{
      this.kindList=response.data;
    })
    this.onClick(0,'');
  },
  methods:{
    onClick(name,title){
      console.log(name+','+title);
      this.axios.get("news/getBykindid?kindId="+(name+1)).then((response)=>{
        this.list=response.data;
      })
    },
    goSearch(){
      this.$router.push("/Search");
    },
    getDetail(newsid){
      this.$router.push("/Detail/"+newsid)
    },
   }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


</style>
