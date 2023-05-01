<template>
    <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回
    </div>
    <div class="top">
        <div class="top-left">
          <van-image :src="picIp+user.image" lazy-load round/>
          <div class="user">{{user.username}}</div>
        </div>
        <div class="top-right">
        </div>
    </div>
    <van-tabs v-model="active" @click="onClick">
       <van-tab title="关注我的"></van-tab>
       <van-tab title="我关注的"></van-tab>
    </van-tabs>
    <div class="list">
        <div class="users" v-for="item in list" :key="item.uid">
            <div class="users-left">
                <van-image :src="picIp+item.image" lazy-load round height="60" width="60"/>
                <div class="name">{{item.username}}</div>
            </div>
            <div class="users-right">
                <van-button type="danger" @click="chat(item)" size="small">私聊</van-button>
                <van-button type="info" @click="getInfo(item)" size="small">详情</van-button>
            </div>
        </div>
    </div>

    </div>
</template>


<script>
import Vue from 'vue';
import {Toast} from 'vant';
Vue.use(Toast);
export default {
    name:'AuthorInfo',
    data(){
        return {
            picIp:'http://localhost:8080/pic/',
            list:[],
            user:JSON.parse(localStorage.getItem("userInfo")),
            active:1,
        }
    },
    created(){
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