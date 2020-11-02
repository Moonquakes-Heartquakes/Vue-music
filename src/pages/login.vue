<template>
  <div class="login">
    <div class="mask"></div>
    <div class="logo">
      <p>Music</p>
      <p>听你想听的</p>
    </div>
    <!--    登录  -->
    <div class="form-g" v-if="loginStatus">
      <div class="input-group">
        <input type="text" placeholder="手机号" v-model="phone" />
        <i class="icon">&#xe6fd;</i>
      </div>
      <div class="input-group">
        <input type="password" placeholder="密码" v-model="password" />
        <i class="icon">&#xe66f;</i>
      </div>
      <!--      <div class="forget-pwd">忘记密码?</div>-->
      <div class="forget-pwd" @click="loginStatus=false">注册</div>
      <div class="form-btn" @click="_login">登录</div>
    </div>
    <!--    注册  -->
    <div class="form-g" v-else>
      <div class="input-group">
        <input type="text" placeholder="昵称" v-model="userName" />
        <i class="icon">&#xe63c;</i>
      </div>
      <div class="input-group">
        <input type="text" placeholder="手机号" v-model="phone" />
        <i class="icon">&#xe6fd;</i>
      </div>
      <div class="input-group">
        <input style="width: 140px;" type="text" placeholder="验证码" v-model="code" />
        <!--        <i class="icon">&#xe6fd;</i>-->
        <button
          :disabled="buttStatus"
          style="height: 30px;margin-left: 10px;width: 75px"
          @click="getCod"
        >{{buttText}}</button>
      </div>
      <div class="input-group">
        <input type="password" placeholder="密码" v-model="password" />
        <i class="icon">&#xe66f;</i>
      </div>
      <div class="input-group">
        <input type="password" placeholder="确认密码" v-model="password2" />
        <i class="icon">&#xe66f;</i>
      </div>
      <div class="forget-pwd" @click="loginStatus=true">登录</div>
      <div class="form-btn" @click="register">注册</div>
    </div>
    <!--    <div class="other-login">-->
    <!--      <p>Or 使用以下账号登录</p>-->
    <!--      <div class="other-login-logo">-->
    <!--        <div class="wechat">-->
    <!--          <i class="icon">&#xe607;</i>-->
    <!--        </div>-->
    <!--        <div class="qq">-->
    <!--          <i class="icon">&#xe722;</i>-->
    <!--        </div>-->
    <!--        <div class="weibo">-->
    <!--          <i class="icon">&#xe602;</i>-->
    <!--        </div>-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
</template>

<script>
import api from "@/api";
export default {
  name: "login",
  data() {
    return {
      phone: "",
      password: "",
      password2: "",
      code: "",
      userName: "",
      buttText: "获取验证码",
      buttStatus: false,
      loginStatus: true,
      time: null
    };
  },
  methods: {
    // 登录
    _login() {
      if (!this.phone || !this.password) {
        this.$toast("请填写完整");
        return;
      }
      // TODO: 正则校验缺少
      let data = {
        phone: this.phone,
        password: this.password
      };
      // 请求登录
      api.Login(data).then(res => {
        if (res.code == 200) {
          sessionStorage.setItem("token", res.token);
          localStorage.setItem("userInfo", JSON.stringify(res.profile));
          this.$router.replace("/");
          window.location.reload(true);
        }
      });
    },
    // 获取验证码
    getCod() {
      // 判断定时器是否存在
      if (typeof this.time === "object") {
        this.buttStatus = true;
        var i = 60;
        // 获取验证码
        api.GetCode({ phone: this.phone }).then(res => {
          if (res.code === 200) {
            this.$toast("发送成功！");
          }
        });
        // 创建定时器60秒倒计时
        this.time = setInterval(() => {
          this.buttText = i;
          i--;
          // 清除定时器，还原button状态
          if (!i) {
            this.buttText = "获取验证码";
            this.buttStatus = false;
            clearInterval(this.time);
            this.time = null;
          }
        }, 1000);
      }
    },
    // 注册
    register() {
      // 验证是否输入内容
      if (
        !this.phone ||
        !this.password ||
        !this.password2 ||
        !this.code ||
        !this.userName
      ) {
        this.$toast("请填写完整");
        return;
      }
      // 验证密码是否一至
      if (this.password !== this.password2) {
        this.$toast("两次输入的密码不一至，请重新输入！");
        return;
      }
      let data = {
        phone: this.phone,
        password: this.password,
        captcha: this.code,
        nickname: this.userName
      };
      // 注册
      api.Register(data).then(res => {
        if (res.code === 200) {
          this.$toast("注册成功！");
        }
      });
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/css/function.scss";
.login {
  width: 100%;
  height: 100%;
  background: url("../assets/img/login-bg.jpg") no-repeat;
  background-size: cover;
  color: #fff;
  text-align: center;
  overflow: hidden;
  position: absolute;
  top: 0;
  .mask {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background: rgba(0, 0, 0, 0.5);
    z-index: 1;
  }
  .logo,
  .form-g,
  .other-login {
    position: relative;
    z-index: 2;
  }
  .logo {
    margin: px2rem(115px) 0 px2rem(75px);
    p:first-child {
      font-size: px2rem(68px);
    }
    p:last-child {
      font-size: px2rem(26px);
      color: rgba(255, 255, 255, 0.8);
    }
  }
  .form-g {
    margin-top: 45%;
    .input-group {
      margin-bottom: px2rem(25px);
      position: relative;
      input {
        width: px2rem(450px);
        height: px2rem(75px);
        box-sizing: border-box;
        background: rgba(105, 106, 108, 0.7);
        border-radius: px2rem(37px);
        padding: 0 px2rem(60px) 0 px2rem(30px);
        font-size: px2rem(28px);
        color: #fff;
      }
      .icon {
        position: absolute;
        right: px2rem(170px);
        top: px2rem(22px);
        font-size: px2rem(36px);
        color: rgba(255, 255, 255, 0.8);
      }
    }
    .forget-pwd {
      font-size: px2rem(24px);
      text-decoration: underline;
      color: rgba(255, 255, 255, 0.8);
      margin-top: px2rem(10px);
      cursor: pointer;
    }
    .form-btn {
      margin: px2rem(70px) auto px2rem(120px);
      width: px2rem(320px);
      height: px2rem(80px);
      line-height: px2rem(80px);
      text-align: center;
      background: #ea2448;
      border-radius: px2rem(40px);
      font-size: px2rem(26px);
      cursor: pointer;
    }
  }
  .other-login {
    p {
      color: rgba(255, 255, 255, 0.8);
      font-size: px2rem(22px);
    }
    .other-login-logo {
      display: flex;
      justify-content: center;
      margin-top: px2rem(40px);
      > div {
        width: px2rem(90px);
        height: px2rem(90px);
        line-height: px2rem(90px);
        border: 2px solid rgba(255, 255, 255, 0.8);
        border-radius: 50%;
        cursor: pointer;
        .icon {
          font-size: px2rem(50px);
          color: rgba(255, 255, 255, 0.8);
        }
        &.qq {
          margin: 0 px2rem(25px);
        }
      }
    }
  }
}

@media screen and(min-width: 769px) {
  .mask {
    width: 460px;
    margin: 0 auto;
  }
  .form-g {
    .input-group {
      .icon {
        right: px2rem(120px) !important;
      }
    }
  }
}
</style>
