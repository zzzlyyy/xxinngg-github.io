<template>
    <div class="hello">
        <div class="nav">
        <van-icon name="arrow-left" @click="onClickLeft"/>返回  <span>{{author.userName}}</span>
        </div>
        <div class="list">
            <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
                <van-list
                    v-model="loading"
                    :finished="finished"
                    finished-text="没有更多了"
                    @load="onLoad"
                    >
                    <div class="clist" v-for="(item,index) in list" :key="index">
                        <div class="time">
                            {{item.data|moment}}
                        </div>
                        <div class="chat" :class="{self:item.fromUser==username}">
                           <van-image :src="item.fromUser==user.username?picIp+user.image:picIp+author.image" lazy-load height="60" width="60" round/>
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
      list:[],
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
    this.content();
  },
  methods:{
    content(){
        var socket = new SockJS('http://127.0.0.1:8080/ws/ep');
        this.stompClient = Stomp.over(socket);
        this/this.stompClient.content({},success=>{
            this.stompClient.subscribe('/user/'+this.user.username+'/queue/chat/',response=>{
                var receiveMsg = JSON.parse(response.body);
                console.log(receiveMsg);
                receiveMsg.toUser=receiveMsg.fromUser;
                this.list.push(receiveMsg);
            })
        })
    },
    sendComment(){
      if(this.user==null){
        Toast("您未登录！请先登陆后再来发表评论！");
        this.$router.push("/Mine");
        return;
      }
      if(this.content.trim()==""){
        Toast("您未输入评论内容！请输入评论内容后再发表评论！");
        return;
      }
      this.axios.post("comment/add",{
        uid:this.user.uid,
        content:this.content,
        newsid:this.newsId
      }).then(response=>{
        this.content="";
        this.getCommentList();
      });
    },
    
  },
}
</script>