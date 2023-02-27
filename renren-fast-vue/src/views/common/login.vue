
<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="brand-info">
          <h2 class="brand-info__text">管理系统</h2>
<!--          <p class="brand-info__intro">平台前端</p>-->
        </div>
        <div class="login-main">
          <h3 class="login-title">登 录</h3>
          <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
            <el-form-item prop="userName">
              <el-input v-model="dataForm.userName" placeholder="帐号"></el-input>
            </el-form-item>
            <el-form-item prop="password">
              <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
            </el-form-item>
            <el-form-item >
              <el-button style="margin-top: 10px" class="login-btn-submit"  type="primary" plain @click="dialogFormVisible = true">注册</el-button>
            </el-form-item>
          </el-form>

          <el-dialog title="注册" :visible.sync="dialogFormVisible" style="width: 600px;margin: 0 auto" center>
            <el-form :model="registerForm">
              <el-form-item>
                <el-input v-model="registerForm.username" autocomplete="off" placeholder="用户名"></el-input>
              </el-form-item>
              <el-form-item  >
                <el-input v-model="registerForm.password" type="password" autocomplete="off" placeholder="密码"></el-input>
              </el-form-item>
              <el-form-item  >
                <el-input v-model="password2" type="password" autocomplete="off" placeholder="确认密码"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button type="primary" @click="register(), dialogFormVisible = false">注 册</el-button>
              <el-button @click="dialogFormVisible = false">取 消</el-button>
            </div>
          </el-dialog>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getUUID } from '@/utils'
export default {
  data () {
    return {
      dialogFormVisible: false,
      dataForm: {
        userName: '',
        password: '',
        uuid: '',
        captcha: ''
      },
      password2: '',
      dataRule: {
        userName: [
          { required: true, message: '帐号不能为空', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'blur' }
        ],
        captcha: [
          { required: true, message: '验证码不能为空', trigger: 'blur' }
        ]
      },
      captchaPath: '',
      registerForm: {
        username: '',
        password: '',
        status: 1
      }
    }
  },
  created () {
    this.getCaptcha()
  },
  methods: {
    // 登录
    dataFormSubmit () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl('/sys/login'),
            method: 'post',
            data: this.$http.adornData({
              'username': this.dataForm.userName,
              'password': this.dataForm.password,
              'uuid': this.dataForm.uuid
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$cookie.set('token', data.token)
              sessionStorage.setItem('username', this.dataForm.userName)
              this.$router.replace({ name: 'home' })
            } else {
              this.getCaptcha()
              this.$message.error(data.msg)
            }
          })
        }
      })
    },
    // 获取验证码
    getCaptcha () {
      this.dataForm.uuid = getUUID()
      this.captchaPath = this.$http.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
    },
    // 注册
    register  () {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          if (this.registerForm.password === this.password2) {
            this.$http({
              url: this.$http.adornUrl(`/sys/user/register`),
              method: 'post',
              data: this.$http.adornData({
                'username': this.registerForm.username,
                'password': this.registerForm.password
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message.success('注册成功')
              } else {
                this.$message.error(data.msg)
              }
            })
          } else {
            this.$message.error('两次密码不一致')
          }
        }
      })
    }
  }
}
</script>

<style lang="scss">
.site-wrapper.site-page--login {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(38, 50, 56, .6);
  overflow: hidden;
  &:before {
    position: fixed;
    top: 0;
    left: 0;
    z-index: -1;
    width: 100%;
    height: 100%;
    content: "";
    background-image: url(~@/assets/img/login_bg.jpg);
    background-size: cover;
  }
  .site-content__wrapper {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    padding: 0;
    margin: 0;
    overflow-x: hidden;
    overflow-y: auto;
    background-color: transparent;
  }
  .site-content {
    min-height: 100%;
    padding: 30px 500px 30px 30px;
  }
  .brand-info {
    margin: 220px 100px 0 90px;
    color: #fff;
  }
  .brand-info__text {
    margin:  0 0 22px 0;
    font-size: 48px;
    font-weight: 400;
    text-transform : uppercase;
  }
  .brand-info__intro {
    margin: 10px 0;
    font-size: 16px;
    line-height: 1.58;
    opacity: .6;
  }
  .login-main {
    position: absolute;
    top: 0;
    right: 0;
    padding: 150px 60px 180px;
    width: 470px;
    min-height: 100%;
    background-color: #fff;
  }
  .login-title {
    font-size: 16px;
  }
  .login-captcha {
    overflow: hidden;
    > img {
      width: 100%;
      cursor: pointer;
    }
  }
  .login-btn-submit {
    width: 100%;
    margin-top: 24px;
  }
}
</style>
