<template>
    <div class="layout">
        <div class="layout-top">
            <div class="layout-content">
                <Row>
                    <Col span="24">
                        <span class="layout-logo fl">
                            <a @click="toIndex()" style="width:100px;height:30px;display:block;"></a>
                        </span>
                        <Button type="primary" class="loginIn fr" @click="toSignIn()">企业注册</Button>
                    </Col>
                </Row>
            </div>
        </div>
        <Row class="mianBox">
            <div class="mianBox-content" @keyup.enter="handleSubmit">
                <div class="logoBox">
                    <img src="../../build/loginPic.png" alt="" class="loginPic">
                    <h2 style="margin-bottom:20px;color:#3596F8;">欢迎登录</h2>
                    <Form :model="formValidate" :rules="ruleValidate">
                        <FormItem prop="mobile">
                            <Input v-model="formValidate.mobile" placeholder="请输入已注册的手机号"></Input>
                        </FormItem>
                        <FormItem prop="password">
                            <Input v-model="formValidate.password" type="password" placeholder="请输入密码"></Input>
                        </FormItem>
                        <FormItem>
                            <CheckboxGroup>
                                <Checkbox label="记住密码" v-model="auto"></Checkbox>
                                <span class="fr" style="color:#ccc;cursor: pointer;"></span><!-- 忘记密码 -->
                            </CheckboxGroup>
                        </FormItem>
                        <FormItem>
                            <Button type="primary" @click="handleSubmit" style="width:100%;">登录</Button>
                        </FormItem>
                    </Form>
                </div>
            </div>
        </Row>
        <div class="footer">
            &copy;yongege
            <!--<div class="layout-content footer-content">-->
            <!--<dl>-->
            <!--<dt>关于我们</dt>-->
            <!--<dd>公司介绍</dd>-->
            <!--<dd>客户案例</dd>-->
            <!--<dd>上午合作</dd>-->
            <!--<dd>联系我们</dd>-->
            <!--</dl>-->
            <!--<dl>-->
            <!--<dt>产品资料</dt>-->
            <!--<dd>产品介绍</dd>-->
            <!--<dd>基础实用手册</dd>-->
            <!--<dd>管理员手册</dd>-->
            <!--<dd>官方指南</dd>-->
            <!--</dl>-->
            <!--<dl>-->
            <!--<dt>服务规则</dt>-->
            <!--<dd>服务条款</dd>-->
            <!--<dd>技术支持</dd>-->
            <!--</dl>-->
            <!--<dl>-->
            <!--<dt>下载test</dt>-->
            <!--<dd>下载客户端</dd>-->
            <!--<dd>企业注册test</dd>-->
            <!--</dl>-->
            <!--</div>-->
        </div>
    </div>
</template>

<script>
    import qs from 'qs'
    import ruleValidate from '../validator'
    export default {
        name: 'signIn',
        data() {
            return {
                formValidate: {
                    mobile: '',
                    possw: ''
                },
                auto: false
            }
        },
        computed:{
            ruleValidate:function () {
                return ruleValidate
            }
        },
        mounted() {
            this.auto = sessionStorage.getItem('auto') === 'true';
            if (sessionStorage.getItem('auto') === 'true') {
                this.formValidate.mobile = sessionStorage.getItem('mobile');
                this.formValidate.password = sessionStorage.getItem('password');
            }

        },
        methods: {
            toIndex(){
                this.$router.push('/home')
            },
            toSignIn() {
                this.$router.push('signIn')
            },
            handleSubmit() {
                this.$Loading.start();
                let data = qs.stringify({
                    'grant_type': 'password',
                    'client_id': '3',
                    'client_secret': 'Z2PIvGbdvcwAgvCoixzcV7uLLkBoZY0PFGKA8xK6',
                    username: this.formValidate.mobile,
                    password: this.formValidate.password,
                    scope: ''
                });
                this.$http.post(this.$api.TOKEN, data)
                    .then(res => {
                        console.log('login', res);
                        sessionStorage.setItem('access_token', res.data.access_token);
                        sessionStorage.setItem('refresh_token', res.data.refresh_token);
                        sessionStorage.setItem('token_type', "Bearer");
                        this.$http.defaults.headers.common['Authorization'] = 'Bearer ' + res.data.access_token;
//                      请求到token后进行登录
                        return this.$http.post(this.$api.LOGIN, {
                            mobile: this.formValidate.mobile,
                            password: this.formValidate.password,
                        })
                    }, err => {
                        this.$Message.error('请输入正确的用户名和密码');
                    })
                    .then(res => {
                        console.log('登陆', res);
                        if (res.data.status === '1') {
                            let userinfo = res.data.userinfo;
                            this.$store.dispatch('setUserInfo', userinfo);
                            this.$router.push('mains');
                            sessionStorage.setItem('auto', this.auto);
                            sessionStorage.setItem('com_id', userinfo.com_id);
                            //TODO 密码加密
                            sessionStorage.setItem('mobile', this.formValidate.mobile);
                            sessionStorage.setItem('password', this.formValidate.password);
                            this.$Loading.finish();
                        } else {
                            this.$Message.error('请输入正确的用户名和密码');
                            this.$Loading.error();
                        }
                    }, err => {
                        this.$Message.error('网络错误');
                    });
            }
        }
    }
