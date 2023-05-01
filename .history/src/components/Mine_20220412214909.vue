<template>
  <div class="hello">
    <div class="head">
      <div class="top">
       <div class="top-left">
         <van-icon name="scan" size="20"/>
      </div>
      <div class="top-right">
        <van-icon name="envelop-o" size="20"/>
        <van-icon name="setting-o" size="20"/>
      </div>
    </div>
    <div class="top">
      <div class="top-left">
        <van-image :src="picIp+user.image" alt=""/>
        <div class="user">{{user.username}}</div>
      </div>
      <div class="top-right">
        <div class="geren">
          个人主页<van-icon name="arrow"/>
        </div>
      </div>
    </div>
    <van-row>
      <van-col span="6">
        <div class="digit">5</div>
        <div class="text">头条</div>
      </van-col>
      <van-col span="6">
        <div class="digit">100</div>
        <div class="text">获赞</div>
      </van-col>
      <van-col span="6">
        <div class="digit">10</div>
        <div class="text">粉丝</div>
      </van-col>
      <van-col span="6" @click="goFollow">
        <div class="digit">8</div>
        <div class="text">关注</div>
      </van-col>
    </van-row>
  </div>
    <div class="grid">
      <van-row>
      <van-col span="6">
        <div class="digit">
    <van-icon name="bullhorn-o" color="#ee0a24" size="24"/>
    </div>
      <div class="text">消息
      </div>
    </van-col>
<van-col span="6">
<div class="digit">
<van-icon name="star-o" color="#ee0a24" size="24"/>
</div>
<div class="text">收藏</div>
</van-col>
<van-col span="6">
<div class="digit">
<van-icon name="clock-o" color="#ee0a24" size="24"/>
</div>
<div class="text">浏览</div>
</van-col>
<van-col span="6">
<div class="digit">
<van-icon name="down" color="#ee0a24" size="24"/>
</div>
<div class="text">下载</div>
</van-col>
</van-row>
</div>
<div class="title">更多服务
</div>
<van-grid>
<van-grid-item icon="edit" text="用户反馈"/>
<van-grid-item icon="balance-pay" text="钱包"/>
<van-grid-item icon="send-gift-o" text="广告"/>
<van-grid-item icon="fire-o" text="免流量"/>
<van-grid-item icon="coupon-o" to="/Comments" text="评论"/>
<van-grid-item icon="good-job-o" text="点赞"/>
<van-grid-item icon="closed-eye" text="夜间模式"/>
<van-grid-item icon="bulb-o" text="创作中心"/>
<van-grid-item icon="notes-o" text="订单"/>
<van-grid-item icon="cart-circle-o" text="购物车"/>
<van-grid-item icon="bag-o" text="公益"/>
<van-grid-item icon="revoke" @click="logout" text="退出登录"/>
</van-grid>
<van-overlay :show="show" @click="show = false">
<div class="wrapper" @click.stop>
<div class="block">
<h1>登录</h1>
<van-form @submit="onSubmit">
<van-field
v-model="user.username"
  name="username"
  label="用户名"
  placeholder="用户名"
:rules="[{ required: true,message:'请填写用户名'}]"
/>
<van-field
v-model="user.password"
  type="password"
  name="password"
  label="密码"
  placeholder="密码"
:rules="[{ required: true,message:'请填写密码'}]"
/>
<div style="margin: 16px; ">
<van-button round block type="info" native-type="submit">提交</van-button>
</div>
</van-form>
<div class="reg" @click="goRegist">
  点击此处进行注册
</div>
</div>
</div>
</van-overlay>
</div>

</template>

<script>
import Vue from "vue";
import { Toast }from "vant";
Vue.use(Toast);
export default {
  name: 'Mine',
  data (){
  return {
  show: true,
  picIp: 'http://localhost:8080/pic/',
  user:{
  uid:0,
  username: '',
  password: '',
  image: 'a1.png'
  }
}
},
created(){
  var user=JSON.parse(localStorage.getItem("userInfo"));
  if(user!=null){
  this.user=user;
  this.show=false;
  }
},
methods :{
  goFollow(){
    this.$router.push("/Follow");
  },
onSubmit(values){
  console.log(values);
  this.axios.post("user/login",this.user).then(response=>{
  console.log(response.data);
  if(response.data==''){
  Toast("登录失败,请重新输入!");
  return;
}
Toast("登录成功");
 this.user=response.data;
 this.show=false;
 localStorage.setItem("userInfo",JSON.stringify(this.user));
});
},
goRegist(){
  this.$router.push("/Regist");
},
 logout(){
            localStorage.removeItem("userInfo");
            this.user={
                uid:0,
                username:'',
                password:'',
                image:'default.png'
            }
            this.show=true;
        }
},
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.wrapper {
display:flex;
align-items : center;
justify-content: center;
height: 100%;
}
.block {
width: 100%;
height: 300px;
background-color:#fff;
}
.reg{
text-align: right;
color: red;
margin: 0 10px;}
.hello{
padding: 2px;
}
.head{
background-color: #eee;
}

.digit{
margin: 5px 0;
}
.text{
  color: #777;
  font-size: 14px;
  margin: 5px 0;
}
.title{
  margin: 5px;
  text-align: left;
}
.geren{
  line-height: 60px;
  font-size: 14px;
  color: #777;
}
.grid{
  margin: 5px 0;
}
</style>
