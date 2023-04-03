<template>
  <el-container>
    <el-header>
      <h2>校园信息共享后台</h2>
      <div class="info">
        <p>{{ role }} {{ nickName }}</p>
        <el-button type="primary" size="small" @click="logOut">退出</el-button>
      </div>
    </el-header>
    <el-container>
      <el-aside width="220px">
        <el-menu
          :default-active="currentPath"
          class="el-menu-vertical-demo"
          @select="handleSelect"
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#409EFF">
          <el-menu-item index="/findgoods">
            <i class="el-icon-location"></i>
            <span slot="title">寻物管理</span>
          </el-menu-item>
          <el-menu-item index="/findperson">
            <i class="el-icon-menu"></i>
            <span slot="title">寻主管理</span>
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
      currentPath: '/admin'
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
  // 保证每次进入页面都是admin界面
  mounted() {
    this.$router.push(this.currentPath);
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

<style lang="less" scoped>
  .el-header {
    background-color: #009688;
    color: #333;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: space-between;
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
    .el-menu-vertical-demo {
      height: calc(100vh - 60px);
    }
  }
  
  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    height: calc(100vh - 60px);
  }
</style>