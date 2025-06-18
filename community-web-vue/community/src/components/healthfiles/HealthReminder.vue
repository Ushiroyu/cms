<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>健康提醒</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-form :model="form">
        <el-form-item label="提醒内容">
          <el-input v-model="form.text" style="width: 70%" />
        </el-form-item>
        <el-form-item label="提醒时间">
          <el-date-picker v-model="form.date" type="datetime" />
        </el-form-item>
        <el-button type="primary" @click="addReminder">添加</el-button>
      </el-form>
      <el-divider />
      <el-timeline>
        <el-timeline-item
          v-for="(item, index) in reminders"
          :key="index"
          :timestamp="item.date"
        >{{ item.text }}</el-timeline-item>
      </el-timeline>
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'HealthReminder',
  data () {
    return {
      form: {
        text: '',
        date: ''
      },
      reminders: []
    }
  },
  methods: {
    addReminder () {
      if (!this.form.text || !this.form.date) {
        return this.$message.warning('请填写提醒内容和时间')
      }
      this.reminders.push({ ...this.form })
      this.form.text = ''
      this.form.date = ''
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
