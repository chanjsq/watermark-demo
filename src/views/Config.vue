<template>
  <div class="flex p-5">
    <!-- 左侧预览 -->
    <div class="relative w-60 h-60 border border-solid border-gray-200 mr-5">
      <div class="absolute" :style="text.style">{{ text.value }}</div>
      <img v-if="image.value" class="absolute" :style="image.style" :src="image.value">
    </div>
    <!-- 右侧配置 -->
    <div>

      <h3 class="mb-3">文字水印</h3>
      <el-input v-model="text.value" class="block mb-3" />
      <el-color-picker v-model="text.style.color" class="block mb-3" />

      <h3 class="mb-3 mt-5">图片水印</h3>
      <el-upload action="" list-type="picture-card" :auto-upload="false" :onChange="onImageChange" :show-file-list="false">
        <i class="el-icon-plus"></i>
        <template #file>
          <img class="el-upload-list__item-thumbnail" :src="image.value">
        </template>
      </el-upload>

      <el-button @click="save" type="primary" class="block mt-5">保存</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 文本水印配置
      text: {
        value: '请输入文字水印内容',
        style: {
          color: '#2878ff',
          top: '10px',
          left: '10px',
          fontSize: '14px'
        }
      },
      // 图片水印配置
      image: {
        value: '', // Base64 的图片
        style: {
          width: '100px',
          height: '100px',
          right: '10px',
          bottom: '10px'
        }
      }
    }
  },

  methods: {
    // 将选择的图片, 解析为 Base64 后保存到 this.image.value
    onImageChange (file, fileList) {
      fileList.splice(0, fileList.length - 1)
      const reader = new FileReader()
      reader.addEventListener('load', () => {
        this.image.value = reader.result
      })
      reader.readAsDataURL(file.raw)
    },
    // 保存水印配置
    save () {
      const { text, image } = this
      const config = { text, image }
      const configJson = JSON.stringify(config)
      localStorage.setItem('watermarkConfig', configJson)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
