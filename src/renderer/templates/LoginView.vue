<template>
    <div id="wrap" @keyup.enter="submitLogin">
        <Card class="box-card">
            <div class='login_center'>
                <span><img id="logo" src="~@/assets/genaro_logo.png"></span>
                <h1>Genaro</h1>
            </div>
            <Form ref="login" :model="login" :rules="ruleInline">
                <FormItem prop="username">
                    <Input type="text" v-model="login.username" placeholder="User Name">
                        <Icon type="ios-person-outline" slot="prepend"></Icon>
                    </Input>
                </FormItem>
                <FormItem prop="password">
                    <Input type="password" v-model="login.password" placeholder="Password">
                        <Icon type="ios-locked-outline" slot="prepend"></Icon>
                    </Input>
                </FormItem>
                <div class='login_center is-clearfix'>
                    <FormItem>
                        <Button type="primary" @click="submitLogin()" size="large" long>Sign In</Button>
                    </FormItem>

                    <router-link class="otherlink is-pulled-left" to="register">Sign Up</router-link>
                    <router-link class="otherlink is-pulled-right" to="password-reset">Reset Passowrd</router-link>
                </div>
            </Form>
        </Card>
    </div>
</template>

<script> 
    import STROJ_CLIENT from '../utils/StorjApiClient'
    import router from '../router'
    import store from '../store'
    import iView from 'iview'
    import { resetPassword } from '../../bridge/users'

    export default {
        name : 'login-view',
        created: function () {
        },
        data: function() {
            return {
                login: {
                    username: '',
                    password: ''
                },
                ruleInline: {
                    username: [
                        { required: true, message: 'Please input username', trigger: 'blur' },
                        { type: 'email', message: 'Incorrect email format', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: 'Please input password', trigger: 'blur' },
                        { type: 'string', min: 6, message: 'Password length must not be less than 6 bits', trigger: 'blur' }
                    ]
                }
            }
        },
        methods:{
            submitLogin() {
                this.$refs['login'].validate((valid) => {
                    if(valid) {
                        this.$Spin.show()
                        var bridgeUser = this.login.username
                        var bridgePass = this.login.password
                        STROJ_CLIENT.getBucketList(bridgeUser, bridgePass, (err) => {
                            iView.Spin.hide()
                            iView.Modal.error({
                                title : 'Login Error',
                                content: 'Username Or Password Error',
                                okText: 'OK'
                            })
                        }, function(result) {
                            iView.Spin.hide()
                            store.commit('updateUsername', bridgeUser)
                            store.commit('updatePassword', bridgePass)
                            router.push({ path: '/index'})
                        });
                    }
                })
            },
            submitSignup() {
                var this2 = this
                this.$refs['login'].validate((valid) => {
                    if(valid) {
                        this2.$Spin.show()
                        var bridgeUser = this2.login.username
                        var bridgePass = this2.login.password
                        STROJ_CLIENT.register(bridgeUser, bridgePass, function(err){
                            this2.$Spin.hide()
                            this2.$Modal.info({
                                title : 'Register Error',
                                content: 'User already exists',
                                okText: 'OK'
                            })
                        }, function(result){
                            this2.$Spin.hide()
                            this2.$Modal.success({
                                title : 'Register Success',
                                content: 'A mail has been sent to &lt;' + bridgeUser + '&gt;, please follow the instructions in the email to activate your account before login.',
                                okText: 'OK'
                            })
                            console.log(result)
                        })
                    }
                })
            }
        }
    }
</script>

<style scoped>
  .text {
    font-size: 14px;
  }

  .item {
    padding: 18px 0;
  }

  .box-card {
    width: 480px;
    position:relative;
    top:-100px;
    margin:0px auto;
  }

  .login_center {
      width: 100%;
      text-align: center;
  }

  #wrap{
    position:fixed;
    top:35%;
    left:0;
    width:100%;
  }

  #login_form {
      width: 100%;
  }

  #a_forget {
      margin-left:10px;
  }

  .otherlink {
      font-size: 12px
  }
  h1 {
      padding: 10px
  }
</style>