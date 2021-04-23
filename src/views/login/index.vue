<template>
  <div class="box">
    <el-form :model="ruleForm"
             :rules="rules"
             ref="ruleForm"
             :label-position="'top'"
             class="demo-ruleForm">
      <el-form-item label="手机号" prop="phone">
        <el-input v-model="ruleForm.phone"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="ruleForm.password" type="password"></el-input>
      </el-form-item>
      <el-button type="primary"
                 class="btn"
                 @click="submitForm('ruleForm')"
                 :loading="isLoding">登录</el-button>
    </el-form>
  </div>
</template>
<script lang="ts">
import Vue from 'vue'
import { Form } from 'element-ui'
// import request from '@/utils/request'
// import qs from 'qs'
import { login } from '@/api/user'

export default Vue.extend({
  name: 'Login',
  data () {
    return {
      ruleForm: {
        phone: '18201288771',
        password: '111111'
      },
      rules: {
        phone: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { pattern: /^1\d{10}$/, message: '请输入正确的手机号', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 18, message: '长度在 6 到 18 个字符', trigger: 'blur' }
        ]
      },
      isLoding: false
    }
  },
  methods: {
    async submitForm () {
      try {
        await (this.$refs.ruleForm as Form).validate()
        this.isLoding = true
        const { data } = await login(this.ruleForm)
        if (data.state !== 1) {
          this.$message.error(data.success)
        } else {
          // 1登录成功，记录登录状态（vuex）
          this.$store.commit('setUser', data.content)
          // 2. 然后在访问需要登录的页面的时候判断有没有登录状态（路由拦截器）
          //    成功：跳转回原来页面或首页
          this.$router.push(this.$route.query.redirect as string || '/')
          this.$message.success('登录成功')
        }
      } catch (error) {
      }
      this.isLoding = false
    }
  }
})
</script>

<style lang="scss" scoped>
  .box {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    .demo-ruleForm {
      background: #fff;
      padding: 20px;
    }
    .btn {
      width: 100%;
    }
  }
</style>
