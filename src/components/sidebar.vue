<template>
  <div class="sidebar" :class="{showSidebar: showSidebar}">
    <div class="sidebar-con" :class="{showbar: showSidebar}">
      <div class="head">
        <div class="avatar">
          <img  :src="userInfo.avatarUrl" alt />
        </div>
        <div class="name" v-if="status">{{userInfo.nickname}}</div>
      </div>
      <div class="menu">
        <ul>
          <li @click="_hidebar">
            <router-link to="/user" @click="_hidebar">
              <i class="icon">&#xe63c;</i>
              <span>个人中心</span>
            </router-link>
          </li>
          <li @click="showToast" v-if="status">
            <router-link to="/login">
              <i class="icon">&#xe61f;</i>
              <span>登陆</span>
            </router-link>
          </li>
          <li @click="_logout" v-if="!status">
            <router-link to="/">
              <i class="icon">&#xe61f;</i>
              <span>退出登录</span>
            </router-link>
          </li>
        </ul>
      </div>
    </div>
    <div v-show="showSidebar" class="sidebar_mask" @click="_hidebar"></div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  data() {
    return {
      userInfo: {
        avatarUrl: "",
        nickname: ""
      },
      status:false,
    };
  },
  mounted() {
    if (localStorage.getItem("userInfo"))
      this.userInfo = JSON.parse(localStorage.getItem("userInfo"));
      // console.log(this.userInfo)
  },
  created(){
    this.user = JSON.parse(localStorage.getItem("userInfo"));
      if(this.user === null){
        this.status = true
      }
      else{
        this.status = false
        this.userInfo = this.user
      }
  },
  methods: {
    _hidebar() {
      this.$store.dispatch("setShowSidebar", false);
    },
    showToast() {
      // this.$toast('开发中，敬请期待...')
    },
    _logout() {
      localStorage.removeItem("userInfo")
       window.location.reload(true); 
      this._hidebar()
    }
  },
  computed: {
    ...mapGetters(["showSidebar"])
  }
};
</script>

<style scoped lang="scss">
@import "../assets/css/function";
.sidebar {
  color: #000;
  .sidebar-con {
    background: rgba(255, 255, 255, 0.9);
    position: absolute;
    top: 0;
    left: px2rem(-400px);
    transform: translateZ(0);
    opacity: 0;
    width: px2rem(350px);
    z-index: 1002;
    height: 100%;
    overflow: auto;
    transition: all 0.3s ease;

    &.showbar {
      transform: translateX(px2rem(400px));
      opacity: 1;
    }
    .head {
      text-align: center;
      .avatar {
        width: px2rem(90px);
        height: px2rem(90px);
        // background: #000;
        border-radius: 50%;
        margin: px2rem(60px) auto px2rem(15px);
        img {
          border: 1px solid;
          width: 100%;
        }
      }
      .name {
        font-size: px2rem(32px);
      }
    }
    .menu {
      margin-top: px2rem(30px);
      ul {
        li {
          height: px2rem(90px);
          line-height: px2rem(90px);
          a {
            display: block;
            padding-left: px2rem(60px);
            .icon {
              font-size: px2rem(36px);
              vertical-align: middle;
            }
            span {
              vertical-align: middle;
              font-size: px2rem(24px);
              padding-left: px2rem(20px);
              color: #000;
            }
          }
          &:hover {
            .icon,
            span {
              color: rgba(221, 27, 27, 0.9);
            }
          }
        }
      }
    }
  }
  .sidebar_mask {
    position: fixed;
    width: 100%;
    margin: 0 auto;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    z-index: 1001;
    background: rgba(0, 0, 0, 0.4);
  }
}
</style>
