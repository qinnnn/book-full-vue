<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="小说名" prop="fictionName">
      <el-input v-model="dataForm.fictionName" placeholder="小说名"></el-input>
    </el-form-item>
    <el-form-item label="作者" prop="autor">
      <el-input v-model="dataForm.autor" placeholder="作者"></el-input>
    </el-form-item>
    <el-form-item label="类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="类型"></el-input>
    </el-form-item>
    <el-form-item label="状态 1 连载中 2 已完结" prop="state">
      <el-input v-model="dataForm.state" placeholder="状态 1 连载中 2 已完结"></el-input>
    </el-form-item>
    <el-form-item label="最新章节" prop="newest">
      <el-input v-model="dataForm.newest" placeholder="最新章节"></el-input>
    </el-form-item>
    <el-form-item label="字数" prop="number">
      <el-input v-model="dataForm.number" placeholder="字数"></el-input>
    </el-form-item>
    <el-form-item label="简介" prop="brief">
      <el-input v-model="dataForm.brief" placeholder="简介"></el-input>
    </el-form-item>
    <el-form-item label="图片地址" prop="imgUrl">
      <el-input v-model="dataForm.imgUrl" placeholder="图片地址"></el-input>
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
          fictionName: '',
          autor: '',
          type: '',
          state: '',
          newest: '',
          number: '',
          brief: '',
          imgUrl: '',
          createTime: ''
        },
        dataRule: {
          fictionName: [
            { required: true, message: '小说名不能为空', trigger: 'blur' }
          ],
          autor: [
            { required: true, message: '作者不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型不能为空', trigger: 'blur' }
          ],
          state: [
            { required: true, message: '状态 1 连载中 2 已完结不能为空', trigger: 'blur' }
          ],
          newest: [
            { required: true, message: '最新章节不能为空', trigger: 'blur' }
          ],
          number: [
            { required: true, message: '字数不能为空', trigger: 'blur' }
          ],
          brief: [
            { required: true, message: '简介不能为空', trigger: 'blur' }
          ],
          imgUrl: [
            { required: true, message: '图片地址不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/book/bookfiction/info/${this.dataForm.fictionId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.fictionName = data.bookFiction.fictionName
                this.dataForm.autor = data.bookFiction.autor
                this.dataForm.type = data.bookFiction.type
                this.dataForm.state = data.bookFiction.state
                this.dataForm.newest = data.bookFiction.newest
                this.dataForm.number = data.bookFiction.number
                this.dataForm.brief = data.bookFiction.brief
                this.dataForm.imgUrl = data.bookFiction.imgUrl
                this.dataForm.createTime = data.bookFiction.createTime
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
              url: this.$http.adornUrl(`/book/bookfiction/${!this.dataForm.fictionId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'fictionId': this.dataForm.fictionId || undefined,
                'fictionName': this.dataForm.fictionName,
                'autor': this.dataForm.autor,
                'type': this.dataForm.type,
                'state': this.dataForm.state,
                'newest': this.dataForm.newest,
                'number': this.dataForm.number,
                'brief': this.dataForm.brief,
                'imgUrl': this.dataForm.imgUrl,
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
