<template>
  <div>
    <!--搜索框-->
    <el-form :inline="true" :model="pageInfo" class="demo-form-inline">
      <el-form-item label="基金代码">
        <el-input v-model="pageInfo.fundCode" placeholder="基金代码" />
      </el-form-item>
      <el-form-item label="基金名称">
        <el-input v-model="pageInfo.fundName" placeholder="基金名称" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
      </el-form-item>
    </el-form>
    <el-backtop target=".page-component__scroll .el-scrollbar__wrap" />
    <!--表格-->
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column fixed prop="id" label="序号" width="100" />
      <el-table-column prop="fundCode" label="基金代码" width="120" />
      <el-table-column prop="fundName" label="基金名称" width="300" />
      <el-table-column prop="fundNav" label="单位净值" width="120" />
      <el-table-column prop="fundAccnav" label="累计净值" width="120" />
      <el-table-column prop="fundDate" label="基金日期" width="120" />
      <el-table-column prop="fundDgr" label="日增长率" width="120" />
      <el-table-column prop="subsribeState" label="申购状态" width="120" />
      <el-table-column prop="redeemState" label="赎回状态" width="120" />
      <el-table-column prop="fundFee" label="基金手续费" width="120" />
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="handleClick(scope.row)">
            查看
          </el-button>
<!--          <el-button type="text" size="small" @click="open(scope.row)">购买</el-button>-->
        </template>
      </el-table-column>
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
  name: 'FundList',
  data() {
    return {
      tableData: [],
      pageInfo: {
        pageNumber: 1,
        pageSize: 7,
        total: 0,
        fundCode: null,
        fundName: null
      }
    }
  },
  created() {
    this.getFundList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.fundCode, this.pageInfo.fundName)
    console.log(this.tableData)
  },
  mounted() {
    this.getFundList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.fundCode, this.pageInfo.fundName)
  },
  methods: {
    // 分页查询方法
    getFundList(pageNum, pageSize, fundCode, fundName) {
      var url = 'http://127.0.0.1:8081/fund/list'
      this.$axios({
        method: 'post',
        url: url,
        data: { pageNumber: pageNum, pageSize: pageSize, fundCode: fundCode, fundName: fundName }
      }).then(res => {
        // 获取表格数据
        this.tableData = res.data.data.records
        // 获取页码数据
        this.pageInfo.pageNumber = res.data.data.current
        this.pageInfo.pageSize = res.data.data.size
        this.pageInfo.total = res.data.data.total
      })
    },
    // 查询基金历史净值
    handleClick(row) {
      console.log(row)
      // alert(row.fundCode)
      this.$router.push({
        path: '/fundHistory',
        query: {
          fundCode: row.fundCode,
          fundName: row.fundName
        }
      })
    },
    // 打开需要购买金额的弹窗
    open(row) {
      this.$prompt('请输入购买金额', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        inputPattern: /^([0-9]+|[0-9]{1,3}(,[0-9]{3})*)(\.[0-9]{2})?$/,
        inputErrorMessage: '输入金额格式不正确'
      }).then(({ value }) => {
        // 点击确定进行的操作
        this.fundPurchase(row, value)
      }).catch(() => {
        // 点击取消进行的操作
      })
    },
    // 购买对应基金函数
    fundPurchase(row, purchaseAmount) {
      const url = 'http://127.0.0.1:8081/fund/purchase'
      const token = localStorage.getItem('token')
      this.$axios({
        method: 'post',
        url: url,
        headers: { token: token },
        data: { fundId: row.id, fundDate: row.fundDate, purchaseAmount: purchaseAmount }
      }).then(res => {
        if (res.data.success) {
          this.$message({
            message: '购买成功',
            type: 'success'
          })
        } else {
          this.$message.error(res.data.message)
        }
      })
    },
    // 分页方法
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`)
      this.pageInfo.pageSize = val
      this.getFundList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.fundCode, this.pageInfo.fundName)
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
      this.pageInfo.pageNumber = val
      this.getFundList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.fundCode, this.pageInfo.fundName)
    },
    // 搜索框方法
    onSubmit() {
      console.log('submit!')
      this.getFundList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.fundCode, this.pageInfo.fundName)
    }

  }

}
</script>

<style scoped>

</style>
