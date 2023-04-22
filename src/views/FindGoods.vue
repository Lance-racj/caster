<template>
  <div class="fg_container">
    <div class="fg_container_toolBar">
      <h2>寻物管理</h2>
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
      <el-table-column prop="classify_1" label="一级分类" align="center"> </el-table-column>
      <el-table-column prop="classify_2" label="二级分类" align="center"> </el-table-column>
      <el-table-column prop="name" label="物品名称" align="center"> </el-table-column>
      <el-table-column prop="date" label="丢失时间" align="center"> </el-table-column>
      <el-table-column prop="region" label="丢失地点" align="center"> </el-table-column>
      <el-table-column prop="phone" label="联系方式" align="center"> </el-table-column>
      <el-table-column prop="desc" label="物品描述" align="center"> </el-table-column>
      <el-table-column label="相关图片">
        <template slot-scope="scope">
          <el-image 
            style="width: 100px; height: 100px"
            :src="scope.row.imgList[0].url" 
            :preview-src-list="scope.row.imgList.map(item => item.url)">
          </el-image>
        </template>
      </el-table-column>
      <el-table-column prop="time" label="发布时间"> </el-table-column>
    </el-table>
    <el-pagination
      class="fp_pagination"
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
import dayjs from 'dayjs';

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
    this.getFindGoodsData();
  },
  methods: {
    getFindGoodsData: async function() {
      const params = {
        type: 1,
        page: this.page,
        size: this.size
      }
      const {
        data: {result, total},
      } = await this.$http.post('/admin/getLose', params);
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
      this.getFindGoodsData();
    },
    handleCurrentChange(val) {
      this.page = val;
      this.getFindGoodsData();
    },
    deleteItem() {

    },
    searchByThingName() {

    }
  },
};
</script>

<style lang="less" scoped>
.fg_container {
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
.fg_pagination {
  margin-top: 20px;
}
</style>
