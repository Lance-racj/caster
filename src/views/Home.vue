<template>
  <el-container>
    <el-header>
      <div class="title">
        <img src="https://gw.alipayobjects.com/zos/rmsportal/KDpgvguMpGfqaHPjicRK.svg" alt="logo">
        <h2>校园信息共享后台</h2>
      </div>
      <div class="info">
        <p>{{ role }} {{ nickName }}</p>
        <el-button type="danger" size="small" @click="logOut" icon="el-icon-switch-button">退出</el-button>
      </div>
    </el-header>
    <el-container>
      <el-aside width="220px">
        <el-menu
          :default-active="$route.path"
          class="el-menu-vertical-demo"
          @select="handleSelect"
          text-color="#000000"
          active-text-color="#1677ff"
        >
          <el-menu-item index="/findgoods">
            <i class="el-icon-location"></i>
            <span slot="title">失物寻物管理</span>
          </el-menu-item>
          <el-menu-item index="/findperson">
            <i class="el-icon-menu"></i>
            <span slot="title">失物寻主管理</span>
          </el-menu-item>
          <el-menu-item index="/sale">
            <i class="el-icon-setting"></i>
            <span slot="title">闲置出售管理</span>
          </el-menu-item>
          <el-menu-item index="/buy">
            <i class="el-icon-setting"></i>
            <span slot="title">闲置求购管理</span>
          </el-menu-item>
          <el-menu-item index="/user">
            <i class="el-icon-document"></i>
            <span slot="title">用户管理</span>
          </el-menu-item>
          <el-menu-item index="/admin">
            <i class="el-icon-setting"></i>
            <span slot="title">管理员管理</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      role: '',
      nickName: '',
      currentPath: this.$route.path
    }
  },
  created() {
    const userInfo = localStorage.getItem('userInfo');
    if (userInfo) {
      const { role, nickName } = JSON.parse(userInfo);
      this.role = role === 0? '超级管理员': '管理员';
      this.nickName = nickName;
    } else { // 缓存中没有说明没登录，返回登录
      this.$router.push('/login')
    }
  },
  methods: {
    logOut() {
      localStorage.removeItem('userInfo');
      this.$router.push('/login');
    },
    handleSelect(path) {
      if (path !== this.currentPath) {
        this.$router.push(path);
        this.currentPath = path;
      }
    }
  }
}
</script>

<style lang="less">
  .el-header {
    background-color: #001529;
    color: #ffffff;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: space-between;
    .title {
      display: flex;
      img {
        height: 34px;
        margin-right: 10px;
      }
      img:hover {
        transform: rotate(666turn);
        transition-delay: 1s;
        transition-property: all;
        transition-duration: 59s;
        transition-timing-function: cubic-bezier(.34,0,.84,1)
      }
      h2 {
        font-size: 18px;
        line-height: 34px;
      }    
    }
    .info {
      display: flex;
      align-items: center;
      .el-button {
        margin-left: 20px;
      }
    }
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    height: calc(100vh - 60px);
    .el-menu {
      .is-active {
        background-color: #e6f7ff !important;
      }
      .is-active::after {
        content: '';
        width: 0;
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        height: 100%;
        display: block;
        border-right: 3px solid #1890ff;
      }
    }
    .el-menu-vertical-demo {
      height: calc(100vh - 60px);
    }
  }
  
  .el-main {
    background-color: #E9EEF3;
    color: #333;
    // text-align: center;
    height: calc(100vh - 60px);
  }
</style>