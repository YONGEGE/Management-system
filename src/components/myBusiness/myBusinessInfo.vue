<template>
    <div class="conRight">
        <p class="tittle-lv1">企业信息</p>
        <Form :model="formLeft" label-position="left" :label-width="130">
            <FormItem label="企业logo" class="logoBox">
                <div class="logo">
                    <img :src="option1.cropedImg" alt="" style="height: 100%;">
                </div>
                <div class="logoCover" @click="modal1=true">
                    <Icon type="camera"></Icon>
                </div>
                <i>推荐尺寸702px*180px</i>
            </FormItem>
            <FormItem label="企业简称" class="lines">
                {{comInfo.short_name}} <a @click="comNameModal=true">修改</a>
            </FormItem>
            <FormItem label="企业成员">
                <p>{{comInfo.members}}个成员</p>
            </FormItem>
            <FormItem label="企业主体类型">
                <p>{{comInfo.business_scope}}<a @click="scopeModal=true">修改</a></p>
            </FormItem>
            <!--<FormItem label="企业部门" class="lines">-->
            <!--<p>一个部门 </p>-->
            <!--</FormItem>-->
            <FormItem label="企业全称">
                <p>{{comInfo.com_name}}</p>
            </FormItem>
            <FormItem label="创建时间">
                <p>{{comInfo.created_at}}</p>
            </FormItem>
            <FormItem label="售电业务经营范围">
                {{comInfo.province}}
                <a @click="saleAreaModal=true" v-if="comInfo.province">修改</a>
                <a @click="saleAreaModal=true" v-else>增加</a>
            </FormItem>
            <FormItem label="售电联系电话" class="lines">
                <p>{{comInfo.mobile}}
                    <a @click="saleNumModal=true" v-if="comInfo.mobile">修改</a>
                    <a @click="saleNumModal=true" v-else>增加</a>
                </p>
            </FormItem>
            <FormItem label="发票抬头">
                <p>
                    {{comInfo.fapiao_name}}
                    <a @click="toAddInvoice()">修改</a>
                    <!--<i>为企业成员配置增值税发票抬头</i>-->
                </p>
            </FormItem>
            <FormItem label="企业联系电话">
                <p>
                    {{comInfo.sell_tel}}
                    <a @click="businessNumModal=true" v-if="comInfo.sell_tel">修改</a>
                    <a @click="businessNumModal=true" v-else>增加</a>
                </p>
            </FormItem>
            <FormItem label="企业地址">
                <p>
                    {{comInfo.province}}省—  {{comInfo.city}}市 —{{comInfo.address}}
                    <a @click="businessAdressModal=true">修改</a>
                </p>
            </FormItem>
        </Form>
        <Modal
            v-model="modal1"
            title="编辑企业Logo"
            @on-ok="ok"
            @on-cancel="cancel">
            <Row :gutter="10">
                <Col span="14">
                <div class="cropper">
                    <img id="cropimg1" alt="">
                </div>
                </Col>
                <Col span="10">
                <Row type="flex" justify="center" align="middle" class="image-edito">
                    <div id="preview1" class="previews"></div>
                </Row>
                <div>
                    <input type="file" accept="image/png, image/jpeg, image/gif, image/jpg" @change="handleChange1"
                           id="fileinput1" class="fileinput" style="display:none;"/>
                    <label class="filelabel" for="fileinput1">
                        <Icon type="image"></Icon>&nbsp;选择图片</label>
                    <!-- <span><Button @click="handlecrop1" type="primary" icon="crop">裁剪</Button></span> -->
                </div>
                <!-- <Modal v-model="option1.showCropedImage">
					<p slot="header">预览裁剪之后的图片</p>
					<img :src="option1.cropedImg" alt="" v-if="option1.showCropedImage" style="width: 100%;">
				</Modal> -->
                </Col>
            </Row>
        </Modal>
        <Modal
            v-model="comNameModal"
            title="公司简称"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.short_name" placeholder="请输入公司简称" style="width: 452px"></Input>
        </Modal>
        <Modal
            v-model="saleAreaModal"
            title="售电业务经营范围"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.province" style="width: 452px"></Input>
        </Modal>
        <Modal
            v-model="saleNumModal"
            title="售电联系电话"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.mobile" style="width: 452px"></Input>
        </Modal>
        <Modal
            v-model="scopeModal"
            title="企业主体类型"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.business_scope" style="width: 452px"></Input>
        </Modal>
        <Modal
            v-model="businessNumModal"
            title="企业联系电话"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.sell_tel" style="width: 452px"></Input>
        </Modal>
        <Modal
            v-model="businessAdressModal"
            title="企业地址"
            @on-ok="comModify"
            @on-cancel="comNamecancel">
            <Input v-model="comInfo.province" style="width: 452px"></Input>
            <Input v-model="comInfo.city" style="width: 452px"></Input>
            <Input v-model="comInfo.address" style="width: 452px"></Input>
        </Modal>

    </div>
</template>

