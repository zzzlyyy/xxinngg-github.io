<template>
    <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回
    </div>
    <div class="top">
        <div class="top-left">
          <van-image :src="picIp+author.image" lazy-load/>
          <div class="user">{{author.userName}}</div>
        </div>
        <div class="top-right">
          <van-button color="#ee0a24" plain @click="follow">{{followLabel}}</van-button>
          <van-button v-if="followLabel=='已关注'" color="#ee0a24"
          danger @click="chat">私聊</van-button>
        </div>
    </div>
    <van-tabs v-model="active">
       <van-tab title="全部"></van-tab>
       <van-tab title="文章"></van-tab>
       <van-tab title="视频"></van-tab>
       <van-tab title="问答"></van-tab>
    </van-tabs>
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
    name:'AuthorInfo',
    data(){
        return {
            picIp:'http://localhost:8080/pic/',
            author:this.$route.query,
            list:[],
            loading:false,
            finished:false,
            refreshing:false,
            page:1,
            user:JSON.parse(localStorage.getItem("userInfo")),
            active:1,
            followLabel:'关注'
        }
    },

    methods:{
        chat(){
            this.$router.push({
                name:'Chat',query:{
                    authorId:this.author.uid,
                    userName:this.author.userName,
                    image:this.author.image
                }
            })
        },
        follow(){
            if(this.user==null){
                Toast("您未登录，请先登录再关注作者！");
                return;
            }
            var userId=this.user.uid;
            var followUserId=this.author.authorId;
            if (this.author.followLabel=='已关注') {
                this.axios.get("followUser/disFollowUser?userId="+userId+"&followUserId="+followUserId).then((response)=>{
                var result =response.data;
                this.author.followLabel='关注'
            })
            }else{
                this.axios.get("followUser/followUser?userId="+userId+"&followUserId="+followUserId).then((response)=>{
                var result =response.data;
                this.author.followLabel='已关注'
            })
            }
        },
        onLoad(){
            if(this.refreshing){
                this.list=[];
                this.refreshing=false;
            }
            this.axios.get("news/getByAuthorId?current="+this.page+"&uid="+this.author.authorId)
            .then((response)=>{
                this.list=this.list.concat(response.data.records);
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
        onClickLeft(){
            this.$router.go(-1);
        },
    },



}
</script>
<style scoped>

</style>