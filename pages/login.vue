<template>
  <div class="login-container">
    <el-form ref="loginForm" label-width="100px" class="login-form" :model="form" :rules="rules">
      <div class="title-container">
        <img src="/logo.png" alt="">
      </div>
      <el-form-item prop="email" label="邮箱">
        <el-input v-model="form.email" placeholder="请输入邮箱" />
      </el-form-item>
      <!-- 图片验证码 -->
      <el-form-item prop="captcha" label="验证码" class="captcha-container">
        <div class="captcha">
          <img :src="code.captcha" alt="" @click="resetCaptcha">
        </div>
        <el-input v-model="form.captcha" placeholder="请输入验证码" />
      </el-form-item>
      <!-- 邮件验证码 -->
      <el-form-item prop="emailcode" label="邮箱验证码" class="captcha-container">
        <div class="captcha">
          <el-button type="primary" :disabled="send.timer>0" @click="sendEmailCode">
            {{ sendText }}
          </el-button>
        </div>
        <el-input v-model="form.emailcode" placeholder="请输入邮箱验证码" />
      </el-form-item>
      <el-form-item prop="password" label="密码">
        <el-input v-model="form.password" type="password" placeholder="请输入密码" />
      </el-form-item>
      <el-form-item label="">
        <el-button type="primary" @click.native.prevent="handlerLogin">
          登录
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import md5 from 'md5'
export default {
  layout: 'login',
  data () {
    return {
      timer: null,
      send: {
        timer: 0
      },
      form: {
        email: '417703682@qq.com',
        password: 'a417703683',
        captcha: '',
        emailcode: ''
      },
      rules: {
        email: [
          { required: true, message: '请输入邮箱' },
          { type: 'email', message: '请输入正确的邮箱格式' }
        ],
        captcha: [
          { required: true, message: '请输入验证码' }
        ],
        emailcode: [
          { required: true, message: '请输入邮箱验证码' }
        ],
        password: [
          { required: true, pattern: /^[\w_-]{6,12}$/, message: '请输入6~12位密码' }
        ]
      },
      code: {
        captcha: '/api/captcha'
      }
    }
  },
  computed: {
    sendText () {
      if (this.send.timer <= 0) {
        return '发送'
      }
      return `${this.send.timer}s后发送`
    }
  },
  methods: {
    // 发送邮件
    async sendEmailCode () {
      await this.$http.get('/sendcode?email=' + this.form.email)
      this.send.timer = 5 // 生成环境改成60秒
      this.timer = setInterval(() => {
        this.send.timer -= 1
        if (this.send.timer === 0) {
          clearInterval(this.timer)
          this.timer = null
        }
      }, 1000)
    },
    resetCaptcha () {
      this.code.captcha = '/api/captcha?_t' + new Date().getTime()
    },
    handlerLogin () {
      this.$refs.loginForm.validate(async (valid) => {
        if (valid) {
          const params = {
            email: this.form.email,
            password: md5(this.form.password), // 注意这里实际使用时应该加盐
            captcha: this.form.captcha,
            emailcode: this.form.emailcode
          }
          const res = await this.$http.post('/user/login', params)
          if (res.code === 200) {
            // 登录成功，返回token，前端存储token
            this.$message({ type: 'success', message: res.message })
            localStorage.setItem('token', res.data.token)
            setTimeout(() => {
              this.$router.push('/')
            }, 500)
          } else {
            this.$message.error(res.message)
          }
        } else {
          console.log('校验失败')
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
