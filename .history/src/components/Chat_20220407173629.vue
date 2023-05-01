<template>
  <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回  <span>{{author.userName}}</span>
    </div>
    <div class="news">
      <div class="n-title">
        {{news.title}}
      </div>
      <div class="top">
        <div class="top-left" @click="getByAuthor">
          <van-image :src="picIp+news.image" lazy-load/>
          <div class="user">{{news.userName}}</div>
        </div>
        <div class="top-right">
          <van-button color="#ee0a24" plain @click="follow">{{followLabel}}</van-button>
        </div>
      </div>

      <div class="n-time">
        {{news.time|moment}}
      </div>
      <div class="n-img" v-if="news.type==1">
        <img :src="picIp+news.pictures" alt="">
      </div>
      <div v-if="news.type==2" class="n-img1" @click="preview">
        <van-row gutter="1">
          <van-col span="8" v-for="(item,index) in news.picList" :key="index">
            <img :src="picIp+item.pic" alt="">
          </van-col>
        </van-row>
      </div>
      <div class="n-img" v-if="news.type==3">
        <video :src="picIp+news.video" alt="" controls></video>
        <div @click="download">
          <van-icon name="down" size="24"/>下载视频
        </div>
      </div>
      <div class="n-content">
        {{news.content}}
        <h3>评论</h3>
        <div class="comment" v-for="item in commentList" :key="item.commentId">
          <div class="comment-left">
            <img :src="picIp+item.image">
          </div>
          <div class="comment-right">
            <div class="comment-user">
              <div class="user-left">
                {{item.userName}}
              </div>
              <div class="user-right"> 
                <span>674</span>
                <van-icon name="good-job-o" size="18"/>
              </div>
            </div>
            <div class="comment-content">
              {{item.content}}
            </div>
            <div class="comment-time">
              {{item.time|moment}}
            </div>
          </div>
        </div>
        <div class="comment-input">
          <van-field
            v-model="content"
            clearable
            placeholder="请输入评论内容"
            @keyup.enter="sendComment"
          >
            <template #button>
              <van-button size="small" type="warning" @click="sendComment">发送</van-button>
            </template>
          </van-field>
        </div>
      </div>
    </div>
  </div>
</template>