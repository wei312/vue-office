<template>
  <el-dialog
    :title="!dataForm.id ? 'API注册' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    >
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      
      <el-form-item label="API名称" prop="apiName" >
        <el-input v-model="dataForm.apiName" placeholder="API名称" ></el-input>
      </el-form-item>
      <el-form-item label="URL地址" prop="apiUrl" >
        <el-input v-model="dataForm.apiUrl" placeholder="URL地址"></el-input>
      </el-form-item>
      <el-form-item label="请求方式" prop="apiMethod" >
        <el-input v-model="dataForm.apiMethod" placeholder="请求方式"></el-input>
      </el-form-item>
      <el-form-item label="请求类型" prop="apiRequestType" >
        <el-input v-model="dataForm.apiRequestType" placeholder="请求类型" value="application/json"></el-input>
      </el-form-item>
      <el-form-item label="返回类型" prop="apiResponseType" >
        <el-input v-model="dataForm.apiResponseType" placeholder="返回类型" value="*/*"></el-input>
      </el-form-item>
      <el-form-item label="所属模块" prop="apiParentModel" >
        <el-input v-model="dataForm.apiParentModel" placeholder="所属模块"></el-input>
      </el-form-item>
      <el-form-item label="研发版本" prop="apiVersion" >
        <el-input v-model="dataForm.apiVersion" placeholder="研发版本"></el-input>
      </el-form-item> 
      <el-form-item label="示例" prop="example" >
        <el-input v-model="dataForm.example" placeholder="示例" type="textarea" rows="3" style="width:500px;"></el-input>
      </el-form-item> 
      <el-form-item label="请求参数" prop="requestExample" >
        <el-input v-model="dataForm.requestExample" placeholder="请求参数" type="textarea" rows="3" style="width:500px;"></el-input>
      </el-form-item> 
      <el-form-item label="返回值" prop="responseExample" >
        <el-input v-model="dataForm.responseExample" placeholder="返回值" type="textarea" rows="3" style="width:500px;"></el-input>
      </el-form-item> 
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>
<script> 
  export default {
    data () {
      return {
        visible: false,
        apiList: [],        
        dataForm: {
          id: 0,
          apiName: '',
          apiUrl: '',
          apiMethod:'',
          apiParentModel:'',
          apiVersion:'',
          apiResponseType:'*/*',
          apiRequestType:'application/json',
          example:'',
          requestExample:'',
          responseExample:''
        },
        dataRule: {
          apiName: [
            { required: true, message: 'API名称不能为空', trigger: 'blur' }
          ],
          apiUrl: [
            { required: true, message: 'URL地址不能为空', trigger: 'blur' }
          ],
          apiMethod: [
            { required: true, message: '请选择请求方式', trigger: 'blur' }
          ],
          apiParentModel:[
            { required: true, message: '所属模块不能为空', trigger: 'blur' }
          ],
          apiVersion:[
            { required: true, message: '研发版本不能为空', trigger: 'blur' }
          ],
          apiRequestType:[
            { required: true, message: '请求类型不能为空', trigger: 'blur' }
          ],
          apiResponseType:[
            { required: true, message: '返回类型不能为空', trigger: 'blur' }
          ],                  
          responseExample:[
            { required: true, message: '返回值不能为空', trigger: 'blur' }
          ]
        },
        tempKey: -666666 // 临时key, 用于解决tree半选中状态项不能传给后台接口问题. # 待优化
      }
    },
    methods: {
      init(id){
        this.dataForm.id = id || 0
        this.visible = true
        if (this.dataForm.id===0){
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          });
        }else if (this.dataForm.id!=0) {
          this.$http({
              url: this.$http.adornUrl(`/api/info`),
              method: 'get',
              params: {
                appId:id
              }
            }).then(({data}) => {
              if(data&&data.code===0){
                let _data=data.data;
                this.dataForm.apiName=_data.apiName
                this.dataForm.apiUrl=_data.apiUrl
                this.dataForm.apiMethod=_data.apiMethod
                this.dataForm.apiParentModel=_data.apiParentModel
                this.dataForm.apiVersion=_data.apiVersion
                this.dataForm.apiResponseType=_data.apiResponseType
                this.dataForm.apiRequestType=_data.apiRequestType
                this.dataForm.example=_data.example
                this.dataForm.requestExample=_data.requestExample
                this.dataForm.responseExample=_data.responseExample
              }
            });
          }
            
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/api/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData(
                {
                  apiId: this.dataForm.id,
                  apiName: this.dataForm.apiName,
                  apiUrl: this.dataForm.apiUrl,
                  apiMethod:this.dataForm.apiMethod,
                  apiParentModel:this.dataForm.apiParentModel,
                  apiVersion:this.dataForm.apiVersion,
                  apiRequestType:this.dataForm.apiRequestType,
                  apiResponseType:this.dataForm.apiResponseType,
                  example:this.dataForm.example,
                  requestExample:this.dataForm.requestExample,
                  responseExample:this.dataForm.responseExample
                }
              )
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
