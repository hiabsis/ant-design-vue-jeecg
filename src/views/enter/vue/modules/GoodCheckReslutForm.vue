<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-item label="品牌" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['brand']" placeholder="请输入品牌"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="纸种" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['kind']" placeholder="请输入纸种"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="级 别" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['level']" placeholder="请输入级 别"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="克重" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['unit']" placeholder="请输入克重"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="规格" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['guige']" placeholder="请输入规格"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="订货数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['orderNumber']" placeholder="请输入订货数量"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="件号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['jianhao']" placeholder="请输入件号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="幅宽" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['fukuan']" placeholder="请输入幅宽"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="纸张破损" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['distoryPaper']" placeholder="请输入纸张破损"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="克重" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['unitUnit']" placeholder="请输入克重"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="外包损耗" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['reduce']" placeholder="请输入外包损耗"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="短重" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['shortWeight']" placeholder="请输入短重"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="水分" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['shuifen']" placeholder="请输入水分"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="筒芯重量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['tonxinzhonglaing']" placeholder="请输入筒芯重量"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="合计" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['toatal']" placeholder="请输入合计"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="采购单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['oderId']" placeholder="请输入采购单号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="供应商" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['seller']" placeholder="请输入供应商"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="进仓日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['entingData']" placeholder="请输入进仓日期"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="进仓单号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['enteringId']" placeholder="请输入进仓单号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col v-if="showFlowSubmitButton" :span="24" style="text-align: center">
            <a-button @click="submitForm">提 交</a-button>
          </a-col>
        </a-row>
      </a-form>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'GoodCheckReslutForm',
    components: {
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: ()=>{},
        required: false
      },
      //表单模式：true流程表单 false普通表单
      formBpm: {
        type: Boolean,
        default: false,
        required: false
      },
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        form: this.$form.createForm(this),
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        validatorRules: {
        },
        url: {
          add: "/enter/goodCheckReslut/add",
          edit: "/enter/goodCheckReslut/edit",
          queryById: "/enter/goodCheckReslut/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return true
          }
        }
        return false
      }
    },
    created () {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData();
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'brand','kind','level','unit','guige','orderNumber','jianhao','fukuan','distoryPaper','unitUnit','reduce','shortWeight','shuifen','tonxinzhonglaing','toatal','oderId','seller','entingData','enteringId'))
        })
      },
      //渲染流程表单数据
      showFlowData(){
        if(this.formBpm === true){
          let params = {id:this.formData.dataId};
          getAction(this.url.queryById,params).then((res)=>{
            if(res.success){
              this.edit (res.result);
            }
          });
        }
      },
      submitForm () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
            })
          }
         
        })
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'brand','kind','level','unit','guige','orderNumber','jianhao','fukuan','distoryPaper','unitUnit','reduce','shortWeight','shuifen','tonxinzhonglaing','toatal','oderId','seller','entingData','enteringId'))
      },
    }
  }
</script>