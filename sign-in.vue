<template>

 <div class="sign-in-container">
        <el-card class="box-card">
            <div class="sign-in-body">
                <div class="sign-in-title">注册</div>
                <el-input placeholder="请输入昵称..." maxlength="30"  v-model="userInfo.nickname" class="sign-in-input" clearable>
                    <template slot="prepend">
                        <div class="el-icon-user-solid"></div>
                    </template>
                </el-input>
                <el-input placeholder="请输入手机号..." maxlength="11" v-model="userInfo.number" class="sign-in-input" clearable>
                    <template slot="prepend">
                        <div class="el-icon-phone"></div>
                    </template>
                </el-input>
                <el-input placeholder="请输入密码..." show-password maxlength="16" v-model="userInfo.password" class="sign-in-input" clearable>
                    <template slot="prepend">
                        <div class="el-icon-lock"></div>
                    </template>
                </el-input>
                <el-input placeholder="请再次输入密码..." show-password maxlength="16" v-model="password2" @keyup.enter.native="signIn" class="sign-in-input" clearable>
                    <template slot="prepend">
                        <div class="el-icon-lock"></div>
                    </template>
                </el-input>
                <div class="sign-in-submit">
                    <el-button type="primary" @click="signIn">提交</el-button>
                    <el-button type="danger" @click="resetIn">取消</el-button>
                </div>


                <div class="login-container">
                    <span @click="toLogin" class="login-text">登录</span>
                </div>
            </div>
        </el-card>
    </div>




</template>

<script>
    export default {
        name: "sign-in",
        data(){
            return{
                password2:'',
                userInfo:{
                    number:'',
                    password:'',
                    nickname:''
                }
            };
        },
        methods:{
            toLogin(){
                this.$router.replace({path: '/login'});
            },
            signIn(){
                console.log(this.userInfo.nickname);
                if(this.userInfo.number&&this.userInfo.password&&this.userInfo.nickname){

                    if(this.userInfo.number.length !== 11){
                        this.$message.error('手机号长度不符合要求!')
                    }else if(this.userInfo.password!==this.password2){
                        this.$message.error('两次输入的密码不相同！');
                    }else {
                        this.$api.signIn(this.userInfo).then(res=>{
                            if(res.status_code===1){
                                this.$message({
                                    message: '注册成功！',
                                    type: 'success'
                                });
                                this.$router.replace({path: '/login'});
                            }else {
                                this.$message.error('注册失败，用户已存！');
                            }
                        }).catch(e=>{
                            console.log(e);
                            this.$message.error('注册失败，网络异常！');
                        })
                    }
                }else{
                    this.$message.error('注册信息未填写完整！');
                }
            },
            resetIn(){
           this.userInfo.number = '';
           this.userInfo.password = '';
           this.userInfo.nickname = '';
           this.password2 = '';
            }
        }
    }
</script>

<style scoped>



    .sign-in-container {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        width: 100%;
        background: url("../img/R-C.jpg") no-repeat;
                         background-position: center;
                          background-size: cover;
    }

    .sign-in-body {
        padding: 30px;
        width: 400px;
        height: 100%;
    }

    .sign-in-title {
        padding-bottom: 30px;
        text-align: center;
        font-weight: 600;
        font-size: 20px;
        color: #409EFF;
    }

    .sign-in-input {
        margin-bottom: 20px;
    }
    .sign-in-submit{
        display: flex;
        justify-content: center;
    }
    .login-container{
        padding: 0 10px;
    }
    .login-text{
        color: #409EFF;
        font-size: 16px;
        cursor:pointer;
    }
</style>
