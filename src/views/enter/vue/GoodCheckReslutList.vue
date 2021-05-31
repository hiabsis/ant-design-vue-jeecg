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
      <a-button type="primary" icon="download" @click="handleExportXls('货物检验单结果')">导出</a-button>
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

    <good-check-reslut-modal ref="modalForm" @ok="modalFormOk"></good-check-reslut-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GoodCheckReslutModal from './modules/GoodCheckReslutModal'

  export default {
    name: 'GoodCheckReslutList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      GoodCheckReslutModal
    },
    data () {
      return {
        description: '货物检验单结果管理页面',
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
            dataIndex: 'unit'
          },
          {
            title:'规格',
            align:"center",
            dataIndex: 'guige'
          },
          {
            title:'订货数量',
            align:"center",
            dataIndex: 'orderNumber'
          },
          {
            title:'件号',
            align:"center",
            dataIndex: 'jianhao'
          },
          {
            title:'幅宽',
            align:"center",
            dataIndex: 'fukuan'
          },
          {
            title:'纸张破损',
            align:"center",
            dataIndex: 'distoryPaper'
          },
          {
            title:'克重',
            align:"center",
            dataIndex: 'unitUnit'
          },
          {
            title:'外包损耗',
            align:"center",
            dataIndex: 'reduce'
          },
          {
            title:'短重',
            align:"center",
            dataIndex: 'shortWeight'
          },
          {
            title:'水分',
            align:"center",
            dataIndex: 'shuifen'
          },
          {
            title:'筒芯重量',
            align:"center",
            dataIndex: 'tonxinzhonglaing'
          },
          {
            title:'合计',
            align:"center",
            dataIndex: 'toatal'
          },
          {
            title:'采购单号',
            align:"center",
            dataIndex: 'oderId'
          },
          {
            title:'供应商',
            align:"center",
            dataIndex: 'seller'
          },
          {
            title:'进仓日期',
            align:"center",
            dataIndex: 'entingData'
          },
          {
            title:'进仓单号',
            align:"center",
            dataIndex: 'enteringId'
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
          list: "/enter/goodCheckReslut/list",
          delete: "/enter/goodCheckReslut/delete",
          deleteBatch: "/enter/goodCheckReslut/deleteBatch",
          exportXlsUrl: "/enter/goodCheckReslut/exportXls",
          importExcelUrl: "enter/goodCheckReslut/importExcel",
          
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
        fieldList.push({type:'string',value:'unit',text:'克重',dictCode:''})
        fieldList.push({type:'string',value:'guige',text:'规格',dictCode:''})
        fieldList.push({type:'string',value:'orderNumber',text:'订货数量',dictCode:''})
        fieldList.push({type:'string',value:'jianhao',text:'件号',dictCode:''})
        fieldList.push({type:'string',value:'fukuan',text:'幅宽',dictCode:''})
        fieldList.push({type:'string',value:'distoryPaper',text:'纸张破损',dictCode:''})
        fieldList.push({type:'string',value:'unitUnit',text:'克重',dictCode:''})
        fieldList.push({type:'string',value:'reduce',text:'外包损耗',dictCode:''})
        fieldList.push({type:'string',value:'shortWeight',text:'短重',dictCode:''})
        fieldList.push({type:'string',value:'shuifen',text:'水分',dictCode:''})
        fieldList.push({type:'string',value:'tonxinzhonglaing',text:'筒芯重量',dictCode:''})
        fieldList.push({type:'string',value:'toatal',text:'合计',dictCode:''})
        fieldList.push({type:'string',value:'oderId',text:'采购单号',dictCode:''})
        fieldList.push({type:'string',value:'seller',text:'供应商',dictCode:''})
        fieldList.push({type:'string',value:'entingData',text:'进仓日期',dictCode:''})
        fieldList.push({type:'string',value:'enteringId',text:'进仓单号',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>