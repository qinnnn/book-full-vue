<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="源id" prop="sourceId">
      <el-input v-model="dataForm.sourceId" placeholder="源id"></el-input>
    </el-form-item>
    <el-form-item label="最新章节" prop="newest">
      <el-input v-model="dataForm.newest" placeholder="最新章节"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
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
          fictionId: 0,
          sourceId: '',
          newest: '',
          updateTime: '',
          createTime: ''
        },
        dataRule: {
          sourceId: [
            { required: true, message: '源id不能为空', trigger: 'blur' }
          ],
          newest: [
            { required: true, message: '最新章节不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.fictionId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.fictionId) {
            this.$http({
              url: this.$http.adornUrl(`/book/bookfictionsource/info/${this.dataForm.fictionId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.sourceId = data.bookFictionSource.sourceId
                this.dataForm.newest = data.bookFictionSource.newest
                this.dataForm.updateTime = data.bookFictionSource.updateTime
                this.dataForm.createTime = data.bookFictionSource.createTime
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
              url: this.$http.adornUrl(`/book/bookfictionsource/${!this.dataForm.fictionId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'fictionId': this.dataForm.fictionId || undefined,
                'sourceId': this.dataForm.sourceId,
                'newest': this.dataForm.newest,
                'updateTime': this.dataForm.updateTime,
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
