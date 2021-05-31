<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('退货表')">导出</a-button>
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

    <return-receipt-modal ref="modalForm" @ok="modalFormOk"></return-receipt-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import ReturnReceiptModal from './modules/ReturnReceiptModal'

  export default {
    name: 'ReturnReceiptList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      ReturnReceiptModal
    },
    data () {
      return {
        description: '退货表管理页面',
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
            title:'品牌',
            align:"center",
            dataIndex: 'brand'
          },
          {
            title:'纸种',
            align:"center",
            dataIndex: 'kind'
          },
          {
            title:'级 别',
            align:"center",
            dataIndex: 'level'
          },
          {
            title:'克重',
            align:"center",
            dataIndex: 'weight'
          },
          {
            title:'规 格',
            align:"center",
            dataIndex: 'grand'
          },
          {
            title:'单位',
            align:"center",
            dataIndex: 'unit'
          },
          {
            title:'进仓数量',
            align:"center",
            dataIndex: 'amount'
          },
          {
            title:'件 数',
            align:"center",
            dataIndex: 'number'
          },
          {
            title:'库 位',
            align:"center",
            dataIndex: 'bin'
          },
          {
            title:'件号',
            align:"center",
            dataIndex: 'acc'
          },
          {
            title:'供应商简称',
            align:"center",
            dataIndex: 'seller'
          },
          {
            title:'进仓单号	',
            align:"center",
            dataIndex: 'enterId'
          },
          {
            title:'进仓日期',
            align:"center",
            dataIndex: 'enterData',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'管理人员',
            align:"center",
            dataIndex: 'user'
          },
          {
            title:'签收时间	',
            align:"center",
            dataIndex: 'data',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status'
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
          list: "/enter/returnReceipt/list",
          delete: "/enter/returnReceipt/delete",
          deleteBatch: "/enter/returnReceipt/deleteBatch",
          exportXlsUrl: "/enter/returnReceipt/exportXls",
          importExcelUrl: "enter/returnReceipt/importExcel",
          
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
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'brand',text:'品牌',dictCode:''})
        fieldList.push({type:'string',value:'kind',text:'纸种',dictCode:''})
        fieldList.push({type:'string',value:'level',text:'级 别',dictCode:''})
        fieldList.push({type:'string',value:'weight',text:'克重',dictCode:''})
        fieldList.push({type:'string',value:'grand',text:'规 格',dictCode:''})
        fieldList.push({type:'string',value:'unit',text:'单位',dictCode:''})
        fieldList.push({type:'string',value:'amount',text:'进仓数量',dictCode:''})
        fieldList.push({type:'string',value:'number',text:'件 数',dictCode:''})
        fieldList.push({type:'string',value:'bin',text:'库 位',dictCode:''})
        fieldList.push({type:'string',value:'acc',text:'件号',dictCode:''})
        fieldList.push({type:'string',value:'seller',text:'供应商简称',dictCode:''})
        fieldList.push({type:'string',value:'enterId',text:'进仓单号	',dictCode:''})
        fieldList.push({type:'date',value:'enterData',text:'进仓日期'})
        fieldList.push({type:'string',value:'user',text:'管理人员',dictCode:''})
        fieldList.push({type:'date',value:'data',text:'签收时间	'})
        fieldList.push({type:'string',value:'status',text:'状态',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>