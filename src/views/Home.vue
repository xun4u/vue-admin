<template>

  <el-container class="home_container">
    <el-header>
      <div>
        <img src="../assets/logo.png" alt="">
        <span>后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">登出</el-button>
    </el-header>
    <el-container>
      <el-aside :width="isCollapse? '64px': '200px'">
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <el-menu
            background-color="#333744"
            text-color="#fff"
            active-text-color="#ffd04b"
            unique-opened
            :collapse="isCollapse"
            :collapse-transition="false">
          <!-- 级菜单-->
          <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id" >
            <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span>{{ item.authName }}</span>
            </template>
            <!--二级菜单-->
            <el-menu-item v-for="secItem in item.children" :key="secItem.id" :index="secItem.id+''">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{ secItem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>

</template>

<script lang="ts">
export default {
  name: 'Home',
  data(){
    return{
      menuList:[],
      iconObj:{
        '125' :'iconfont icon-yonghuguanli',
        '103' :'iconfont icon-cedaohang-quanxian',
        '101' :'iconfont icon-shangpinguanli',
        '102' :'iconfont icon-dingdanguanli',
        '145' :'iconfont icon-icon-test',
      },
      isCollapse:false
    }
  },
  created() {
    this.getMenuList();
  },
  methods: {
    async getMenuList() {
      const {data: res} = await this.$http.get('menus');
      if(res.meta.status !== 200)return this.$message.error(res.meta.msg)
      this.menuList = res.data
    },
    logout() {
      window.sessionStorage.clear();
      this.$router.push('/login');
    },
    toggleCollapse(){
      this.isCollapse = !this.isCollapse
    }

  }
};
</script>

<style lang="scss" scoped>
.home_container {
  height: 100%;
}

.el-header {
  background: #373d41;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-left: 10px;
  font-size: 20px;
  color: #ffffff;

  > div {
    height: 100%;
    display: flex;
    align-items: center;

    > span {
      margin-left: 10px;
    }

    > img {
      height: 40px;
      width: 40px;
    }
  }

}

.el-aside {
  background: #333744;

  .el-menu {
    border-right: none;
  }
}

.el-main {
  background: #eaedf1;
}
.iconfont{
  margin-right: 10px;
}
.toggle-button{
  text-align: center;
  color: #fff;
  font-size: 10px;
  line-height: 2;
  letter-spacing: 0.2em;
  background: #4a5064;
  cursor: pointer;
}
</style>