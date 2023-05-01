<template>
  <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回
    </div>
    <div class="top">
        <div class="top-left">
            <van-image :src="picIp+user.image" alt=""/>
            <div class="user">
                {{user.username}}
            </div>
        </div>
        <div class="top-right">
            <div class="geren">
                个人主页<van-icon name="arrow"/>
            </div>
        </div>
    </div>
    <van-tabs v-model="active">
        <van-tab title="收藏"></van-tab>
        <van-tab title="评论">
            <div class="list">
                <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
                    <van-list
                    v-model="loading"
                    :finished="finished"
                    finished-text="没有更多了"
                    @load="onLoad"
                    >
                        <div class="comment-card" v-for="(item,index) in list" :key="item.commentid">
                            <van-swipe-cell>
                                <div class="comment-title">{{item.content}}</div>
                                <div class="comment-title">{{item.time|moment}}</div>
                                <div class="news-card" @click="getDetail(item.news.newsid)">
                                    
                                    <div class="top">
                                        <div class="top-left">
                                            <van-image :src="picIp+item.news.image"/>
                                            <div class="user">
                                                {{item.news.username}}
                                            </div>
                                        </div>
                                        <div class="top-right">
                                        </div>
                                    </div>
                                    <div class="card">
                                        <div class="card-left">
                                            <div class="title">{{item.news.title}}</div>
                                            <div class="time">
                                                <span>{{item.news.time | moment}}</span>
                                                <span>{{item.news.source}}</span>
                                                <span>10评论</span>
                                            </div>
                                        </div>
                                        <div class="card-right">
                                            <img :src="picIp+item.news.pictures" alt="">
                                        </div>
                                    </div>
                                    <div class="share">
                                        <van-row>
                                            <van-col span="8">
                                                <van-icon name="share-o"/>分享
                                            </van-col>
                                            <van-col span="8">
                                                <van-icon name="chat-o"/>评论
                                            </van-col>
                                            <van-col span="8">
                                                <van-icon name="good-job-o"/>赞
                                            </van-col>
                                        </van-row>
                                    </div>
                                </div>
                                <template #right>
                                    <van-button square type="danger" text="删除" class="delete-button" @click="removeComment(index,item.commentid)"/>
                                </template>
                            </van-swipe-cell>
                        </div>
                    </van-list>
                </van-pull-refresh>
            </div>
        </van-tab>
        <van-tab title="点赞"></van-tab>
        <van-tab title="历史"></van-tab>
    </van-tabs>
  </div>
</template>

<script>
import Vue from 'vue';
import { Toast } from 'vant';

Vue.use(Toast);
export default{
  name: 'Comments',
  data() {
    return{
      show:true,
      picIp: 'http://localhost:8000/pic/',
      active:1,
      list:[],
      loading:false,
      finished:false,
      refreshing:false,
      page:1,
      user:JSON.parse(localStorage.getItem("userInfo"))
    }
  },
  created(){
  },
  methods:{
    removeComment(index,commentid){
        this.axios.post("comment/remove",{
            commentid:commentid,
        }).then((response)=>{
            this.list.splice(index,1);
            Toast.success("删除评论成功!");
        })
    },
    onClickLeft(){
        this.$router.go(-1);
    },
    getDetail(newsid){
        this.$router.push("/Detail/"+newsid)
    },
    onLoad(){
        if(this.user==null){
            Toast("您未登录!请先登录后再查看我的评论!");
            this.$router.push("/Mine");
            return;
        }
      if(this.refreshing){
        this.list=[];
        this.refreshing=false;
      }
      this.axios.post("comment/getByUid",{
          uid:this.user.uid,
          page:this.page,
      }).then((response)=>{
        this.list=this.list.concat(response.data.list);
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
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.comment-title{
  text-align: left;
  margin: 15px 10px;
}
.comment-card{
    background-color: #fff;
}
.news-card .top .top-left img{
    height: 25px;
    width: 25px;
}
.news-card{
    border: 0.8px solid #eee;
    margin: 15px 10px;
}
.share{
    margin: 15px 10px;
}
.delete-button{
    height: 100%;
}
</style>
