<template>
  <div class="user_container">
    <h2>用户管理</h2>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="openid" label="openID"></el-table-column>
      <el-table-column prop="username" label="用户名"></el-table-column>
      <el-table-column prop="password" label="密码"></el-table-column>
      <el-table-column prop="time" label="创建时间"></el-table-column>
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
      :page-size="100"
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
      size: 5,
      total: 0,
    };
  },
  created() {
    this.getUserData();
  },
  methods: {
    getUserData: async function() {
      const params = {
        page: this.page,
        size: this.size
      };
      const {
        data: {result, total},
      } = await this.$http.post('/admin/getUser', params);
      this.tableData = result.map((item) => {
        return {
          ...item,
          time: dayjs(item.time).format("YYYY-MM-DD HH:mm:ss")
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
    h2 {
      text-align: left;
      margin-bottom: 20px;
    }
    .user_pagination {
      margin-top: 20px;
    }
  }
</style>