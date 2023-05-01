<template>
  <div>
    <div class="fixed">
  <van-search
  v-model="value"
  shape="round"
  background="#ee0a24"
  placeholder="请输入搜索关键词"
  @search="onSearch"
  />
    </div>
  <van-divider />
  <div class="search">
    <div>
      搜索历史
    </div>
    <div>
      <van-icon name="delete-o" @click="clearHistory"/>
    </div>
  </div>
    <van-row>
      <van-col span="12" v-for="(item,index) in list" :key="index" @click="goSearch(item)">
        {{item}}
      </van-col>
    </van-row>
  <van-divider/>
  </div>
</template>

<script>
export default{
  name: 'Search',
  data() {
    return{
      value: '',
      list:[],
    }
  },
  created(){
    var history=localStorage.getItem("history");
    if(history!=null){
      this.list=history.split(",");
    }
  },
  methods:{
    goSearch(item){
      this.$router.push("/SearchResult/"+item)
      },
    onSearch(val){
      var newArr=this.list.filter(item=>item!=val)
      this.list=newArr;
      this.list.unshift(val);
      localStorage.setItem("history",this.list.toString());
      this.goSearch(val);
    },
    clearHistory(){
      localStorage.removeItem("history");
      this.list=[];
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.search{
  display: flex;
  justify-content: space-between;
  padding: 5px;
  height: 30px;
  line-height: 30px;
}
.van-divider{
  margin-top: 65px;
}
.van-col{
  text-align: left;
  border-right: 0.5px solid #eee;
  padding: 5px;
}

</style>
