<template>
  <a-card :bordered="false" class="card-area">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <!-- 搜索区域 -->
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <!-- 名称 -->
          <a-col :md="6" :sm="8">
            <a-form-item :label="$t('app.label.roleName')">
              <j-input  v-model="queryParam.roleName"></j-input>
            </a-form-item>
          </a-col>

          <!-- 创建时间 -->
          <!-- <a-col :md="10" :sm="12">
            <a-form-item :label="$t('app.label.createTime')" :labelCol="{span: 5}" :wrapperCol="{span: 18, offset: 1}">
              <j-date v-model="queryParam.createTime_begin" :showTime="true" date-format="YYYY-MM-DD HH:mm:ss" style="width:45%" ></j-date>
              <span style="width: 10px;">~</span>
              <j-date v-model="queryParam.createTime_end" :showTime="true" date-format="YYYY-MM-DD HH:mm:ss" style="width:45%"></j-date>
            </a-form-item>
          </a-col> -->
          <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
            <!-- 按钮部分 -->
            <a-col :md="6" :sm="24">
              <a-button type="primary" @click="searchQuery" icon="search">{{this.$t('app.common.search')}}</a-button>
              <a-button style="margin-left: 8px" @click="searchReset" icon="reload" type="primary">{{this.$t('app.common.reload')}}</a-button>
            </a-col>
          </span>
        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">{{this.$t('app.common.add')}}</a-button>
      <!-- <a-button type="primary" icon="download" @click="handleExportXls($t('app.label.roleInformation'))">{{this.$t('app.common.export')}}</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">{{this.$t('app.common.import')}}</a-button>
      </a-upload> -->

      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>{{this.$t('app.common.delete')}}</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          {{this.$t('app.common.bulkDelete')}} <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> {{this.$t('app.common.selected')}}&nbsp;<a style="font-weight: 600">{{ selectedRowKeys.length }}</a>{{this.$t('app.common.item')}}&nbsp;&nbsp;
        <a style="margin-left: 24px" @click="onClearSelected">{{this.$t('app.common.clear')}}</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :scroll="{x:1200}"
        :rowSelection="{fixed:true,selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        :rowClassName="InterlaceDiscoloration"
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
            <a @click="handleEdit(record)">
              <a-icon type="edit"/>編集</a>
          <a-divider type="vertical" />

          <a-dropdown>
            <a class="ant-dropdown-link" v-html="$t('app.common.more')"><a-icon type="down" />
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handlePerssion(record.id)" v-html="$t('app.common.authorize')"></a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm :title="$t('app.message.msg0036')" @confirm="() => handleDelete(record.id)">
                  <a v-html="$t('app.common.delete')"></a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>


      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <role-modal ref="modalForm" @ok="modalFormOk"></role-modal>
    <user-role-modal ref="modalUserRole"></user-role-modal>
  </a-card>
</template>

<script>
  import RoleModal from './modules/RoleModal'
  import UserRoleModal from './modules/UserRoleModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import JDate from '@/components/jeecg/JDate'
  import JInput from '@/components/jeecg/JInput'

  export default {
    name: "RoleList",
    mixins:[JeecgListMixin],
    components: {
      RoleModal,
      UserRoleModal,
      JDate,
      JInput
    },
    data () {
      return {

        description: this.$t('app.label.roleManagement'),
        // 查询条件
        queryParam: {roleName:'',},
        // 表头
        columns: [
          // {
          //   title: '#',
          //   dataIndex: '',
          //   key:'rowIndex',
          //   width:40,
          //   fixed:"left",
          //   align:"center",
          //   customRender:function (t,r,index) {
          //     return parseInt(index)+1;
          //   }
          // },
          {
            // 角色编码
            title: this.$t('app.label.roleCoding'),
            align:"left",
            dataIndex: 'roleCode',
            width: 140
          },
          {
            // 角色名称
            title: this.$t('app.label.roleName'),
            align:"left",
            dataIndex: 'roleName',
            width: 140,
          },
          {
            // 备注
            title: this.$t('app.label.memo'),
            align:"left",
            dataIndex: 'description'
          },
          {
            // 创建时间
            title: this.$t('app.label.createTime'),
            dataIndex: 'createTime',
            align:"left",
            sorter: true,
            width: 140
          },
          {
            // 更新时间
            title: this.$t('app.label.updateTime'),
            dataIndex: 'updateTime',
            align:"left",
            sorter: true,
            width: 140
          },
          {
            // 操作
            title: this.$t('app.label.action'),
            dataIndex: 'action',
            align:"center",
            width:140,
            fixed:"right",
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/sys/role/list",
          delete: "/sys/role/delete",
          deleteBatch: "/sys/role/deleteBatch",
          exportXlsUrl: "/sys/role/exportXls",
          importExcelUrl: "sys/role/importExcel",
        },
      }
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      handlePerssion: function(roleId){
       // alert(roleId);
        this.$refs.modalUserRole.show(roleId);
      },
      onChangeDate(date, dateString) {
        console.log(date, dateString);
      },
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>