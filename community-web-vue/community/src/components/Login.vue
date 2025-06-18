<template>
  <div>
    <div class="login_container">
      <!-- 登录块 -->
      <div class="login_box">
        <!-- logo头像 -->
        <div class="avatar_box">
          <img src="../assets/sportlogo.jpg" />
        </div>
        <!-- 表单区域 -->
        <el-form
          ref="loginFormRef"
          :rules="loginRules"
          :model="loginForm"
          label-width="0"
          class="login_form"
        >
          <!-- 用户名 -->
          <el-form-item prop="workNo">
            <el-input
              v-model="loginForm.workNo"
              prefix-icon="iconfont icon-denglu"
              placeholder="用户账号"
            />
          </el-form-item>
          <!-- 密码 -->
          <el-form-item prop="password">
            <el-input
              v-model="loginForm.password"
              prefix-icon="iconfont icon-mima"
              show-password
              placeholder="密码"
            />
          </el-form-item>
          <!-- 角色 -->
          <el-form-item prop="roleId">
            <el-select v-model="loginForm.roleId" clearable placeholder="请选择角色">
              <el-option
                v-for="item in options"
                :key="item.roleId"
                :label="item.role"
                :value="item.roleId"
              />
            </el-select>
          </el-form-item>
          <!-- 按钮 -->
          <el-form-item class="btns">
            <el-link
              @click="isRegister = true"
              style="margin-right: 10px; color: #409EFF"
              :underline="false"
              >没有账号，注册</el-link
            >
            <el-button type="primary" @click="login()">登录</el-button>
            <el-button type="info" @click="resetLoginForm()">重置</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>

    <el-dialog
      title="注册页面"
      v-if="isRegister"
      :visible.sync="isRegister"
      :with-header="false"
      width="50%"
      @close="editClosed"
    >
      <el-form
        v-if="addForm"
        :model="addForm"
        :rules="addFormRules"
        ref="addFormRef"
        label-width="100px"
      >
        <el-form-item label="姓名：" prop="username">
          <el-input
            v-model="addForm.username"
            size="medium"
            style="width: 60%;"
          />
        </el-form-item>
        <el-form-item label="密码：" prop="password">
          <el-input
            v-model="addForm.password"
            show-password
            size="medium"
            style="width: 60%;"
          />
        </el-form-item>
        <el-form-item label="性别：" prop="sex">
          <el-radio v-model="addForm.sex" label="0">男</el-radio>
          <el-radio v-model="addForm.sex" label="1">女</el-radio>
        </el-form-item>
        <el-form-item label="电话号码：" prop="phone">
          <el-input
            v-model="addForm.phone"
            size="medium"
            style="width: 60%;"
          />
        </el-form-item>
        <el-form-item label="社区：" prop="communityCd">
          <el-select
            v-model="addForm.communityCd"
            clearable
            filterable
            placeholder="请选择社区"
          >
            <el-option
              v-for="item in communities"
              :key="item.communityCd"
              :label="item.communityName"
              :value="item.communityCd"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="isRegister = false">取 消</el-button>
        <el-button type="primary" @click="addUser">注 册</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      // 登录表单初始化
      loginForm: {
        workNo: '',
        password: '',
        roleId: ''
      },
      // 注册表单初始化
      addForm: {
        username: '',
        password: '',
        sex: '',
        phone: '',
        communityCd: '',
        roleId: '4'
      },
      // 控制注册对话框
      isRegister: false,
      // 可选社区列表
      communities: [],
      // 角色选项
      options: [
        { roleId: '1', role: '管理员' },
        { roleId: '2', role: '医生' },
        { roleId: '3', role: '员工' },
        { roleId: '4', role: '监护人' }
      ],
      // 登录校验规则
      loginRules: {
        workNo: [
          { required: true, message: '请输入用户账号', trigger: 'blur' },
          { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '用户密码必须填写', trigger: 'blur' },
          { min: 5, max: 12, message: '长度在 5 到 12 个字符', trigger: 'blur' }
        ],
        roleId: [
          { required: true, message: '请选择角色', trigger: 'change' }
        ]
      },
      // 注册校验规则
      addFormRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 2, max: 12, message: '长度在 2 到 12 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 24, message: '长度在 6 到 24 个字符', trigger: 'blur' }
        ],
        sex: [{ required: true, message: '请选择性别', trigger: 'change' }],
        phone: [
          { required: true, message: '请输入电话号码', trigger: 'blur' },
          { min: 8, max: 11, message: '长度在 8 到 11 个字符', trigger: 'blur' }
        ],
        communityCd: [
          { required: true, message: '请选择社区', trigger: 'change' }
        ]
      }
    };
  },
  created() {
    this.selectAddress();
  },
  methods: {
    // 获取社区列表
    async selectAddress() {
      const { data: res } = await this.$http.get('/User/address');
      this.communities = Array.isArray(res.data) ? res.data : [];
    },
    // 登录
    login() {
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return;
        const { data: res } = await this.$http.post('/Worker/login', this.loginForm);
        if (res.flag === 'ok') {
          this.$message.success('登录成功！！！');
          sessionStorage.setItem('workName', res.worker.workName);
          sessionStorage.setItem('id', res.worker.id);
          sessionStorage.setItem('roleId', res.worker.roleId);
          sessionStorage.setItem('isAssess', res.worker.isAssess);
          if (res.worker.roleId === '4') {
            sessionStorage.setItem('phone', res.worker.workNo);
          }
          this.$router.push({ path: '/home', query: { roleId: res.worker.roleId } });
        } else {
          this.$message.error('登录失败！！！');
        }
      });
    },
    // 注册用户
    addUser() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) return;
        const { data: res } = await this.$http.post('/User/insertUser', this.addForm);
        if (res !== 'success') {
          return this.$message.error('注册失败！！！');
        }
        this.$message.success('注册成功！！！');
        this.isRegister = false;
      });
    },
    // 对话框关闭时重置
    editClosed() {
      this.$refs.addFormRef.resetFields();
    },
    // 重置登录表单
    resetLoginForm() {
      this.$refs.loginFormRef.resetFields();
    }
  }
};
</script>

<style lang="less" scoped>
.dialogImg {
  background: url(../assets/register.jpg) no-repeat;
  height: 100%;
  width: 100%;
  background-size: 100% 100%;
}
.login_container {
  background: url("../common/community.jpg") no-repeat;
  height: 100%;
  width: 100%;
  position: fixed;
  background-size: 100% 100%;
}
.login_box {
  width: 450px;
  height: 350px;
  background-color: #fff;
  border-radius: 4px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  .avatar_box {
    width: 130px;
    height: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 2px #ddd;
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #eee;
    img {
      width: 100%;
      height: 100%;
      border-radius: 50%;
      background-color: #eee;
    }
  }
}
.btns {
  display: flex;
  justify-content: flex-end;
}
.login_form {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 0 10px;
  box-sizing: border-box;
}
</style>
