<!--
*   
*   ページ： エージェントを探すview
*   説明： コンテナ履歴の情報を検索する
*
-->
<template>
    <a-drawer
        :title="title"
        :width="drawerWidth"
        :maskClosable="true"
        @close="handleCancel"
        :visible="visible"
        :confirmLoading="confirmLoading"
        :wrapStyle="{height: 'calc(100% - 108px)',overflow: 'auto',paddingBottom: '108px'}">

        <div :style="{width: '100%',padding: '10px 16px',background: '#fff',}">
            <a-spin :spinning="confirmLoading">
                <a-form :form="form" class="standard-form">
                    <!-- コンテナ -->
                    <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    :label="this.$t('app.CtmChc01D01.10000')">
                    <div v-decorator="['container', validatorRules.container]" style="width:150px"> 
                        <a-input type="primary" style="width:150px" v-model="container" placeholder="00001" />
                    </div>
                    </a-form-item>

                    <!-- フライト日 -->
                    <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    :label="this.$t('app.CtmChc01D01.10001')">
                    <div v-decorator="['flightday', validatorRules.flightday]" style="width:160px"> 
                        <a-date-picker
                            style="width: 160px" />
                    </div>
                    </a-form-item>

                </a-form>
            </a-spin>
        </div>
        
        <div class="fill-drawer-bottom"></div>
        <div style="z-index:999 " class="drawer-bootom-button">
            <a-button @click="resetClick" icon="reload" style="margin-left: 8px">{{this.$t('app.common.reload')}}</a-button>

            <a-button @click="handleOk" type="primary" :loading="confirmLoading" :disabled="disableSubmit" icon="search">{{this.$t('app.common.search')}}</a-button>    
        </div>

    </a-drawer>
</template>

<script>
    import pick from 'lodash.pick'
    export default {
        name: "CtmChc01D03",
        components: {
                
        },
        data () {
            return {
                drawerWidth: 600,
                title:this.$t('app.common.search'),
                addTitle: this.$t('app.common.add'),
                editTitle: this.$t('app.common.edit'),
                visible: false,
                disableSubmit:false,
                container:"",
                flightday:"",
                model: {},
                
                labelCol: {
                    xs: { span: 24 },
                    sm: { span: 5 },
                },
                wrapperCol: {
                    xs: { span: 24 },
                    sm: { span: 19 },
                },

                confirmLoading: false,
                form: this.$form.createForm(this),

                iconChooseVisible: false,
                validateStatus:""
            }
        },
        computed:{
            validatorRules:function() {
                return {
                    container: { rules: [{ required: true, message: this.$t('app.message.msg0001',{'0': this.$t('app.CtmChc01D01.10000')})}]},
                    flightday: { rules: [{ required: true, message: this.$t('app.message.msg0001',{'0': this.$t('app.CtmChc01D01.10001')})}]},
                }
            }
        },
        methods: {
            edit (record) {
                this.resetScreenSize(); 
                this.form.resetFields();
                this.model = Object.assign({}, record);
                this.visible = true;
                console.log(record);

            },
            close () {
                this.visible = false;
            },
            handleOk () {
                this.$emit('ok', this.model);
                this.close();
            },
            handleCancel () {
                this.close();
            },
            handleButtonClick () {

            },
            resetScreenSize(){
                let screenWidth = document.body.clientWidth;
                if(screenWidth < 400){
                    this.drawerWidth = screenWidth;
                }else{
                    this.drawerWidth = 600;
                }
            },

            resetClick(){
                this.container = "";
                this.flightday = "";
            },

        }
    }
</script>