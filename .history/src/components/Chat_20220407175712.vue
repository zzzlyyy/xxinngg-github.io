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
    getByAuthor(){
      this.$router.push({
        name:'AuthorInfo',query:{
          authorId:this.news.uid,
          userName:this.news.userName,
          image:this.news.image,
          followLabel:this.followLabel
        }
      })
    },
    isFollow(){
      if(this.user==null){
        return;
      }
      var userId=this.user.uid;
      var followUserId=this.news.uid;
      this.axios.get("followUser/isFollowUser?userId="+userId+"&followUserId="+followUserId).then((response)=>{
        var result =response.data;
        if(result==true){
          this.followLabel='已关注'
        }
      })
    },
    follow(){
      if(this.user==null){
        Toast("您未登录，请先登录再关注作者！");
        return;
      }
      var userId=this.user.uid;
      var followUserId=this.news.uid;
      if (this.followLabel=='已关注') {
        this.axios.get("followUser/disFollowUser?userId="+userId+"&followUserId="+followUserId).then((response)=>{
        var result =response.data;
          this.followLabel='关注'
      })
      }else{
        this.axios.get("followUser/followUser?userId="+userId+"&followUserId="+followUserId).then((response)=>{
        var result =response.data;
          this.followLabel='已关注'
      })
      }
      
    },
    download(){
      this.axios.get("news/download?filename="+this.news.video,{responseType:'blob'}).then((response)=>{
        let url=window.URL.createObjectURL(new Blob([response.data]));
        let link=document.createElement("a");
        link.style.display='none';
        link.href=url;
        const fileName=this.news.video;
        link.setAttribute("download",fileName);
        document.body.appendChild(link);
        link.click();
        window.URL.revokeObjectURL(url);
        document.body.removeChild(link);
        Toast("下载成功");
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
    getCommentList(){
      this.axios.get("comment/getByNewsid?newsId="+this.newsId).then((response)=>{
        this.commentList=response.data;
      })
    },
    onClickLeft(){
      this.$router.go(-1);
    },
    preview(){
      var arr=[];
      this.news.picList.forEach(item => {
        arr.push(this.picIp+item.pic)
      })
      ImagePreview(arr);
    }
  },
}
</script>