<template>
  <div class="login_container">
    <div class="login_box">
      <div class="avatar_box">
        <img src="../assets/logo.png" alt="logo">
      </div>
      <el-form ref="loginFormRef" :model="loginForm" :rules="loginFormRules" label-width="0" class="login_form">
        <el-form-item prop="username">
          <el-input v-model="loginForm.username" prefix-icon="iconfont icon-yonghu" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="loginForm.password" prefix-icon="iconfont icon-mima" placeholder="请输入密码"></el-input>
        </el-form-item>
        <el-form-item class="buttons">
          <el-button type="primary" @click="login">登录</el-button>
          <el-button type="info" @click="resetLoginForm">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  name: 'Login',
  data() {
    return {
      loginForm: {
        'username': '',
        'password': ''
      },
      loginFormRules: {
        username: [
          {required: true, message: '请输入用户名', trigger: 'blur'},
          {min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur'}
        ],
        password: [
          {required: true, message: '请输入密码', trigger: 'blur'},
          {min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur'}
        ],
      }
    };
  },
  methods: {
    resetLoginForm() {
      this.$refs.loginFormRef.resetFields();
    },
    login() {
      this.$refs.loginFormRef.validate(async (valid) => {
        if (!valid) return;
        const {data: res} = await this.$http.post('login', this.loginForm);
        if (res.meta.status !== 200) {
          return this.$message.error(res.meta.msg);
        }
        this.$message.success(res.meta.msg);
        window.sessionStorage.setItem('token', res.data.token);
        this.$router.push('/home');
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.login_container {
  background: #2b4b6b;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;

  .login_box {
    width: 450px;
    height: 300px;
    background: #fff;
    border-radius: 3px;
    position: relative;

    .avatar_box {
      height: 130px;
      width: 130px;
      border: 1px solid #eee;
      border-radius: 50%;
      padding: 10px;
      box-shadow: 0 0 10px #ddd;
      background: #fff;
      position: absolute;
      left: 50%;
      top: 0;
      transform: translate(-50%, -50%);

      img {
        height: 100%;
        width: 100%;
        border-radius: 50%;
        background: #eee;
      }
    }

    .login_form {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      padding: 0 20px;

      .buttons {
        display: flex;
        justify-content: flex-end;
      }
    }
  }
}

</style>