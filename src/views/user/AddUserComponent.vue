<template>
  <div style="width: 300px;margin-top: 30px">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="用户名">
        <el-input v-model="rowInfo.name"></el-input>
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input v-model="rowInfo.phoneNumber"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">新增</el-button>
        <el-button @click="back">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'AddUserComponent',
  data() {
    return {
      rowInfo: {
        name: '',
        phoneNumber: '',
      }
    }
  },
  methods: {
    onSubmit() {
      const url = 'http://127.0.0.1:8081/user/add'
      // console.log('submit!');
      this.$axios({
        method: 'post',
        url: url,
        data: {
          name: this.rowInfo.name,
          phoneNumber: this.rowInfo.phoneNumber
        }
      }).then(res => {
        if (res.data.success) {
          this.$message({
            message: '添加成功',
            type: 'success'
          })
          //跳转回页面
          this.$router.push('/userList');
        } else {
          this.$message.error(res.data.message);
          this.$router.push('/userList');
        }
      })
    },
    back() {
      this.$router.push('/userList');
    }
  }
}
</script>

<style scoped>

</style>
