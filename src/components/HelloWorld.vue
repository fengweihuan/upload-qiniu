<template>
  <div class="hello">
    <h1 class="title">pc端 element-ui 上传</h1>
    <el-upload
      class="upload-demo"
      drag
      action="http://upload-z0.qiniu.com"
      :data="from"
      :before-upload="beforeUpload"
      multiple>
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
      <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过2MB!</div>
    </el-upload>
    <h1 class="title">移动端cube-ui 上传</h1>
    <cube-upload
      :action="action"
      :simultaneous-uploads="1"
      class="cube-upload"
      :processFile="processFile"
      @files-added="filesAdded" />
  </div>
</template>

<script>
import QiniuUPToken from 'qiniu-uptoken'
import { Style, Upload } from 'cube-ui'
export default {
  name: 'HelloWorld',
  components: { 'cube-upload': Upload },
  data () {
    return {
      fileList: [],
      imageUrl: '',
      fileimg: '',
      from: {
        key: '',
        token: '',
      },
      action: {
        target: 'http://upload-z0.qiniu.com',
        data: {
          key: '',
          token: '',
        }
      },
      uptoken: ''
    }
  },
  created () {
    this.uptoken = QiniuUPToken('mvuIwlFjDZ-ZmsqrtDT7AieuznMthgoq9k0Fya42', 'V24_T4HiKkVZ8DYgl-mW0eyFTbBtun5wLUYIHlkP', '233fengweihuanimage')
    this.action.data.token = this.uptoken
  },
  methods: {
      processFile(file, next) {
        this.action.data.key = file.name
        next(file)
      },
      filesAdded(files) {
        let hasIgnore = false
        const maxSize = 1 * 1024 * 1024 // 1M
        this.from.key = files.name
        for (let k in files) {
          const file = files[k]
          if (file.size > maxSize) {
            file.ignore = true
            hasIgnore = true
          }
        }
        hasIgnore && this.$createToast({
          type: 'warn',
          time: 1000,
          txt: 'You selected >1M files'
        }).show()
      },
      handleAvatarSuccess(res, file) {
        this.imageUrl = URL.createObjectURL(file.raw);
        console.log(this.imageUrl)
      },
      beforeUpload(file) {
        const isLt2M = file.size / 1024 / 1024 < 2;
        this.from.key = file.name
        this.from.token = this.uptoken
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 2MB!');
        }
        return isLt2M;
      }
    }
}
</script>

<style scoped>
.title{
  font-weight: 600;
  margin-top: 100px;
  margin-bottom: 40px;
}
.cube-upload{
  margin: 0 auto;
  padding: 0 80px;
}
.upload-demo{
  width: 600px;
  margin: 0 auto;
  text-align: center;
}
a {
  color: #42b983;
}
</style>
