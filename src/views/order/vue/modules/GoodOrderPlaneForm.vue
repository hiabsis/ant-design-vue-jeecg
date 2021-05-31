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
            <a-form-item label="克 重" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['weight']" placeholder="请输入克 重"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="规    格" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['grand']" placeholder="请输入规    格"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="出厂价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['price']" placeholder="请输入出厂价"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="运 费" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['transportatioExpenses']" placeholder="请输入运 费"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="总 价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['totalPrice']" placeholder="请输入总 价"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="标准售价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['standardPrice']" placeholder="请输入标准售价"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="会员售价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['memberPrice']" placeholder="请输入会员售价"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="最低库存" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['minStock']" placeholder="请输入最低库存"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="最高 库存" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['maxStock']" placeholder="请输入最高 库存"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="供应商" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['seller']" placeholder="请输入供应商"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="框架合同号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['contractId']" placeholder="请输入框架合同号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="合同日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择合同日期" v-decorator="['contractData']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['status']" placeholder="请输入状态"  ></a-input>
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
    name: 'GoodOrderPlaneForm',
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
          add: "/order/goodOrderPlane/add",
          edit: "/order/goodOrderPlane/edit",
          queryById: "/order/goodOrderPlane/queryById"
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
          this.form.setFieldsValue(pick(this.model,'brand','kind','weight','grand','price','transportatioExpenses','totalPrice','standardPrice','memberPrice','minStock','maxStock','seller','contractId','contractData','status'))
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
        this.form.setFieldsValue(pick(row,'brand','kind','weight','grand','price','transportatioExpenses','totalPrice','standardPrice','memberPrice','minStock','maxStock','seller','contractId','contractData','status'))
      },
    }
  }
</script>