<template>
  <div class="user_container">
    <h2>用户管理</h2>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="openid" label="openID"></el-table-column>
      <el-table-column prop="username" label="用户名"></el-table-column>
      <el-table-column prop="password" label="密码"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-popconfirm
            title="是否删掉这条数据？不可恢复"
            @confirm="deleteItem(scope.row._id)"
          >
            <el-button slot="reference">删除</el-button>
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
      console.log('uuu', result);
      console.log('uuu', total);
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    },
    deleteItem() {
      
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