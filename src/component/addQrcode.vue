<template>
  <div id="addQrcode" class="sx_basis_scroll sx_scroll_style">
    <el-row class="addQrcode_body" type="flex" justify="center">
      <el-col :span="16">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h2 style="float: left">添加App二维码</h2>
          </div>
          <el-form 
            :model="ruleForm"
            ref="ruleForm"
            label-position="left"
            label-width="100px" 
            class="demo-ruleForm">
            <el-form-item label="App名称">
              <el-input v-model="ruleForm.name"></el-input>
            </el-form-item>
            <el-form-item label="二维码">
              <el-upload
                class="avatar-uploader"
                :action="uploadQrcode"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :on-error="handleError"
                :before-upload="beforeAvatarUpload"
                name="qrcode">
                <img v-if="imgUrl" :src="imgUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="submitForm">立即创建</el-button>
              <el-button @click="resetForm('ruleForm')">重置</el-button>
            </el-form-item>
          </el-form>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import { uploadQrcode } from '../assets/axios/api.js'
  export default{
    name: 'addQrcode',
    data () {
      return {
        uploadQrcode, // 图片预上传
        ruleForm: {
          name: '' // 名称
        },
        imgUrl: '' // 图片地址
      }
    },
    methods: {
      /* ---- 提交数据 ---- */
      submitForm () {
        if (this.ruleForm.name) {
          if (this.imgUrl) {
            this.$axios.post(uploadQrcode, {
              name: this.ruleForm.name,
              url: this.imgUrl
            })
            .then(msg => {
              const data = msg.data
              switch (data.flag) {
                case 1000:
                  this.consoleSuccess(`添加成功!`)
                  setTimeout(() => {
                    /* 跳转 */
                    this.$router.push('qrcodeList')
                  }, 500)
                  break
                default:
                  this.consoleWarning(data.return_code)
                  break
              }
            })
            .catch(error => {
              this.consoleError(error)
            })
          } else {
            this.consoleWarning(`请上传二维码`)
          }
        } else {
          this.consoleWarning(`请填写App名称`)
        }
      },
      /* ------------------ */
      /* 清空表单 */
      resetForm () {
        // 清空字段
        this.ruleForm.name = ''
        // 清空封面图
        this.imgUrl = null
      },
      /* ---- 图片上传 ---- */
      handleAvatarSuccess (res, file) {
        if (res) {
          switch (res.flag >> 0) {
            case 1000:
              this.consoleSuccess(`二维码上传成功`)
              this.imgUrl = res.file_url
              break
            default:
              this.consoleWarning(`${res.return_code}`)
              break
          }
        }
      },
      beforeAvatarUpload (file) {
        const isJPG = file.type === 'image/jpeg'
        const isLt2M = file.size / 1024 / 1024 < 1

        if (!isJPG) {
          this.$message.error('上传图片只能是 JPG 格式!')
        }
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 1MB!')
        }
        return isJPG && isLt2M
      },
      /* ------- 错误时处理 ------*/
      handleError (err, file, fileList) {
        this.consoleWarning(`文件上传失败! 原因: ${err}`)
      },
      /* ----------------------- */
      consoleSuccess (success) {
        this.$notify({
          title: '成功',
          message: success,
          type: 'success'
        })
      },

      consoleWarning (warning) {
        this.$notify({
          title: '警告',
          message: warning,
          type: 'warning'
        })
      },

      consoleNews (news) {
        this.$notify.info({
          title: '消息',
          message: news
        })
      },

      consoleError (error) {
        this.$notify.error({
          title: '错误',
          message: error
        })
      }
    },
    components: {}
  }
</script>

<style lang="less">
  #addQrcode{
    width: 100%;
    height: 100%;
    overflow: auto;
    .addQrcode_body{
      box-sizing: border-box;
      padding: 50px 0;
    }
  }
</style>