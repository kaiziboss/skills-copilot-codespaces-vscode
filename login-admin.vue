<template>
    <div class="login-container">
        <el-card class="box-card">
            <div class="login-body">
                <div class="login-title">渔具租借管理系统</div>
                <el-form ref="form" :model="userForm">
                    <el-input placeholder="请输入管理员账号" v-model="userForm.number" class="login-input">
                        <template slot="prepend">
                            <div class="el-icon-user-solid"></div>
                        </template>
                    </el-input>
                    <el-input placeholder="请输入管理员密码" v-model="userForm.password" class="login-input"
                              @keyup.enter.native="login"  show-password>
                        <template slot="prepend">
                            <div class="el-icon-lock"></div>
                        </template>
                    </el-input>
                    <div class="login-submit">
                        <el-button type="primary" @click="login">登录</el-button>
                        <el-button type="danger" @click="resetLogin">取消</el-button>
                    </div>
                    <div class="other-submit">
                        <router-link to="/login" class="sign-in-text">用户登录</router-link>
                    </div>
                </el-form>
            </div>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: "login",
        data() {
            return {
                userForm: {
                    number: '',
                    password: ''
                }
            };
        },
        methods: {
            login() {
                this.$api.adminLogin({
                   number: this.userForm.number,
                   password: this.userForm.password
                }).then(res => {
                    console.log(res);
                    if (res.status_code === 1) {
                        console.log(res);
                        this.$sta.isLogin = true;
                        this.$sta.name=res.data.name;
                        this.$router.replace({path:'/platform-admin'});
                    } else {
                        this.$message.error('登录失败，账号或密码错误！');
                    }
                });

            },
            resetLogin(){
                  this.userForm.number='';
                  this.userForm.password='';
            }
        }
    }
</script>

<style scoped>
    .login-container {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        width: 100%;
      background: url("../img/gd1.jpg") no-repeat;
        background-position: center;
         background-size: cover;
    }
    .login-body {
        padding: 30px;
        width: 400px;
        height: 100%;
    }
    .login-title {
        padding-bottom: 30px;
        text-align: center;
        font-weight: 600;
        font-size: 20px;
        color: #409EFF;
        cursor: pointer;
    }
    .login-input {
        margin-bottom: 20px;
    }
    .login-submit {
        display: flex;
        justify-content: center;
    }
    .sign-in-text {
        color: #409EFF;
        font-size: 16px;
        text-decoration: none;
        line-height:28px;
    }
    .other-submit{
        display:flex;
        justify-content: space-between;
        margin-top: 10px;
    }
</style>
