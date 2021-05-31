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
      <a-button type="primary" icon="download" @click="handleExportXls(' 进仓验货单')">导出</a-button>
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

              <a-menu-item>
                <a-popconfirm title="确定通过吗?" @confirm="() => handleApply(record)">
                  <a>通过</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <entering-warehouse-status-modal ref="modalForm" @ok="modalFormOk"></entering-warehouse-status-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import EnteringWarehouseStatusModal from './modules/EnteringWarehouseStatusModal'
  import {httpAction} from "@api/manage";
  export default {
    name: 'EnteringWarehouseStatusList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      EnteringWarehouseStatusModal
    },
    data () {
      return {
        description: ' 进仓验货单管理页面',
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
            title:'规  格',
            align:"center",
            dataIndex: 'grand'
          },
          {
            title:'单 位',
            align:"center",
            dataIndex: 'unit'
          },
          {
            title:'纸厂标签重量',
            align:"center",
            dataIndex: 'lableWeigth'
          },
          {
            title:'件  号',
            align:"center",
            dataIndex: 'entingGoodsId'
          },
          {
            title:'库位',
            align:"center",
            dataIndex: 'bin'
          },
          {
            title:'供应商简称',
            align:"center",
            dataIndex: 'seller'
          },
          {
            title:'采购单号	',
            align:"center",
            dataIndex: 'oderId'
          },
          {
            title:'进仓单号	',
            align:"center",
            dataIndex: 'entingWarehouseId'
          },
          {
            title:'进仓日期	',
            align:"center",
            dataIndex: 'warehouseDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'签收人员',
            align:"center",
            dataIndex: 're'
          },
          {
            title:'验货情况',
            align:"center",
            dataIndex: 'signeInfo'
          },
          {
            title:'状态',
            align:"center",
            dataIndex: 'status'
          },
          {
            title:'申请环节',
            align:"center",
            dataIndex: 'link'
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
          list: "/enter/enteringWarehouseStatus/list",
          delete: "/enter/enteringWarehouseStatus/delete",
          deleteBatch: "/enter/enteringWarehouseStatus/deleteBatch",
          exportXlsUrl: "/enter/enteringWarehouseStatus/exportXls",
          importExcelUrl: "enter/enteringWarehouseStatus/importExcel",

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
      handleApply(record){
        console.log("同意",record);
        const that = this
        httpAction("/enter/enteringWarehouseStatus/apply",record,"PUT").then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.loadData();
          } else {
            that.$message.warning(res.message);
          }
        }).finally(() => {
          that.loading = false;
        });
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'brand',text:'品牌',dictCode:''})
        fieldList.push({type:'string',value:'kind',text:'纸种',dictCode:''})
        fieldList.push({type:'string',value:'level',text:'级 别',dictCode:''})
        fieldList.push({type:'string',value:'weight',text:'克重',dictCode:''})
        fieldList.push({type:'string',value:'grand',text:'规  格',dictCode:''})
        fieldList.push({type:'string',value:'unit',text:'单 位',dictCode:''})
        fieldList.push({type:'string',value:'lableWeigth',text:'纸厂标签重量',dictCode:''})
        fieldList.push({type:'string',value:'entingGoodsId',text:'件  号',dictCode:''})
        fieldList.push({type:'string',value:'bin',text:'库位',dictCode:''})
        fieldList.push({type:'string',value:'seller',text:'供应商简称',dictCode:''})
        fieldList.push({type:'string',value:'oderId',text:'采购单号	',dictCode:''})
        fieldList.push({type:'string',value:'entingWarehouseId',text:'进仓单号	',dictCode:''})
        fieldList.push({type:'date',value:'warehouseDate',text:'进仓日期	'})
        fieldList.push({type:'string',value:'re',text:'签收人员',dictCode:''})
        fieldList.push({type:'string',value:'signeInfo',text:'验货情况',dictCode:''})
        fieldList.push({type:'string',value:'status',text:'状态',dictCode:''})
        fieldList.push({type:'string',value:'link',text:'申请环节',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>