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
        if (this.user==null) {
            Toast("您未登录！");
            return;
        }
        this.onClick(1,'');
    },
    methods:{
        chat(item){
            this.$router.push({
                name:'Chat',query:{
                    authorId:item.uid,
                    userName:item.username,
                    image:item.image
                }
            })
        },
        getInfo(item){
            this.$router.push({
                name:'AuthorInfo',query:{
                    authorId:item.uid,
                    userName:item.username,
                    image:item.image
                }
            })
        },
        onClick(name,title){
            console.log(name+','+title);
            var url="";
            if (name==0) {
                url="followUser/getUsersByFollowUid?followUserId="+this.user.uid;
            } else{
                url="followUser/getFollowUsersByUid?userId="+this.user.uid;
            }
            this.axios.get(url).then((response)=>{
                this.list=response.data;
            })
        },
        onClickLeft(){
            this.$router.go(-1);
        },
    },



}
</script>
<style scoped>

</style>