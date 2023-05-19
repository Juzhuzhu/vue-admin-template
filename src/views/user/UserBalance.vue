<template>
  <div style="width: 80.8%">
    <el-button type="primary" @click="reload" >刷新</el-button>
    <!--表格-->
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column fixed prop="id" label="主键id" width="100" v-if="false"/>
      <el-table-column prop="name" label="用户名" width="120"/>
      <el-table-column prop="fundCode" label="基金代码" width="120"/>
      <el-table-column prop="fundName" label="基金名称" width="350"/>
      <el-table-column prop="fundDate" label="买入基金日期" width="120"/>
      <el-table-column prop="latestDate" label="最后计算日期" width="120"/>
      <el-table-column prop="purchaseAmount" label="买入金额（元）" width="120"/>
      <el-table-column prop="balance" label="当前余额（元）" width="120"/>
    </el-table>
    <br>
    <!--  分页  -->
    <div class="block">
      <el-pagination
        :current-page.sync="this.pageInfo.pageNumber"
        :page-size="this.pageInfo.pageSize"
        layout="total, prev, pager, next"
        :total="this.pageInfo.total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "UserBalance",
  data() {
    return {
      tableData: [],
      pageInfo: {
        pageNumber: 1,
        pageSize: 6,
        total: 0,
      }
    }
  },
  created() {
    this.getUserBalance(this.pageInfo.pageNumber, this.pageInfo.pageSize);
  },
  methods: {
    // 分页查询方法
    getUserBalance(pageNum, pageSize) {
      const url = 'http://127.0.0.1:8081/fund/user/balance';
      const token = localStorage.getItem("token");
      console.log(token);
      this.$axios({
        method: 'post',
        url: url,
        headers: {token: token},
        data: {pageNumber: pageNum, pageSize: pageSize}
      }).then(res => {
        // 获取表格数据
        this.tableData = res.data.data.records
        // 获取页码数据
        this.pageInfo.pageNumber = res.data.data.current
        this.pageInfo.pageSize = res.data.data.size
        this.pageInfo.total = res.data.data.total
      })
    },
    // 分页方法
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`)
      this.pageInfo.pageSize = val
      this.getUserBalance(this.pageInfo.pageNumber, this.pageInfo.pageSize)
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
      this.pageInfo.pageNumber = val
      this.getUserBalance(this.pageInfo.pageNumber, this.pageInfo.pageSize)
    },
    reload(){
      this.getUserBalance(1, 6);
    }
  }
}
</script>

<style scoped>

</style>
