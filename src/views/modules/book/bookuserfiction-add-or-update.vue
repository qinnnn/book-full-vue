<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="书id" prop="fictionId">
      <el-input v-model="dataForm.fictionId" placeholder="书id"></el-input>
    </el-form-item>
    <el-form-item label="类型 1小说 2漫画" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型 1小说 2漫画"></el-input>
    </el-form-item>
    <el-form-item label="最新阅读时间" prop="useTime">
      <el-input v-model="dataForm.useTime" placeholder="最新阅读时间"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="是否删除 0非 1是" prop="isDel">
      <el-input v-model="dataForm.isDel" placeholder="是否删除 0非 1是"></el-input>
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
          id: 0,
          userId: '',
          fictionId: '',
          type: '',
          useTime: '',
          createTime: '',
          isDel: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          fictionId: [
            { required: true, message: '书id不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型 1小说 2漫画不能为空', trigger: 'blur' }
          ],
          useTime: [
            { required: true, message: '最新阅读时间不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          isDel: [
            { required: true, message: '是否删除 0非 1是不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/book/bookuserfiction/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.bookUserFiction.userId
                this.dataForm.fictionId = data.bookUserFiction.fictionId
                this.dataForm.type = data.bookUserFiction.type
                this.dataForm.useTime = data.bookUserFiction.useTime
                this.dataForm.createTime = data.bookUserFiction.createTime
                this.dataForm.isDel = data.bookUserFiction.isDel
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
              url: this.$http.adornUrl(`/book/bookuserfiction/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'fictionId': this.dataForm.fictionId,
                'type': this.dataForm.type,
                'useTime': this.dataForm.useTime,
                'createTime': this.dataForm.createTime,
                'isDel': this.dataForm.isDel
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