</script>

<style scoped>

    .mianBox {
        width: 100%;
        height: 726px;
        margin: 0 auto;
        background-color: #3596F8;
    }

    .mianBox-content {
        width: 700px;
        height: 400px;
        background-color: #fff;
        position: relative;
        margin: auto;
        margin-top: 8%;
        border-radius: 6px;
    }

    .logoBox {
        width: 266px;
        height: 250px;
        position: absolute;
        right: 40px;
        top: 80px;
    }

    .ivu-checkbox-group {
        font-size: 12px;
    }

    /* .mianBox:hover {
		box-shadow: 2px 2px 5px #888888;
	} */
    .layout-content {
        width: 1080px;
        margin: 0 auto;
    }

    .layout .layout-top {
        width: 100%;
        height: 60px;
        margin-bottom: 0;
        background: #fff;
        border-bottom: 1px solid #ccc;
    }

    .layout-logo {
        width: 100px;
        height: 30px;
        background-image: url('../assets/logo.png');
        background-repeat: no-repeat;
        background-size: contain;
        /*background-color: #5b6270;*/
        border-radius: 3px;
        position: relative;
        top: 15px;
        left: 0px;
    }

    .loginIn {
        margin-top: 14px;
        margin-right: 0px;
    }

    .mains {
        background-color: #F9F9F9;
        padding-top: 20px;
        overflow: hidden;
    }

    .content {
        width: 680px;
        height: 650px;
        margin: 0 auto;
        background-color: #fff;
        border-radius: 4px;
        box-shadow: 2px 2px 2px #ccc;
        padding: 40px;
        margin-bottom: 30px;
    }

    .tittle-lv1 {
        text-align: center;
        font-weight: 600;
    }

    .content i {
        position: absolute;
        top: 28px;
        left: 10px;
        font-style: normal;
        color: #ccc;
    }

    .content p {
        font-size: 14px;
        font-weight: 600;
    }

    .footer {
        width: 100%;
        height: 188px;
        background-color: #303036;
        overflow: hidden;
        color: #ccc;
        font-size: 14px;
        display: flex;
        flex-flow: row nowrap;
        justify-content: center;
        align-items: center;

    }

    .footer-content {
        width: 30%;
        margin: 20px auto;
        display: flex;
        flex-flow: row nowrap;
        justify-content: space-around;
    }

    .mTop {
        padding: 26px 74px;
        padding-bottom: 0;
        border-bottom: 1px solid #DCE1E6;
    }

    .layout-content dl {
        display: inline-block;
        width: 15%;
        vertical-align: top;
        line-height: 2;
    }

    .layout-content dt {
        color: #fff;
        font-size: 14px;
    }

    .layout-content dd {
        color: #aaa;
    }

    .loginPic {
        position: absolute;
        bottom: -70px;
        left: -452px;
    }
</style>
