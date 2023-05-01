<template>
    <div class="hello">
        <div class="nav">
            <van-icon name="arrow-left" @click="onClickLeft"/>返回  <span>{{author.userName}}</span>
        </div>
        <div class="list">
            <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
                <van-list
                    v-model="loading"
                    
                    @load="onLoad"
                    >
                    <div class="clist" v-for="(item,index) in list" :key="index">
                        <div class="time">
                            {{item.data|moment}}
                        </div>
                        <div class="chat" :class="{self:item.fromUser==user.username}">
                           <van-image :src="item.fromUser==user.username?picIp+user.image:picIp+author.image" 
                           lazy-load height="60" width="60" round/>
                           <div class="content">{{item.content}}</div>
                        </div>
                    </div>
                </van-list>
            </van-pull-refresh>
        </div>
            <div class="comment-input">
            <van-field
                v-model="content"
                clearable
                placeholder="请输入聊天内容"
                @keyup.enter="send"
            >
                <template #button>
                <van-button size="small" type="warning" @click="send">发送</van-button>
                </template>
            </van-field>
            </div>
    </div>
</template>
<script>
import Vue from 'vue';
import { Toast } from 'vant';
Vue.use(Toast);
import SockJS from 'sockjs-client'
import Stomp from 'stompjs'
export default{
  name: 'Chat',
  data() {
    return{
      picIp:'http://localhost:8080/pic/',
      author:this.$route.query,
      list: [],
      content:"",
      loading:false,
      finished:false,
      refreshing:false,
      page:1,
      active:1,
      user:JSON.parse(localStorage.getItem("userInfo")),
      value:'',
      stompClient:null
    }
  },
  created(){
    this.connect();
  },
  methods:{
    connect(){
        var socket = new SockJS('http://127.0.0.1:8080/ws/ep');
        this.stompClient = Stomp.over(socket);
        this.stompClient.connect({},success=>{
            this.stompClient.subscribe('/user/'+this.user.username+'/queue/chat',response=>{
                var receiveMsg = JSON.parse(response.body);
                console.log(receiveMsg);
                receiveMsg.toUser=receiveMsg.fromUser;
                this.list.push(receiveMsg);
            });
        });
    },
    send(){
      if(this.user==null){
        Toast("您未登录！请先登陆后再聊天！");
        this.$router.push("/Mine");
        return;
      }
      if(this.content.trim()==""){
        Toast("您未输入聊天内容！请输入内容！");
        return;
      }
      var msg={
          fromUser:this.user.username,
          content:this.content,
          toUser:this.author.userName,
          date:new Date()
      };
      this.list.push(msg);
      this.stompClient.send("/chat",{},JSON.stringify(msg));
      this.content=""
    },
    onLoad(){
        if(this.user==null){
        Toast("您未登录！请先登陆后再聊天！");
        return;
      }
        if(this.refreshing){
            this.list=[];
            this.refreshing=false;
        }
        this.axios.get("chatmsg/list?current="+this.page+"&fromUser="+this.user.username+"&toUser="+this.author.userName)
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
    onClickLeft(){
        this.$router.go(-1);
    },
  },
}
</script>
<style scoped>
.clist{
    margin-bottom: 88px;
}
.clist .time{
    margin: 10px 0;
}
.clist .chat{
    display: flex;
    padding: 0 10px;
}
.chat .content{
    height: 50px;
    line-height: 50px;
    margin: 0 5px;
    border: 0.5px solid #ddd;
    padding: 0 10px;
    background-color: #eee;
    border-radius: 5px;
    font-size: 17px;
}
.self{
    display: flex;
    flex-direction: row-reverse;
}
.self .content{
    background-color: rgb(143,168,223);
}
.nav{
    position: fixed;
    top: 0;
}
.van-list{
    margin-top: 50px;
}
</style>