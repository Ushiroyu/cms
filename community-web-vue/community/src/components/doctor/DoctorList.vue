<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>医生信息</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-table :data="doctorList" border stripe>
        <el-table-column label="ID" prop="doctorId" align="center" width="80" />
        <el-table-column label="医生" prop="doctor" align="center" />
        <el-table-column label="科室" prop="departmentName" align="center" />
        <el-table-column label="医院" prop="hospitalName" align="center" />
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'DoctorList',
  data () {
    return {
      doctorList: []
    }
  },
  created () {
    this.getDoctorList()
  },
  methods: {
    async getDoctorList () {
      const { data: res } = await this.$http.get('/medical/doctorDrop')
      this.doctorList = Array.isArray(res) ? res.filter(item => item) : []
    }
  }
}
</script>

<style scoped>
.el-breadcrumb {
  margin-bottom: 15px;
  font-size: 17px;
}
</style>
