<template>
  <div id="login">
        <div class="nav_bg">
            <div class="bor">
                <div class="wel">用户登录</div>
                <form id="login_form" method="POST">
                    <div class="Login">
                        <img v-bind:src="yonghu"/>
                        <p><input type="text" id="userName"  placeholder="用户名" ref="userName"></p>
                    </div>
                    <div class="pass">
                        <img v-bind:src="pass"/>
                        <p><input type="password" id="passWord"  placeholder="请输入密码" ref="passWord"></p>
                    </div>
                    <div></div>
                    <p><input class="sure" type="button" id="login" @click="loginSubmit($event)"  value="登录"/></p>
<!-- {{this.$store.state.userState}}
        {{this.$store.state.userInfo}} -->
                </form>
                <dl class="sign">
                    <dt><a href="retrievePassword.html">忘记密码？</a></dt>
                    <dd>新用户请先<a href="index.html">注册</a></dd>
                </dl>
                <div class="clearfix"></div>
                <div class="rem"><input class="kuai" type="checkbox" value="1"/>
                    <p>记住密码</p></div>
                <div class="text-center">
                    <span id="error" style="color:red"></span>
                    <span id="errorpass" style="color:red"></span>
                </div>
            </div>
            <div class="jpg"><img v-bind:src="beijing"/></div>

             <div id="box">
    </div>
        </div>
        
    </div>
    
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
        yonghu:'./static/assets/img/yonghu.png',
        pass:'./static/assets/img/pass.png',
        beijing:'./static/assets/img/beijing.png'
    };
  },
  methods: {
    loginSubmit(ev) {
      //获取登录信息
      let _this = this;
      var userName = _this.$refs.userName.value;
      var passWord = _this.$refs.passWord.value;
      if(!userName || !passWord){
           $("#error").text("用户或者密码不能为空");
      }else{
          let params = new URLSearchParams();
          params.append("webUserName", userName),
          params.append("webUserPassword", passWord),

          _this.$axios.post('/webUser/loginWeb',params)
                     .then(response =>{
                         console.log(response.data);
                         var user=response.data;
                         if(user.status=="1000"){
                             //将登录信息存放到，store中，进行管理
                             _this.$store.commit('SAVE_USERINFO',user)
                             _this.$store.commit('SAVE_USERSTATE',"true")
                             _this.$store.commit('SAVE_REFRESH',true)
                             _this.$router.replace('/home')
                         }else if(user.status=="1001"){
                              //$("#error").text("用户或密码不正确")
                            layer.alert(user.errorMessage);
                         }else{
                              $("#error").text("用户或密码不正确")
                         }
                     })
               }
       }
    },
    created(){
        var lett = this;
            document.onkeydown = function(e) {
            var key = window.event.keyCode;
            if (key == 13) {
            lett.loginSubmit();
            }
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import "./common/css/loginForm";
</style>