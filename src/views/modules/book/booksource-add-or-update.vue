<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="网站名" prop="sourceName">
      <el-input v-model="dataForm.sourceName" placeholder="网站名"></el-input>
    </el-form-item>
    <el-form-item label="状态 1可使用 2已失效" prop="state">
      <el-input v-model="dataForm.state" placeholder="状态 1可使用 2已失效"></el-input>
    </el-form-item>
    <el-form-item label="类型 1小说 2漫画" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型 1小说 2漫画"></el-input>
    </el-form-item>
    <el-form-item label="网站地址" prop="url">
      <el-input v-model="dataForm.url" placeholder="网站地址"></el-input>
    </el-form-item>
    <el-form-item label="配置值" prop="dateValue">
      <el-input v-model="dataForm.dateValue" placeholder="配置值"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
        dataForm: {
          sourceId: 0,
          sourceName: '',
          state: '',
          type: '',
          url: '',
          dateValue: '',
          createTime: ''
        },
        dataRule: {
          sourceName: [
            { required: true, message: '网站名不能为空', trigger: 'blur' }
          ],
          state: [
            { required: true, message: '状态 1可使用 2已失效不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型 1小说 2漫画不能为空', trigger: 'blur' }
          ],
          url: [
            { required: true, message: '网站地址不能为空', trigger: 'blur' }
          ],
          dateValue: [
            { required: true, message: '配置值不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.sourceId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.sourceId) {
            this.$http({
              url: this.$http.adornUrl(`/book/booksource/info/${this.dataForm.sourceId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.sourceName = data.bookSource.sourceName
                this.dataForm.state = data.bookSource.state
                this.dataForm.type = data.bookSource.type
                this.dataForm.url = data.bookSource.url
                this.dataForm.dateValue = data.bookSource.dateValue
                this.dataForm.createTime = data.bookSource.createTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/book/booksource/${!this.dataForm.sourceId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'sourceId': this.dataForm.sourceId || undefined,
                'sourceName': this.dataForm.sourceName,
                'state': this.dataForm.state,
                'type': this.dataForm.type,
                'url': this.dataForm.url,
                'dateValue': this.dataForm.dateValue,
                'createTime': this.dataForm.createTime
              })
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
