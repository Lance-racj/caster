<template>
  <div class="buy_container">
    <div class="buy_container_toolBar">
      <h2>闲置求购管理</h2>
      <el-input
        placeholder="请输入物品名称"
        prefix-icon="el-icon-search"
        v-model="keyWord"
        @change="searchByThingName"
      >
      </el-input>
    </div>
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="openid" label="openID" align="center"> </el-table-column>
      <el-table-column prop="name" label="物品名称" align="center"> </el-table-column>
      <el-table-column prop="phone" label="联系方式" align="center"> </el-table-column>
      <el-table-column prop="desc" label="物品描述" align="center"> </el-table-column>
      <el-table-column prop="time" label="发布时间" align="center"> </el-table-column>
      <el-table-column prop="status" label="当前状态" align="center"> </el-table-column>
      <el-table-column label="删除" align="center">
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
      class="buy_pagination"
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
  async created() {
    this.getNeedData();
  },
  methods: {
    // 获取信息主接口
    getNeedData: async function() {
      const params = {
        type: 1,
        page: this.page,
        size: this.size,
        keyWord: this.keyWord
      }
      const {
        data: {result, total},
      } = await this.$http.post('/admin/getNeed', params);
      this.tableData = result.map((item) => {
        return {
          ...item,
          status: item.status === 0? '进行中': '已结束' 
        }
      });
      this.total = total;
    },
    handleSizeChange(val) {
      this.size = val;
      this.getNeedData();
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getNeedData();
    },
    // 删除功能
    deleteItem: async function(_id) {
      const params = {
        _id
      };
      const { data } = await this.$http.post('/admin/delNeed/item', params);
      if (data === 'success') {
        this.$message.success(`删除成功`);
        this.getNeedData();
      } else {
        this.$message.error(`删除失败`);
      }
    },
    // 检索功能
    searchByThingName() {
      this.getNeedData();
    }
  },
};
</script>

<style lang="less" scoped>
.buy_container {
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
}
.buy_pagination {
  margin-top: 20px;
}
</style>
