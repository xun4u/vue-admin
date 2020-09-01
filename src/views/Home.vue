<template>
  <div>
    <el-button type="info" @click="logout">登出</el-button>
  </div>
</template>

<script lang="ts">
export default {
  name: 'Home',
  methods: {
    logout() {
      window.sessionStorage.clear();
      this.$router.push('/login');
    }
  }
};
</script>

<style lang="scss" scoped>

</style>