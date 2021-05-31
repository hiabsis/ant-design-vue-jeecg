<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-item label="员工姓名" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['name']" placeholder="请输入员工姓名"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="性别" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="radio" v-decorator="['gender']" :trigger-change="true" dictCode="" placeholder="请选择性别" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="出生日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择出生日期" v-decorator="['birthday']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="身份证号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['idcard']" placeholder="请输入身份证号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="婚姻状况" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="radio" v-decorator="['wedlock']" :trigger-change="true" dictCode="" placeholder="请选择婚姻状况" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="民族" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['nationid']" :trigger-change="true" dictCode="" placeholder="请选择民族" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="籍贯" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['nativeplace']" placeholder="请输入籍贯"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="政治面貌" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['politicid']" :trigger-change="true" dictCode="" placeholder="请选择政治面貌" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="邮箱" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['email']" placeholder="请输入邮箱"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="电话号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['phone']" placeholder="请输入电话号码"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="联系地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['address']" placeholder="请输入联系地址"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="所属部门" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-category-select v-decorator="['departmentid']" pcode="" placeholder="请选择所属部门"  />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="职称ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['joblevelid']" :trigger-change="true" dictCode="" placeholder="请选择职称ID" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="职位ID" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['posid']" :trigger-change="true" dictCode="" placeholder="请选择职位ID" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="聘用形式" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="radio" v-decorator="['engageform']" :trigger-change="true" dictCode="" placeholder="请选择聘用形式" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="最高学历" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['tiptopdegree']" :trigger-change="true" dictCode="" placeholder="请选择最高学历" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="所属专业" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['specialty']" placeholder="请输入所属专业"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="毕业院校" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['school']" placeholder="请输入毕业院校"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="入职日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择入职日期" v-decorator="['begindate']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="在职状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['workstate']" placeholder="请输入在职状态"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="工号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['workid']" placeholder="请输入工号"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="合同期限" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['contractterm']" placeholder="请输入合同期限" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="转正日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择转正日期" v-decorator="['conversiontime']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="离职日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择离职日期" v-decorator="['notworkdate']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="合同起始日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择合同起始日期" v-decorator="['begincontract']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="合同终止日期" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-date placeholder="请选择合同终止日期" v-decorator="['endcontract']" :trigger-change="true" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="工龄" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['workage']" placeholder="请输入工龄" style="width: 100%" />
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
    name: 'EmployeeForm',
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
          add: "/employee/employee/add",
          edit: "/employee/employee/edit",
          queryById: "/employee/employee/queryById"
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
          this.form.setFieldsValue(pick(this.model,'name','gender','birthday','idcard','wedlock','nationid','nativeplace','politicid','email','phone','address','departmentid','joblevelid','posid','engageform','tiptopdegree','specialty','school','begindate','workstate','workid','contractterm','conversiontime','notworkdate','begincontract','endcontract','workage'))
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
        this.form.setFieldsValue(pick(row,'name','gender','birthday','idcard','wedlock','nationid','nativeplace','politicid','email','phone','address','departmentid','joblevelid','posid','engageform','tiptopdegree','specialty','school','begindate','workstate','workid','contractterm','conversiontime','notworkdate','begincontract','endcontract','workage'))
      },
      handleCategoryChange(value,backObj){
        this.form.setFieldsValue(backObj)
      }
    }
  }
</script>