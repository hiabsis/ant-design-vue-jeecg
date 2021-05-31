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
      <a-button type="primary" icon="download" @click="handleExportXls('采购计划表')">导出</a-button>
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
                <a-popconfirm title="确定同意吗?" @confirm="() => handleApply(record)">
                  <a>同意</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <good-order-plane-modal ref="modalForm" @ok="modalFormOk"></good-order-plane-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import GoodOrderPlaneModal from './modules/GoodOrderPlaneModal'
  import {httpAction} from "@api/manage";

  export default {
    name: 'GoodOrderPlaneList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      GoodOrderPlaneModal
    },
    data () {
      return {
        description: '采购计划表管理页面',
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
            title:'克 重',
            align:"center",
            dataIndex: 'weight'
          },
          {
            title:'规    格',
            align:"center",
            dataIndex: 'grand'
          },
          {
            title:'出厂价',
            align:"center",
            dataIndex: 'price'
          },
          {
            title:'运 费',
            align:"center",
            dataIndex: 'transportatioExpenses'
          },
          {
            title:'总 价',
            align:"center",
            dataIndex: 'totalPrice'
          },
          {
            title:'标准售价',
            align:"center",
            dataIndex: 'standardPrice'
          },
          {
            title:'会员售价',
            align:"center",
            dataIndex: 'memberPrice'
          },
          {
            title:'最低库存',
            align:"center",
            dataIndex: 'minStock'
          },
          {
            title:'最高 库存',
            align:"center",
            dataIndex: 'maxStock'
          },
          {
            title:'供应商',
            align:"center",
            dataIndex: 'seller'
          },
          {
            title:'框架合同号',
            align:"center",
            dataIndex: 'contractId'
          },
          {
            title:'合同日期',
            align:"center",
            dataIndex: 'contractData',
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
          list: "/order/goodOrderPlane/list",
          delete: "/order/goodOrderPlane/delete",
          deleteBatch: "/order/goodOrderPlane/deleteBatch",
          exportXlsUrl: "/order/goodOrderPlane/exportXls",
          importExcelUrl: "order/goodOrderPlane/importExcel",

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
        httpAction("/order/goodOrderPlane/apply",record,"PUT").then((res) => {
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
        fieldList.push({type:'string',value:'weight',text:'克 重',dictCode:''})
        fieldList.push({type:'string',value:'grand',text:'规    格',dictCode:''})
        fieldList.push({type:'string',value:'price',text:'出厂价',dictCode:''})
        fieldList.push({type:'string',value:'transportatioExpenses',text:'运 费',dictCode:''})
        fieldList.push({type:'string',value:'totalPrice',text:'总 价',dictCode:''})
        fieldList.push({type:'string',value:'standardPrice',text:'标准售价',dictCode:''})
        fieldList.push({type:'string',value:'memberPrice',text:'会员售价',dictCode:''})
        fieldList.push({type:'string',value:'minStock',text:'最低库存',dictCode:''})
        fieldList.push({type:'string',value:'maxStock',text:'最高 库存',dictCode:''})
        fieldList.push({type:'string',value:'seller',text:'供应商',dictCode:''})
        fieldList.push({type:'string',value:'contractId',text:'框架合同号',dictCode:''})
        fieldList.push({type:'date',value:'contractData',text:'合同日期'})
        fieldList.push({type:'string',value:'status',text:'状态',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>