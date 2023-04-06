<template>
  <div>
    <!-- 有权限访问 -->
    <div v-if="role === 0" class="admin_container">
      <!-- 头部区域 -->
      <div class="admin_container_toolBar">
        <el-button 
          v-if="role === 0" 
          icon="el-icon-plus"
          class="admin_container_newAdminBtn" 
          type="primary"
          @click="() => {
            this.form.create_time = new Date().getTime()
            dialogFormVisible = true
          }"
        >
          新建管理员
        </el-button>
        <el-dialog title="新增管理员信息" :visible.sync="dialogFormVisible" width="40%" modal @close="clearInfo">
          <el-form :model="form">
            <el-form-item label="管理员用户名" :label-width="formLabelWidth">
              <el-input v-model="form.username" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="管理员密码" :label-width="formLabelWidth">
              <el-input v-model="form.password" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="管理员类型" :label-width="formLabelWidth">
              <el-select v-model="form.role" clearable placeholder="请选择管理员类型">
                <el-option label="超级管理员" value="0"></el-option>
                <el-option label="管理员" value="1"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="管理员花名" :label-width="formLabelWidth">
              <el-input v-model="form.nickname" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="创建时间" :label-width="formLabelWidth">
              <el-input v-model="form.create_time" autocomplete="off" disabled></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleAddAdmin">确 定</el-button>
          </div>
        </el-dialog>
        <el-input
          class="searchInput"
          placeholder="请输入管理员用户名"
          prefix-icon="el-icon-search"
          v-model="keyWord"
          @change="searchByUserName"
        >
        </el-input>
      </div>
      <!-- 表单部分 -->
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column prop="username" label="用户名"></el-table-column>
        <el-table-column prop="password" label="密码"></el-table-column>
        <el-table-column prop="time" label="创建时间"></el-table-column>
        <el-table-column prop="role" label="管理员级别"></el-table-column>
        <el-table-column prop="nickName" label="花名"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-popconfirm
              title="是否删掉这条数据？不可恢复"
              @confirm="deleteItem(scope.row._id)"
            >
              <el-button 
                type="danger" 
                size="small" 
                slot="reference" 
                icon="el-icon-delete"
                style="margin-left: 10px"
                :disabled="scope.row.role === `超级管理员`"
              >
                删除
              </el-button>
            </el-popconfirm>
            <el-button 
              type="primary" 
              size="small" 
              slot="reference" 
              icon="el-icon-edit"
              :disabled="scope.row.role === `超级管理员`"
              style="margin-left: 10px"
              @click="editItem(scope.row)"
            >
              编辑
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        class="admin_pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="page"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination>
    </div>
    <!-- 无权限访问 -->
    <div v-else>
      您没有权限访问该页面
    </div>
  </div>
</template>

<script>
import dayjs from "dayjs";

export default {
  data() {
    return {
      tableData: [],
      page: 1,
      size: 10,
      total: 0,
      keyWord: '',
      role: JSON.parse(localStorage.getItem('userInfo')).role,
      dialogFormVisible: false,
      form: {
        username: '',
        password: '',
        role: '',
        create_time: '',
        nickname: '',
        _id: ''
      },
      formLabelWidth: '120px'
    };
  },
  created() {
    this.getAdminData();
  },
  mounted() {
    if (this.role === 1) {
      this.$message.error(`您没有权限访问该页面`);
    }
  },
  methods: {
    // 获取管理员用户信息
    getAdminData: async function() {
      const params = {
        page: this.page,
        size: this.size,
        keyWord: this.keyWord
      };
      const {
        data: {result, total},
      } = await this.$http.post('/admin/getAdmin', params);
      this.tableData = result.map((item) => {
        return {
          ...item,
          role: item.role === 0? '超级管理员': '管理员',
          time: dayjs(item.create_time).format("YYYY-MM-DD HH:mm:ss")
        }
      })
      this.total = total;
    },
    // 更改每页显示的size大小
    handleSizeChange(val) {
      this.size = val;
      this.getAdminData();
    },
    // 换页
    handleCurrentChange(val) {
      this.page = val;
      this.getAdminData();
    },
    // 根据管理员用户名搜索
    searchByUserName() {
      this.getAdminData();
    },
    // 删除管理员信息
    deleteItem: async function(_id) {
      const params = {
        _id,
        username: JSON.parse(localStorage.getItem('userInfo')).username
      };
      const { data } = await this.$http.post('/admin/deleteAdmin', params);
      if (data === 'success') {
        this.$message.success(`删除成功`);
        this.getAdminData();
      } else {
        this.$message.error(`删除失败`);
      }
    },
    // 新增管理员信息
    handleAddAdmin: async function() {
      const {username, password, nickname, create_time, role, _id} = this.form;
      const params = {
        username,
        password,
        nickname,
        create_time,
        role: Number(role),
        _id
      };
      const res = await this.$http.post('/admin/addAdmin', params);
      if (res.data === 'success') {
        this.$message.success(_id? '编辑成功': '创建成功');
        this.clearInfo();
      } else {
        this.$message.error(_id? '编辑失败': '创建失败');
      }
      this.dialogFormVisible = false;
      this.getAdminData();
    },
    // 编辑管理员信息
    editItem: async function(item) {
      const {
        username,
        password,
        createTime,
        nickName,
        role,
        _id
      } = item;
      this.form.username = username;
      this.form.password = password;
      this.form.role = role === '超级管理员'? '0': '1';
      this.form.nickname = nickName;
      this.form._id = _id;
      this.form.create_time = createTime;
      this.dialogFormVisible = true;
    },
    clearInfo() {
      this.form.username = '';
      this.form.password = '';
      this.form.role = '';
      this.form.nickname = '';
      this.form._id = '';
    }
  }
}
</script>

<style lang="less" scoped>
  .admin_container {
    padding: 20px;
    background-color: #fff;
    border-radius: 20px;
    box-sizing: border-box;
    width: 100%;
    &_toolBar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      .searchInput {
        width: 300px;
        display: flex;
        align-items: center;
      }
      .el-input__inner {
        width: 50%;
      }
    }
    .admin_pagination {
      margin-top: 20px;
    }
  }
</style>