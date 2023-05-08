<template>
  <div>
    <!--搜索框-->
    <el-form :inline="true" :model="pageInfo" class="demo-form-inline" style="margin-top: 10px">
      <el-form-item label="用户名">
        <el-input v-model="pageInfo.name" placeholder="用户名称"/>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
      </el-form-item>
    </el-form>
    <el-button type="primary" @click="add" size="small">新增用户</el-button>
    <el-backtop target=".page-component__scroll .el-scrollbar__wrap"/>
    <!--表格-->
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column fixed prop="id" label="用户id" width="100"/>
      <el-table-column prop="name" label="用户名" width="120"/>
      <el-table-column prop="phoneNumber" label="电话号码" width="120"/>
      <el-table-column prop="amount" label="用户余额" width="120"/>
      <el-table-column prop="headImgUrl" label="用户头像图片地址" width="400"/>
      <el-table-column prop="userStateEnum" label="用户状态" width="120"/>
      <el-table-column prop="utcCreate" label="创建时间" width="200"/>
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="handleClick(scope.row)">
            修改
          </el-button>
          <el-button type="text" size="small" @click="open(scope.row)">删除</el-button>
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
  name: 'UserList',
  data() {
    return {
      tableData: [],
      pageInfo: {
        pageNumber: 1,
        pageSize: 6,
        total: 0,
        fundCode: null,
        name: null
      }
    }
  },
  created() {
    this.getUserList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.name)
    console.log(this.tableData)
  },

  methods: {
    // 分页查询方法
    getUserList(pageNum, pageSize, name) {
      var url = 'http://127.0.0.1:8081/user/list'
      this.$axios({
        method: 'post',
        url: url,
        data: { pageNumber: pageNum, pageSize: pageSize, name: name }
      }).then(res => {
        // 获取表格数据
        this.tableData = res.data.data.records
        // 获取页码数据
        this.pageInfo.pageNumber = res.data.data.current
        this.pageInfo.pageSize = res.data.data.size
        this.pageInfo.total = res.data.data.total
      })
    },
    // 前往修改界面
    handleClick(row) {
      this.$router.push({
        path: '/updateUserComponent',
        query: {
          row: row
        }
      })
    },
    //新手动新增用户
    add() {
      this.$router.push('/addUserComponent')
    },
    // 打开需要购买金额的弹窗
    open(row) {
      this.$confirm('此操作将删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 点击确定进行的操作
        this.deleteUserById(row.id)
      }).catch(() => {
        // 点击取消进行的操作
      })
    },
    //根据id删除对应用户
    deleteUserById(id) {
      const url = 'http://127.0.0.1:8081/user/delete'
      this.$axios({
        method: 'post',
        url: url,
        async: false,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        data: { id: id }
      }).then(res => {
        if (res.data.success) {
          this.$message({
            message: '删除成功',
            type: 'success'
          })
          this.getUserList(this.pageInfo.pageNumber, this.pageInfo.pageSize, null)
        } else {
          this.$message.error(res.data.message)
        }
      })
    },
    // 分页方法
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`)
      this.pageInfo.pageSize = val
      this.getUserList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.name)
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`)
      this.pageInfo.pageNumber = val
      this.getUserList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.name)
    },
    // 搜索框方法
    onSubmit() {
      console.log('submit!')
      this.getUserList(this.pageInfo.pageNumber, this.pageInfo.pageSize, this.pageInfo.name)
    }

  }

}
</script>

<style scoped>

</style>
