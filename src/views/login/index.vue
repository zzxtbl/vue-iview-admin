<template>
    <div class="login-container">
        <Form autoComplete="on" :model="loginForm" :rules="loginRules" ref="loginForm" label-position="left" label-width="0px" class="card-box login-form">
            <h3 class="title">系统登录</h3>
            <Form-item prop="email">
                <span class="svg-container"><wscn-icon-svg icon-class="jiedianyoujian"/></span>
                <Input type="text" name="email" v-model="loginForm.email" placeholder="邮箱" autoComplete="on"/>
            </Form-item>
            <Form-item prop="password">
                <span class="svg-container"><wscn-icon-svg icon-class="mima"/></span>
                <Input type="password" name="password" @keyup.enter.native="handleLogin" v-model="loginForm.password" placeholder="密码" autoComplete="on"/>
            </Form-item>
            <Form-item>
                <Button type="primary" style="width:100%;" :loading="loading" @click.native.prevent="handleLogin">
                    登录
                </Button>
            </Form-item>
            <div class='tips'>admin账号为:admin@wallstreetcn.com 密码随便填</div>
            <div class='tips'>editor账号:editor@wallstreetcn.com 密码随便填</div>
            <router-link to="/sendpwd" class="forget-pwd">
                忘记密码?(或首次登录)
            </router-link>
        </Form>
        <Modal title="第三方验证" :visible.sync="showDialog">
            邮箱登录成功,请选择第三方验证
            <socialSign></socialSign>
        </Modal>
    </div>
</template>

<script>
import { mapGetters } from 'vuex';
import { isWscnEmail } from 'utils/validate';
// import { getQueryObject } from 'utils';
import socialSign from './socialsignin';

export default {
    components: {
        socialSign
    },
    name: 'login',
    data() {
        const validateEmail = (rule, value, callback) => {
            if (!isWscnEmail(value)) {
                callback(new Error('请输入正确的合法邮箱'));
            } else {
                callback();
            }
        };
        const validatePass = (rule, value, callback) => {
            if (value.length < 6) {
                callback(new Error('密码不能小于6位'));
            } else {
                callback();
            }
        };
        return {
            loginForm: {
                email: 'admin@wallstreetcn.com',
                password: ''
            },
            loginRules: {
                email: [{
                    required: true,
                    trigger: 'blur',
                    validator: validateEmail
                }],
                password: [{
                    required: true,
                    trigger: 'blur',
                    validator: validatePass
                }]
            },
            loading: false,
            showDialog: false
        }
    },
    computed: {
        ...mapGetters([
            'auth_type'
        ])
    },
    methods: {
        handleLogin() {
            this.$refs.loginForm.validate(valid => {
                if (valid) {
                    this.loading = true;
                    this.$store.dispatch('LoginByEmail', this.loginForm).then(() => {
                        this.loading = false;
                        this.$router.push({
                            path: '/'
                        });
                        // this.showDialog = true;
                    }).catch(err => {
                        this.$message.error(err);
                        this.loading = false;
                    });
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });
        },
        afterQRScan() {
            // const hash = window.location.hash.slice(1);
            // const hashObj = getQueryObject(hash);
            // const originUrl = window.location.origin;
            // history.replaceState({}, '', originUrl);
            // const codeMap = {
            //   wechat: 'code',
            //   tencent: 'code'
            // };
            // const codeName = hashObj[codeMap[this.auth_type]];
            // if (!codeName) {
            //   alert('第三方登录失败');
            // } else {
            //   this.$store.dispatch('LoginByThirdparty', codeName).then(() => {
            //     this.$router.push({ path: '/' });
            //   });
            // }
        }
    },
    created() {
        // window.addEventListener('hashchange', this.afterQRScan);
    },
    destroyed() {
        // window.removeEventListener('hashchange', this.afterQRScan);
    }
}
</script>

<style rel="stylesheet/scss" lang="scss">
@import "src/styles/mixin.scss";
.tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 5px;
}
.login-container {
    @include relative;
    height: 100vh;
    background-color: #2d3a4b;
    input:-webkit-autofill {
        -webkit-box-shadow: 0 0 0 1000px #293444 inset !important;
        -webkit-text-fill-color: #fff !important;
    }
    input {
        background: transparent;
        border: 0;
        -webkit-appearance: none;
        border-radius: 0;
        padding: 12px 5px 12px 15px;
        color: #eeeeee;
        height: 47px;
    }
    .ivu-input-wrapper {
        display: inline-block;
        height: 47px;
        width: 85%;
        .ivu-input:hover,
        .ivu-input:focus {
            border: 0;
            box-shadow: none;
        }
    }
    .ivu-form-item-error .ivu-input {
        border: 0;
    }
    .svg-container {
        padding: 6px 5px 6px 15px;
        color: #889aa4;
    }

    .title {
        font-size: 26px;
        font-weight: 400;
        color: #eeeeee;
        margin: 0 auto 40px;
        text-align: center;
        font-weight: bold;
    }

    .login-form {
        position: absolute;
        left: 0;
        right: 0;
        width: 400px;
        padding: 35px 35px 15px;
        margin: 120px auto;
    }

    .ivu-form-item {
        border: 1px solid rgba(255, 255, 255, 0.1);
        background: rgba(0, 0, 0, 0.1);
        border-radius: 5px;
        color: #454545;
    }

    .forget-pwd {
        color: #fff;
    }
}
</style>
