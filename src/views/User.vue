<template>
  <div class="user_container">
    <div class="user_container_toolBar">
      <h2>用户管理</h2>
      <el-input
        placeholder="请输入用户名"
        prefix-icon="el-icon-search"
        v-model="keyWord"
        @change="searchByUserName"
      >
      </el-input>
    </div>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="openid" label="openID" align="center"></el-table-column>
      <el-table-column prop="username" label="用户名" align="center"></el-table-column>
      <el-table-column prop="password" label="密码" align="center"></el-table-column>
      <el-table-column prop="time" label="创建时间" align="center"></el-table-column>
      <el-table-column label="操作" align="center">
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
            >
              删除
            </el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      class="user_pagination"
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
      keyWord: ''
    };
  },
  created() {
    this.getUserData();
  },
  methods: {
    getUserData: async function() {
      const params = {
        page: this.page,
        size: this.size,
        keyWord: this.keyWord
      };
      const {
        data: {result, total},
      } = await this.$http.post('/admin/getUser', params);
      this.tableData = result.map((item) => {
        return {
          ...item,
          time: dayjs(item.date).format("YYYY-MM-DD HH:mm:ss")
        }
      })
      this.total = total;
    },
    handleSizeChange(val) {
      this.size = val;
      this.getUserData();
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getUserData();
    },
    searchByUserName() {
      this.getUserData();
    },
    async deleteItem(_id) {
      const params = {
        _id
      };
      const { data } = await this.$http.post('/admin/deleteUser', params);
      if (data === 'success') {
        this.$message.success(`删除成功`);
        this.getUserData();
      } else {
        this.$message.error(`删除失败`);
      }
    }
  }
}
</script>

<style lang="less" scoped>
  .user_container {
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
      h2 {
        text-align: left;
      }
      .el-input {
        width: 300px;
        display: flex;
        align-items: center;
      }
    }
    .user_pagination {
      margin-top: 20px;
    }
  }
</style>