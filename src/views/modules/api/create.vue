<template>
  <div class="mod-role">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.apiName" placeholder="API名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()" >查询</el-button>
        <el-button v-if="isAuth('api:save')" type="primary" @click="addOrUpdateHandle()">注册</el-button>
        <el-button v-if="isAuth('api:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
        <el-button v-if="isAuth('api:export:excel')" type="primary" @click="exportExcel()">导出EXCEL</el-button>
        <el-button v-if="isAuth('api:export:word')" type="primary" @click="exportWordList()">导出WORD清单</el-button>
        <el-button type="primary" @click="getDataList()">刷新</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="apiParentModel"
        header-align="center"
        align="center"
        width="120"
        label="所属模块"
        >
      </el-table-column>
      <el-table-column
        prop="apiUrl"
        header-align="center"
        align="center"
        label="URL地址">
      </el-table-column>
      <el-table-column
        prop="apiName"
        header-align="center"
        align="center"
        label="请求名称">
      </el-table-column>
      <el-table-column
        prop="apiMethod"
        header-align="center"
        align="center"
        label="请求方式">
      </el-table-column>     
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('api:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.apiId)">修改</el-button>
          <el-button v-if="isAuth('api:delete')" type="text" size="small" @click="deleteHandle(scope.row.apiId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './create-add-or-update'
  import Vue from 'vue'
  export default {
    data () {
      return {
        indexArr: [],
        spanArr: [],
        pos: 0,
        dataForm: {
          apiName: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
      
    },
    activated () {
      this.getDataList();
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/api/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'apiName':this.dataForm.apiName,
            'r':Math.random()
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount          
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.roleId
        })
        this.$confirm(`确定是否[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/api/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      },
      // 导出EXCEL
      exportExcel(){        
        let url=this.$http.adornUrl('/api/export/excel')
        this.$http({
          url:url,
          method:'post',
          data:this.dataList
        }).then(({data})=>{
          this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500 ,
                onClose: () => {
                  this.downLoadFile(encodeURI(data.msg));
                }        
              }) 
        })
      },
      // 导出WORD清单
      exportWordList(){
        this.$http({
            url: this.$http.adornUrl('/api/export/word'),
            method: 'post',
            data: this.dataList
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.downLoadFile(encodeURI(data.msg));
                }              
              })              
            } else {
              this.$message.error(data.msg)
            }
          })
      },
      // 文件下载
      downLoadFile(url){
        window.location=this.$http.adornUrl('/api/download?fileName='+url+'&token='+Vue.cookie.get('token'))
      },
      // 打开文件
      OpenFile(url){
        window.open(url);
      },
      getSpanArr(data) {
        for (var i = 0; i < data.length; i++) {
          if (i === 0) {
            this.spanArr.push(1);
            this.pos = 0;
          } else {
            // 判断当前元素与上一个元素是否相同（line为标记）
            if (data[i].apiParentModel === data[i - 1].apiParentModel) {
              this.spanArr[this.pos] += 1;
              this.spanArr.push(0);
            }else{
              this.spanArr.push(1);
              this.pos=0;
            }
          }
        }
      },
      arraySpanMethod({ row, column, rowIndex, columnIndex }) {
        //处理行的合并
        if (this.indexArr.includes(rowIndex)) {
          if (columnIndex === 0) {
            return {
              rowspan: 1,
              colspan: 4
            };
          } else if (columnIndex === 1 || columnIndex === 2 || columnIndex == 3) {
            return {
              rowspan: 0,
              colspan: 0
            };
          }
        }

        if (columnIndex === 1) {
        const _row = this.spanArr[rowIndex];
        const _col = _row > 0 ? 1 : 0;
        return {
          rowspan: _row,
          colspan: _col
        };
      }

        
      }
    }
  }
</script>
