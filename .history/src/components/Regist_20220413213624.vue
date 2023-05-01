<template>
  <div class="hello">
    <div class="nav">
      <van-icon name="arrow-left" @click="onClickLeft">返回</van-icon>
    </div>
    <h1>注册</h1>
<van-form @submit="onSubmit" class="form">
<van-field
  v-model="user.username"
  name="username"
  label="用户名"
  placeholder="用户名"
  @blur="checkUsername"
  :rules="[{ pattern,required: true, message:'用户名必须是6到12位'}]"
/>
<van-field
v-model="user.password"
  type="password"
  name="password"
  label="密码"
  placeholder="密码"
  :rules="[{ pattern,required: true, message:'密码必须是6到12位'}]"
  />
<van-field
v-model="user.password1"
  type="password"
  name="password1"
  label="确认密码"
  placeholder="确认密码"
  :rules="[{ pattern,required: true, message:'请填写确认密码'}]"
/>
<van-field
v-model="user.email"
  name="email"
  label="邮箱"
  placeholder="邮箱"
  :rules="[{ validator,required: true, message:'请填写正确的邮箱格式'}]"
/>
<van-field
  v-model="user.checkcode"
  name="checkcode"
  label="验证码"
  placeholder="验证码"
>
  <template #button>
    <van-button size="small" type="info" @click="sendMail">发送验证码</van-button>
  </template>
</van-field>
<van-field name="uploader" label="头像">
  <template #input>
    <van-uploader v-model="uploader" :before-read="beforeRead" />
  </template>
</van-field>
  <div style="margin: 16px;">
  <van-button round block type="warning" native-type="submit">提交</van-button>
  </div>
</van-form>
  </div>
</template>

<script>
import Vue from 'vue';
import { Toast } from 'vant';

Vue.use(Toast);
export default{
  name: 'Regist',
  data() {
    return{
      show: true,
      picIp:'http://localhost:8080/pic/',
      user:{
        username:'',
        password:'',
        password1:'',
        email:'',
        checkcode:''
      },
      pattern:/^.{6,20}$/,
      emailPattern:/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/,
      uploader:[],
    }
  },
  created(){
    
  },
  methods:{
    sendMail(){
      if(this.user.email.trim()==''){
        Toast("请输入邮箱！");
        return;
      }
    },
    validator(val){
      return /\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/.test(val);
    },
    checkUsername(){
      if(this.user.username.trim()==''){
        Toast("请输入用户名！");
        return;
      }
      this.axios.post("user/checkUsername",{
        username:this.user.username
      }).then(response=>{
        if (response.data=="exist") {
          Toast("您的用户名与其他用户一样，不能使用！");
          this.user.username=''
        } else {
          Toast("您的用户名可以使用!");
        }
      });
    },
    onSubmit(values) {
      if(this.user.password!=this.user.password1){
        Toast("密码与确认密码不一致!");
        return;
      }
      console.log(values);
      var form=document.querySelector(".form");
      var formData=new FormData(form);
      var upload=this.uploader[0];
      if (upload==undefined) {
        Toast("您未上传头像！");
        return;
      }
       formData.append("imgFile",this.uploader[0].file);
      this.axios.post("user/regist",formData,{
      "content-type":'multipart/form-data'
      }).then(response=>{
        if (response.data=="failed") {
          Toast("注册失败，请确认信息是否填写完整！");
        } else {
          Toast("注册成功！");
          this.$router.push("/Mine");
        }
      });
    },
      beforeRead(file){
        if (file.type !== 'image/jpeg'){
        Toast("请上传jpg格式图片");
        return false;
      }
        return true;
    },
      onClickLeft(){
        this.$router.go(-1);
    },
},
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  padding: 2px;
}

</style>
