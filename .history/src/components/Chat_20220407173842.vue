<template>
  <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft"/>返回  <span>{{author.userName}}</span>
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
</template>