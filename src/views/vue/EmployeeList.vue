<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="员工姓名">
              <a-input placeholder="请输入员工姓名" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('employee')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <employee-modal ref="modalForm" @ok="modalFormOk"></employee-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import EmployeeModal from './modules/EmployeeModal'
  import { loadCategoryData } from '@/api/api'

  export default {
    name: 'EmployeeList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      EmployeeModal
    },
    data () {
      return {
        description: 'employee管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'员工姓名',
            align:"center",
            dataIndex: 'name'
          },
          {
            title:'性别',
            align:"center",
            dataIndex: 'gender_dictText'
          },
          {
            title:'出生日期',
            align:"center",
            dataIndex: 'birthday',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'身份证号',
            align:"center",
            dataIndex: 'idcard'
          },
          {
            title:'婚姻状况',
            align:"center",
            dataIndex: 'wedlock_dictText'
          },
          {
            title:'民族',
            align:"center",
            dataIndex: 'nationid_dictText'
          },
          {
            title:'籍贯',
            align:"center",
            dataIndex: 'nativeplace'
          },
          {
            title:'政治面貌',
            align:"center",
            dataIndex: 'politicid_dictText'
          },
          {
            title:'邮箱',
            align:"center",
            dataIndex: 'email'
          },
          {
            title:'电话号码',
            align:"center",
            dataIndex: 'phone'
          },
          {
            title:'联系地址',
            align:"center",
            dataIndex: 'address'
          },
          {
            title:'所属部门',
            align:"center",
            dataIndex: 'departmentid',
            customRender: (text) => (text ? filterMultiDictText(this.dictOptions['departmentid'], text) : '')
          },
          {
            title:'职称ID',
            align:"center",
            dataIndex: 'joblevelid_dictText'
          },
          {
            title:'职位ID',
            align:"center",
            dataIndex: 'posid_dictText'
          },
          {
            title:'聘用形式',
            align:"center",
            dataIndex: 'engageform_dictText'
          },
          {
            title:'最高学历',
            align:"center",
            dataIndex: 'tiptopdegree_dictText'
          },
          {
            title:'所属专业',
            align:"center",
            dataIndex: 'specialty'
          },
          {
            title:'毕业院校',
            align:"center",
            dataIndex: 'school'
          },
          {
            title:'入职日期',
            align:"center",
            dataIndex: 'begindate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'在职状态',
            align:"center",
            dataIndex: 'workstate'
          },
          {
            title:'工号',
            align:"center",
            dataIndex: 'workid'
          },
          {
            title:'合同期限',
            align:"center",
            dataIndex: 'contractterm'
          },
          {
            title:'转正日期',
            align:"center",
            dataIndex: 'conversiontime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'离职日期',
            align:"center",
            dataIndex: 'notworkdate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'合同起始日期',
            align:"center",
            dataIndex: 'begincontract',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'合同终止日期',
            align:"center",
            dataIndex: 'endcontract',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'工龄',
            align:"center",
            dataIndex: 'workage'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/employee/employee/list",
          delete: "/employee/employee/delete",
          deleteBatch: "/employee/employee/deleteBatch",
          exportXlsUrl: "/employee/employee/exportXls",
          importExcelUrl: "employee/employee/importExcel",
          
        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
        loadCategoryData({code:''}).then((res) => {
          if (res.success) {
            this.$set(this.dictOptions, 'departmentid', res.result)
          }
        })
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'name',text:'员工姓名',dictCode:''})
        fieldList.push({type:'string',value:'gender',text:'性别',dictCode:''})
        fieldList.push({type:'date',value:'birthday',text:'出生日期'})
        fieldList.push({type:'string',value:'idcard',text:'身份证号',dictCode:''})
        fieldList.push({type:'string',value:'wedlock',text:'婚姻状况',dictCode:''})
        fieldList.push({type:'int',value:'nationid',text:'民族',dictCode:''})
        fieldList.push({type:'string',value:'nativeplace',text:'籍贯',dictCode:''})
        fieldList.push({type:'int',value:'politicid',text:'政治面貌',dictCode:''})
        fieldList.push({type:'string',value:'email',text:'邮箱',dictCode:''})
        fieldList.push({type:'string',value:'phone',text:'电话号码',dictCode:''})
        fieldList.push({type:'string',value:'address',text:'联系地址',dictCode:''})
        fieldList.push({type:'int',value:'departmentid',text:'所属部门'})
        fieldList.push({type:'int',value:'joblevelid',text:'职称ID',dictCode:''})
        fieldList.push({type:'int',value:'posid',text:'职位ID',dictCode:''})
        fieldList.push({type:'string',value:'engageform',text:'聘用形式',dictCode:''})
        fieldList.push({type:'string',value:'tiptopdegree',text:'最高学历',dictCode:''})
        fieldList.push({type:'string',value:'specialty',text:'所属专业',dictCode:''})
        fieldList.push({type:'string',value:'school',text:'毕业院校',dictCode:''})
        fieldList.push({type:'date',value:'begindate',text:'入职日期'})
        fieldList.push({type:'string',value:'workstate',text:'在职状态',dictCode:''})
        fieldList.push({type:'string',value:'workid',text:'工号',dictCode:''})
        fieldList.push({type:'double',value:'contractterm',text:'合同期限',dictCode:''})
        fieldList.push({type:'date',value:'conversiontime',text:'转正日期'})
        fieldList.push({type:'date',value:'notworkdate',text:'离职日期'})
        fieldList.push({type:'date',value:'begincontract',text:'合同起始日期'})
        fieldList.push({type:'date',value:'endcontract',text:'合同终止日期'})
        fieldList.push({type:'int',value:'workage',text:'工龄',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>