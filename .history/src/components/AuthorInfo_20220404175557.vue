<template>
    <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回
    </div>
    <div class="top">
        <div class="top-left">
          <van-image :src="picIp+news.image" lazy-load/>
          <div class="user">{{news.userName}}</div>
        </div>
        <div class="top-right">
          <van-button color="#ee0a24" plain @click="follow">{{author.followLabel}}</van-button>
          <van-button v-if="author.followLablel=='已关注'" color="#ee0a24"
          danger @click="chat">私聊</van-button>
        </div>
    </div>
    <van-tabs v-model="active">
       <van-tab title="全部"></van-tab>
       <van-tab title="文章"></van-tab>
       <van-tab title="视频"></van-tab>
       <van-tab title="问答"></van-tab>
    <div class="list">
        <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
            <van-list
                v-model="loading"
                :finished="finished"
                finished-text="没有更多了"
                @load="onLoad"
                >
                <div class="card" v-for="item in list" :key="item.newsid" @click="getDetail(item.newsid)">
                    <div class="card-left">
                        <div class="title">
                            {{item.title}}
                        </div>
                        <div class="time">
                            <span>{{item.time | moment}}</span>
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
export default {
    name:'SearchResult',
    data(){
        return {
            picIp:'http://localhost:8080/pic/',
            value:this.$route.params.value,
            list:[],
            loading:false,
            finished:false,
            refreshing:false,
            page:1
        }
    },

    methods:{
        goSearch(){
            this.$router.push("/Search");
        },
        onLoad(){
            if(this.refreshing){
                this.list=[];
                this.refreshing=false;
            }
            this.axios.post("news/getByTitle",{
                page:this.page,
                title:this.value
            }).then((response)=>{
                this.list=this.list.concat(response.data.list);
                this.loading=false;
                if(this.page>=response.data.pages){
                    this.finished=true;//最后一页
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
        getDetail(newsid){
            this.$router.push("/Detail/"+newsid)
        },
    },



}
</script>
<style scoped>
.list{
    margin-top: 50px;
}
</style>