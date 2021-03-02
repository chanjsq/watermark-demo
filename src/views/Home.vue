<template>
  <div class="p-5">
    <!-- 顶部工具栏 -->
    <div class="flex mb-8">
      <div class="w-1/4 flex items-center">
        <el-checkbox v-model="shouldResize">调整宽度至</el-checkbox>
        <el-input v-model.number="resizedWidth" :disabled="!shouldResize" class="ml-5">
          <template #append>px</template>
        </el-input>
      </div>
      <div class="w-1/4 flex items-center ml-8">
        <el-checkbox v-model="useWatermark">添加水印</el-checkbox>
        <el-link type="primary" href="/config" target="_blank" class="ml-5">配置水印</el-link>
      </div>
    </div>
    <!-- 中间上传操作和状态 -->
    <div class="bg-gray-50 p-5 mb-8">
      <el-upload
        v-if="fileList.length === 0"
        multiple
        accept="image/*"
        action="https://jsonplaceholder.typicode.com/posts/"
        :auto-upload="false"
        :show-file-list="false"
        :on-change="handleChange"
      >
        <el-button icon="el-icon-plus" type="text">选择图片</el-button>
      </el-upload>
      <div class="">
        <div v-for="(file, index) in fileList" :key="file.uid" class="flex items-center mb-5">
          <div class="w-1/5">
            <canvas :ref="`canvas${index}`" class="h-16"></canvas>
          </div>
          <div class="w-1/5">{{ file.name }}</div>
          <div class="w-1/5">{{ file.size }}</div>
          <div class="w-1/5">{{ file.percentage }}%</div>
          <div class="w-1/5">{{ file.status }}</div>
        </div>
      </div>
    </div>
    <!-- 提交按钮 -->
    <div class="flex items-center">
      <el-button type="primary">确定</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      shouldResize: false,
      resizedWidth: 800,
      useWatermark: false,
      fileList: []
    }
  },

  methods: {
    // 处理选择图片
    handleChange (_, fileList) {
      for (let i = 0; i < fileList.length; i++) {
        this.makeCanvas(fileList[i], i)
      }
      this.fileList = fileList
    },
    // 合成水印图片
    makeCanvas (file, index) {
      // 加载选中图片
      const img = new Image()
      img.onload = () => {
        // 缩放图片
        const imgWidth = img.width
        const imgHeight = img.height
        const targetWidth = this.resizedWidth || imgWidth
        const targetHeight = (imgHeight / imgWidth) * targetWidth
        const canvas = this.$refs[`canvas${index}`][0]
        canvas.width = targetWidth
        canvas.height = targetHeight
        const ctx = canvas.getContext('2d')
        ctx.drawImage(img, 0, 0, targetWidth, targetHeight)
        // 添加水印
        // const watermarkConfig = this.getWatermarkConfig()
        // 添加图片水印
        // ...
        // 添加文字水印
        ctx.fillStyle = '#dd0400'
        ctx.font = "bold 80px/80px 'Microsoft YaHei'"
        const textX = targetWidth - ctx.measureText('耐克').width - 40
        const textY = 100
        ctx.fillText('安踏', textX, textY)
      }
      img.src = URL.createObjectURL(file.raw)
    },
    // 获取水印配置
    getWatermarkConfig () {
      const defaultConfig = {
        image: { style: {}, value: '' },
        text: { style: {}, value: '' }
      }
      return JSON.parse(localStorage.getItem('watermarkConfig')) || defaultConfig
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
