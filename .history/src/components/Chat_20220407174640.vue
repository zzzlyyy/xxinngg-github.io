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