<template>
  <div class="body">
    <img class="log_bg" src="../assets/login-bg.jpg" alt="">
    <div class="form">
      <h2>校园信息共享平台后台管理系统</h2>
      <el-input v-model="username" placeholder="请输入账号"></el-input>
      <el-input v-model="password" placeholder="请输入密码" show-password></el-input>
      <el-button @click="submit">登录</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    async submit() {
      const { username, password } = this;
      if (!username || !password) {
        this.$message.error(`存在必填项未填`);
        return;
      }

      const params = {
        username,
        password
      };

      const res = await this.$http.post('admin/login', params);
      const { data } = res;
      if (typeof data === 'object') {
        localStorage.setItem("userInfo", JSON.stringify(data));
        this.$message.success(`登录成功`)
        this.$router.push('/home');
      } else {
        this.$message.error(`登录失败，请校验用户名及密码`);
        console.log(`errrrr`);
      }
    }
  }
}
</script>

<style lang="less" scoped>
  .body {
    width: 100vw;
    height: 100vh;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    .log_bg {
      width: 100vw;
      height: 100vh;
      position: absolute;
      z-index: 10;
    }
    .form {
      position: absolute;
      z-index: 11;
      padding: 20px 30px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      border-radius: 20px;
      background-image: linear-gradient(120deg, #4c72c6 0%, #0c4379 100%);
      h2 {
        margin-bottom: 20px;
      }
      .el-input {
        margin-bottom: 10px;
        width: 300px;
      }
      .el-button {
        width: 100px;
      }
    }
  }
</style>