<script>
    //截图文件样式引入
    import Cropper from 'cropperjs';
    import './cropper.min.css';

    export default {
        name: 'myBusinessInfo',
        data() {
            return {
                comInfo: {},
                comNameModal: false,
                saleAreaModal: false,
                saleNumModal: false,
                businessNumModal: false,
                businessAdressModal: false,
                scopeModal: false,
                cropper1: {},
                option1: {
                    showCropedImage: false,
                    cropedImg: ''
                },
                modal1: false,
                formLeft: {
                    input1: '',
                    input2: '',
                    input3: '',
                },
                formData: null
            }
        },
        methods: {
            comModify() {
                console.log(this.comInfo);
                this.$http.post(this.$api.COM_UPDATE, {
                    id: this.comInfo.id,
                    com_name: this.comInfo.com_name,
                    short_name: this.comInfo.short_name,
                    business_scope: this.comInfo.business_scope,
                    sell_tel: this.comInfo.sell_tel,
                    mobile: this.comInfo.mobile,
                    province: this.comInfo.province,
                    city: this.comInfo.city,
                    county: this.comInfo.county,
                    address: this.comInfo.address,
                }).then(res => {
                    console.log('公司信息修改', res);

                }, err => {
                    this.$api.errcallback(err)
                });
            },
            getComInfo() {
                this.$http.get(this.$api.COM_INFO + '\/' + this.$store.getters.userInfo.com_id).then(res => {
                    console.log('公司信息', res);
                    Object.assign(this.comInfo, res.data.data);
                    this.option1.cropedImg = 'http://39.106.106.150/' + this.comInfo.logo;
                    this.$store.dispatch('setComInfo', res.data.data)
                }, err => {
                    this.$api.errcallback(err)
                });
            },
            comNamecancel() {
                this.$Message.info('点击了取消');
            },
            ok() {
                this.$Message.info('点击了确定');
                this.handlecrop1();
            },
            cancel() {
                this.$Message.info('点击了取消');
            },
            handleChange1(e) {
                let file = e.target.files[0];
                let reader = new FileReader();
                reader.onload = () => {
                    this.cropper1.replace(reader.result);
                    reader.onload = null;
                };
                reader.readAsDataURL(file);
                
                console.log(file);
                this.formData = new FormData();
                this.formData.append('file', file);
            },
            handlecrop1() {
                this.option1.cropedImg = this.cropper1.getCroppedCanvas().toDataURL();
                this.option1.showCropedImage = true;
                // console.log(this.option1.cropedImg);
                //base64转换为图片格式
                var arr = this.option1.cropedImg.split(','), mime = arr[0].match(/:(.*?);/)[1],
                bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n);
                while(n--){
                    u8arr[n] = bstr.charCodeAt(n);
                }
                var obj = new Blob([u8arr], {type:mime});
                var fd = new FormData();
                fd.append("upfile", obj,"image.png");
                
                this.$http.post(this.$api.UPLOAD_IMG, fd)
                    .then(res => {
                        console.log('上传图片', res);
                        this.comInfo.logo = res.data.fileid;
                        return this.$http.post(this.$api.COM_UPDATE, {
                            id: this.comInfo.id,
                            logo: this.comInfo.logo
                        })
                    }, err => {
                        this.$api.errcallback(err)
                    })
                    .then(res => {
                        console.log('公司信息修改', res);

                    }, err => {
                        this.$api.errcallback(err)
                    });
            },
            toAddInvoice() {
                this.$router.push('/addInvoice')
            }
        },
        beforeMount() {
            Object.assign(this.comInfo, this.$store.getters.comInfo);
        },
        mounted() {
            this.getComInfo();
            // 图片剪切初始化
            let img1 = document.getElementById('cropimg1');
            this.cropper1 = new Cropper(img1, {
                dragMode: 'move',
                preview: '#preview1',
                aspectRatio: 3.9,
                restore: false,
                center: false,
                highlight: false,
                cropBoxMovable: false,
                toggleDragModeOnDblclick: false
            });
        }
    }
</script>

<style scoped>
    .conRight {
        padding: 10px 26px;
    }

    .red {
        color: red;
    }

    form div i {
        font-size: 12px;
        font-style: normal;
        color: #787878;
    }

    .logoBox {
        position: relative;
    }

    .logo {
        width: 236px;
        height: 62px;
        border: 1px solid #DCE1E6;
        overflow: hidden;
    }

    .ivu-form-item {
        margin-bottom: 0;
    }

    .lines {
        padding-bottom: 20px;
        margin-bottom: 20px;
        border-bottom: 1px solid #DCE1E6;
    }

    .logoCover {
        display: none;
        width: 236px;
        height: 62px;
        position: absolute;
        top: 0;
        left: 0;
        background: rgba(0, 0, 0, .3);
        cursor: pointer;
        text-align: center;
    }

    .logoBox:hover .logoCover {
        display: block;
    }

    .logoCover i {
        color: #fff;
        font-size: 20px;
        line-height: 60px;
    }

    .image-edito {
        width: 200px;
        height: 88px;
        overflow: hidden;
        background-color: #e9eaeb;
    }

    .image-edito div {
        width: 100%;
        height: 100%;
    }

    /* 图片剪切样式修改 */
    .cropper {
        height: 200px;
        border: 1px solid #DCE1E6;
    }

    .filelabel {
        display: inline-block;
        width: 80px;
        height: 32px;
        line-height: 32px;
        background-color: #2d8cf0;
        text-align: center;
        border-radius: 2px;
        color: #fff;
        position: absolute;
        bottom: -176px;
        left: -284px;
    }

    .previews {
        overflow: hidden;
    }
</style>
