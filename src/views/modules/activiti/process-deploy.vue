<template>
  <el-dialog :visible.sync="visible" :title="$t('process.deployFile')" :close-on-click-modal="false" :close-on-press-escape="false">
    <!-- https://github.com/d2-projects/d2-admin-renren-security-enterprise/issues/8 -->
    <el-upload
      name="processFile"
      :action="url"
      :file-list="fileList"
      drag
      :before-upload="beforeUploadHandle"
      :on-success="successHandle"
      class="d2-text-center">
      <i class="el-icon-upload"/>
      <div class="el-upload__text" v-html="$t('upload.text')"/>
      <div class="el-upload__tip" slot="tip">{{ $t('upload.tip', { 'format': 'zip、xml、bar、bpmn' }) }}</div>
    </el-upload>
  </el-dialog>
</template>

<script>
import { cookieGet } from '@/common/cookie'
export default {
  data () {
    return {
      visible: false,
      url: '',
      fileList: []
    }
  },
  methods: {
    init () {
      this.visible = true
      this.url = `${window.SITE_CONFIG['apiURL']}/act/process/deploy?token=${cookieGet('token')}`
      this.fileList = []
    },
    // 上传之前
    beforeUploadHandle (file) {
      if (!/.+\.zip$/.test(file.name) && !/.+\.xml$/.test(file.name) && !/.+\.bar$/.test(file.name) && !/.+\.bpmn$/.test(file.name)) {
        this.$message.error(this.$t('upload.tip', { 'format': 'zip、xml、bar、bpmn' }))
        return false
      }
    },
    // 上传成功
    successHandle (res, file, fileList) {
      if (res.code !== 0) {
        return this.$message.error(res.msg)
      }
      this.$message({
        message: this.$t('prompt.success'),
        type: 'success',
        duration: 500,
        onClose: () => {
          this.visible = false
          this.$emit('refreshDataList')
        }
      })
    }
  }
}
</script>
