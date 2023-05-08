<template>
  <div style="width: 300px;margin-top: 30px">
    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="用户id">
        <el-input v-model="rowInfo.id" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="用户状态">
        <el-input v-model="rowInfo.userStateEnum" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="用户名">
        <el-input v-model="rowInfo.name"></el-input>
      </el-form-item>
      <el-form-item label="电话号码">
        <el-input v-model="rowInfo.phoneNumber"></el-input>
      </el-form-item>
      <el-form-item label="用户头像图片地址">
        <el-input v-model="rowInfo.headImgUrl" :disabled="true" style="width: 500px"></el-input>
      </el-form-item>
      <el-form-item label="创建时间">
        <el-input v-model="rowInfo.utcCreate" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="用户余额">
        <el-input v-model="rowInfo.amount" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">更新</el-button>
        <el-button @click="back">取消</el-button>
      </el-form-item>
    </el-form>
  </div>


</template>

<script>
export default {
  name: 'UpdateUserComponent',
  data() {
    return {
      rowInfo: {
        id: this.$route.query.row.id,
        userStateEnum: this.$route.query.row.userStateEnum,
        name: this.$route.query.row.name,
        headImgUrl: this.$route.query.row.headImgUrl,
        phoneNumber: this.$route.query.row.phoneNumber,
        utcCreate: this.$route.query.row.utcCreate,
        amount: this.$route.query.row.amount
      }
    }
  },
  methods: {
    onSubmit() {
      const url = 'http://127.0.0.1:8081/user/update'
      // console.log('submit!');
      this.$axios({
        method: 'post',
        url: url,
        data: {
          id: this.rowInfo.id,
          name: this.rowInfo.name,
          phoneNumber: this.rowInfo.phoneNumber
        }
      }).then(res => {
        if (res.data.success) {
          this.$message({
            message: '更新成功',
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
