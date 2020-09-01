<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>活动管理</el-breadcrumb-item>
      <el-breadcrumb-item>活动列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <el-row :gutter="20">
        <el-col :span="10">
          <el-input placeholder="请输入内容" v-model="queryData.query" clearable @clear="getUserList">
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addDialogVisible =true">添加用户</el-button>
        </el-col>
      </el-row>

      <el-table
          :data="userList"
          stripe
      >
        <el-table-column type="index" width="50" label="#"></el-table-column>
        <el-table-column prop="username" label="用户名" width="120"></el-table-column>
        <el-table-column prop="mobile" label="电话" width="120"></el-table-column>
        <el-table-column prop="email" label="邮箱" width="120"></el-table-column>
        <el-table-column prop="create_time" label="创建时间" width="120"></el-table-column>
        <el-table-column label="用户状态" width="120">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.mg_state" @change="userStateChange(scope.row)">
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column prop="role_name" label="角色名称" width="120"></el-table-column>

        <el-table-column
            fixed="right"
            label="操作"
        >
          <template slot-scope="scope">
            <el-button @click="showEditDialog(scope.row)" type="text" size="small">修改</el-button>
            <el-button type="text" size="small" @click="removeUserById(scope.row)">删除</el-button>
            <el-button type="text" size="small">分配角色</el-button>
          </template>
        </el-table-column>
      </el-table>

      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="queryData.pagenum"
          :page-sizes="[10, 20, 50, 100]"
          :page-size="queryData.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>
    </el-card>
    <!-- 新增表单-->
    <el-dialog
        title="管理员新增"
        :visible.sync="addDialogVisible"
        width="50%" @close="addDialogClose">
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>

<!--修改表单-->
    <el-dialog
        title="管理员修改"
        :visible.sync="editDialogVisible"
        width="50%" @close="editDialogClose">
      <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editForm.username" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editUser">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script lang="ts">
export default {
  name: 'User',
  data() {
    const checkEmail = (rule, value, callback) => {
      const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/;
      if (value && !regEmail.test(value)) {
        return callback(new Error('请输入正确的邮箱'));
      }
      return callback();
    };
    const checkMobile = (rule, value, callback) => {
      const regEmail = /^1[3-9]\d{9}$/;
      if (value && !regEmail.test(value)) {
        return callback(new Error('请输入正确的手机号'));
      }
      return callback();
    };
    return {
      queryData: {
        query: '',
        pagenum: 1,
        pagesize: 20,
      },
      userList: [],
      total: 0,
      addDialogVisible: false,
      editDialogVisible: false,
      addForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      editForm:{},
      addFormRules: {
        username: [
          {required: true, message: '请输入用户名称', trigger: 'blur'},
          {min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur'}
        ],
        password: [
          {required: true, message: '请输入用户密码', trigger: 'blur'},
          {min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur'}
        ],
        email: [
          {required: false},
          {validator: checkEmail, trigger: 'blur'}
        ],
        mobile: [
          {validator: checkMobile, trigger: 'blur'}
        ],
      },
      editFormRules:{
        email: [
          {required: false},
          {validator: checkEmail, trigger: 'blur'}
        ],
        mobile: [
          {validator: checkMobile, trigger: 'blur'}
        ],
      }
    };
  },
  created() {
    this.getUserList();
  },
  methods: {
    async getUserList() {
      const {data: res} = await this.$http.get('users', {params: this.queryData});
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg);
      }
      this.userList = res.data.users;
      this.total = res.data.total;
    },
    //每页条数
    handleSizeChange(newSize) {
      this.queryData.pagesize = newSize;
      this.getUserList();
    },
    //当前页码
    handleCurrentChange(newPage) {
      this.queryData.pagenum = newPage;
      this.getUserList();
    },
    async userStateChange(row) {
      const {data: res} = await this.$http.put(`users/${row.id}/state/${row.mg_state}`);
      if (res.meta.status !== 200) {
        row.mg_state = !row.mg_state;
        return this.$message.error(res.meta.msg);
      }
      return this.$message.success(res.meta.msg);
    },
    addDialogClose() {
      this.$refs.addFormRef.resetFields();
    },
    addUser() {
      this.$refs.addFormRef.validate(async (valid) => {
        if (!valid) return;
        const {data: res} = await this.$http.post('users', this.addForm);
        if (res.meta.status !== 201) {
          return this.$message.error(res.meta.msg);
        }
        this.$message.success(res.meta.msg);
        this.addDialogVisible = false;
        return this.getUserList();
      });
    },
    async showEditDialog(user){
      const {data:res} = await this.$http.get(`users/${user.id}`)
      if(res.meta.status !== 200){
        this.$message.error(res.meta.msg)
      }
      this.editForm = res.data
      this.editDialogVisible = true
    },
    editDialogClose(){
      this.$refs.editFormRef.resetFields()
    },
    editUser(){
      this.$refs.editFormRef.validate(async (valid)=>{
        if(!valid)return
        const {data:res} = await this.$http.put(`users/${this.editForm.id}`,{
          email:this.editForm.email,
          mobile:this.editForm.mobile,
        })
        if(res.meta.status !==200){
          this.$message.error(res.meta.msg)
        }
        this.$message.success(res.meta.msg)
        this.editDialogVisible = false
        this.getUserList()
      })
    },
    async removeUserById(user){
      const delAction = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err=>err)
      if(delAction !== 'confirm'){
        this.$message.info('已取消删除')
      }
      const {data:res} = await this.$http.delete(`users/${user.id}`)
      if(res.meta.status !==200){
        this.$message.error(res.meta.msg)
      }
      this.$message.success(res.meta.msg)
      this.getUserList()
    }
  }
};
</script>

<style lang="scss" scoped>

</style>