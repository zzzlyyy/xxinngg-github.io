<template>
  <div id="app">
    <router-view/>
      <van-tabbar v-model="active" active-color="#ee0a24" inactive-color="#000">
       <van-tabbar-item icon="wap-home" to="/">首页</van-tabbar-item>
       <van-tabbar-item icon="video" to="/Video">视频</van-tabbar-item>
       <van-tabbar-item icon="coupon" to="/Kind">分类</van-tabbar-item>
       <van-tabbar-item icon="manager" to="/Mine">我的</van-tabbar-item>
      </van-tabbar>
  </div>
</template>

<script>
export default {
  name: 'App',

 data(){
  return {
    active:0,
  };
},
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
