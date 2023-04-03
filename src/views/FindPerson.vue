<template>
  <div class="fp_container">
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="openid" label="openID"> </el-table-column>
      <el-table-column prop="classify_1" label="一级分类"> </el-table-column>
      <el-table-column prop="classify_2" label="二级分类"> </el-table-column>
      <el-table-column prop="name" label="名称"> </el-table-column>
      <el-table-column prop="date" label="丢失时间"> </el-table-column>
      <el-table-column prop="region" label="丢失地点"> </el-table-column>
      <el-table-column prop="phone" label="联系方式"> </el-table-column>
      <el-table-column prop="desc" label="描述"> </el-table-column>
      <el-table-column label="相关图片">
        <template slot-scope="scope">
          <el-image 
            style="width: 100px; height: 100px"
            :src="scope.row.imgList[0]" 
            :preview-src-list="scope.row.imgList">
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
  async created() {
    const params = {
      type: 0,
      page: this.page,
      size: this.size
    }
    // const pageData = await this.$http.post('/admin/getLose', params);
    // console.log('xxx', pageData);
  },
  methods: {
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
    },
  },
};
</script>

<style lang="less" scoped>
.fp_container {
  padding: 20px;
  background-color: #fff;
  border-radius: 20px;
  box-sizing: border-box;
  width: 100%;
}
.fp_pagination {
  margin-top: 20px;
}
</style>
