<template>
    <div class="hello">
    <div class="fixed">
      <van-search
      v-model="value"
      shape="round"
      background="#ee0a24"
      placeholder="请输入搜索关键词"
      @click="goSearch"
      />
    </div>
    
    <div class="list">
        <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
            <van-list
                v-model="loading"
                :finished="finished"
                finished-text="没有更多了"
                @load="onLoad"
                >
                <div class="card" v-for="item in list" :key="item.newsId" @click="getDetail(item.newsId)">
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
    name:'SearchResult',
    data(){
        return {
            picIp:'http://192.168.56.1:8080/pic/',
            value:this.$route.params.value,
            list:[],
            loading:false,
            finished:false,
            refreshing:false,
            page:1
        }
    },

    methods:{
        goSearch(){
            this.$router.push("/Search");
        },
        onLoad(){
            if(this.refreshing){
                this.list=[];
                this.refreshing=false;
            }
            this.axios.post("news/getByTitle",{
                page:this.page,
                title:this.value
            }).then((response)=>{
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
        getDetail(newsId){
            this.$router.push("/Detail/"+newsId)
        },
    },



}
</script>
<style scoped>
.list{
    margin-top: 50px;
}
</style>