<template>
  <div id="qrcodeList" class="sx_basis_scroll sx_scroll_style">
    <el-row class="qrcodeList_body" type="flex" justify="center">
      <el-col :span="16">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <h2 style="float: left">App二维码</h2>
          </div>
          <el-table
            :data="qrcodeList"
            border
            stripe
            style="width: 100%">
            <el-table-column
              prop="name"
              label="App名称"
              width="120"
              align="center">
            </el-table-column>
            <el-table-column
              label="App二维码"
              align="center">
              <template scope="scope">
                <img :src="scope.row.url" v-if="scope.row.url" alt="App二维码">
              </template>
            </el-table-column>
            <el-table-column
              label="操作"
              width="180"
              align="center">
              <template scope="scope">
                <el-button 
                  type="danger"
                  size="small"
                  @click="removeNews(scope.row.id)">删除<i class="el-icon-delete2 el-icon--right"></i>
                </el-button>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import { listQrcode, removeQrcode } from '../assets/axios/api.js'
  export default{
    name: 'qrcodeList',
    data () {
      return {
        qrcodeList: [] // App列表
      }
    },
    created: function () {
      /* ===== 获取招聘列表 ===== */
      this.getListQrcode()
      /* ======================== */
    },
    methods: {
      // 获取招聘
      getListQrcode () {
        this.$axios.post(listQrcode)
        .then((msg) => {
          const data = msg.data
          switch (data.flag) {
            case 1000:
              this.qrcodeList = data.qrcode_list
              break
            case 9001:
              /* 不做处理 */
              this.$alert('暂无App二维码', '友情提示', {
                confirmButtonText: '确定'
              })
              break
            default:
              this.consoleWarning(data.return_code)
              break
          }
        })
        .catch(error => {
          this.consoleError(error)
        })
      },
      /* 删除招聘 */
      removeNews (id) {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$axios.post(removeQrcode, {
            id
          })
          .then((msg) => {
            const data = msg.data
            switch (data.flag) {
              case 1000:
                this.consoleSuccess(`App二维码删除成功`)
                /* ===== 获取招聘列表 ===== */
                this.getListQrcode()
                /* ======================== */
                break
              default:
                this.consoleWarning(data.return_code)
                break
            }
          })
          .catch(error => {
            this.consoleError(error)
          })
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
      },
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
    }
  }
</script>

<style lang="less">
   #qrcodeList{
    width: 100%;
    height: 100%;
    .qrcodeList_body{
      box-sizing: border-box;
      padding-top: 50px;
    }
  }
</style>
