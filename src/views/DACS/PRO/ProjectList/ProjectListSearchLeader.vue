<!--
*   
*   ページ： 项目负责人選択view
*   説明： 用于项目负责人選択搜索数据
*
-->

<template>
    <a-modal
        :title="title"
        :width="800"
        :visible="visible"
        :confirmLoading="confirmLoading"
        @ok="handleOk"
        @cancel="handleCancel"
        :cancelText="$t('app.common.close')">
        <template slot="footer">
            <a-button @click="handleCancel">{{this.$t('app.common.close')}}</a-button>
        </template>

        <div class="table-page-search-wrapper" style="margin-bottom: 15px">
            <a-form layout="inline" @keyup.enter.native="searchQuery">

                <a-row :gutter="24" >
                    <a-col :md="12" :sm="8">
                        <a-form-item :label="$t('app.ProjectList.10012')"> 
                            <a-input :value="selectPjLeaderName" style="width: 150px;"></a-input>
                        </a-form-item>
                    </a-col>
                    <a-col :md="4" :sm="8" class="row-right-model-button">
                        <a-button type="primary" @click="searchQuery" icon="search">{{this.$t('app.common.search')}}</a-button>
                    </a-col>
                </a-row>
            </a-form>
        </div>

        <!-- table-begin -->
        <div>
            <a-table
                ref="table"
                bordered
                size="middle"
                :rowKey="(record,index)=>{return index}"
                :columns="columns"
                :dataSource="dataSource"
                :pagination="ipagination"
                :loading="loading"
                :rowClassName="InterlaceDiscoloration"
                @change="handleTableChange">
                <span slot = "selectNumb" slot-scope="text, record" style="cursor:pointer;text-decoration: none;font-family: 'Yu Gothic Medium';font-weight: bold;color: #5D7DA9;"> 
                    <a  @click="selectRow(record)" > ◆</a>
                </span>;
            </a-table>
        </div>
        <!-- table-end -->
    </a-modal>
</template>

<script>
    import pick from 'lodash.pick'
    import {addDictItem, editDictItem} from '@/api/api'
    import { getMsgInfo } from "@/utils/util"
    import {getAction} from '@/api/manage';
    import {frozenBatch} from '@/api/api'
    import {JeecgListMixin} from '@/mixins/JeecgListMixin'
    import JInput from '@/components/jeecg/JInput'

    export default {
        name: "ProjectListSearchLeader",
        mixins: [JeecgListMixin],
        components: {
            JInput,
        },
        data() {
            return {
                title: this.$t('app.ProjectList.10012'),
                visible: false,
                visibleCheck: true,
                selectPjLeaderName:"",
                model: {},
                // dictId: "",
                status: 1,
                labelCol: {
                    xs: {span: 24},
                    sm: {span: 5},
                },
                wrapperCol: {
                    xs: {span: 24},
                    sm: {span: 16},
                },
                confirmLoading: false,
                form: this.$form.createForm(this),
                validatorRules: {
                    itemText: {rules: [{required: true, message: getMsgInfo(this,'msg0001','itemText') }]},
                    itemValue: {rules: [{required: true, message: getMsgInfo(this,'msg0001','itemValue') }]},
                },
                columns:[
                    {
                        title: 'No',
                        dataIndex: '',
                        key:'rowIndex',
                        width:40,
                        align:"center",
                        customRender:function (t,r,index) {
                            return parseInt(index)+1;
                        }
                    },
                    {
                        title: this.$t('app.common.select'),
                        width:50,
                        align:"center",
                        scopedSlots: {customRender: 'selectNumb'},
                    },
                    {
                        // 项目负责人
                        title: this.$t('app.ProjectList.10006'),
                        align: "center",
                        dataIndex: 'pjLeaderName',
                        width:150
                    },
                ],
                url: {
                    list: "/sysp/projectList/list",
                },
            }
        },
        created() {
        },
        methods: {
            
            add(pjLeaderName) {
                this.selectPjLeaderName = pjLeaderName;
                this.edit({});
            },
            
            edit(record) {
                if (record.id) {
                    // this.dictId = record.dictId;
                    this.visibleCheck = (record.status == 1) ? true : false;
                }
                this.form.resetFields();
                this.model = Object.assign({}, record);
                this.visible = true;
                
                
            },

            handleOk() {
                this.close();
            },

            handleCancel() {
                this.close();
            },
            close() {
                this.$emit('close');
                this.visible = false;
            },
            onSearch(){

            },
            selectRow(record){
                this.$emit("ok",record.pjLeaderName);
                this.close();
            }
        }
    }
</script>