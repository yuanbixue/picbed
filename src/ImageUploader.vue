<template>
  <div class="upload-container">
    <div 
      class="drop-area"
      @dragover.prevent
      @drop="handleDrop"
    >
      <input ref="fileInput" type="file" accept="image/*" @change="handleFileSelect" style="display: none;">
      <button @click="triggerFileInput">点击上传</button>
      将图片拖拽到此处或直接粘贴
    </div>
    <div v-if="imageUrl" class="preview-area">
      <img :src="imageUrl" alt="上传的图片">
      <div class="url-display">
        <input type="text" :value="imageUrl" readonly>
        <button @click="copyUrl">复制地址</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imageUrl: ''
    };
  },
  mounted() {
    window.addEventListener('paste', this.handlePaste);
  },
  beforeDestroy() {
    window.removeEventListener('paste', this.handlePaste);
  },
  methods: {
    /**
     * 触发文件选择
     */
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    /**
     * 处理文件选择
     * @param {Event} event 文件选择事件对象
     */
    handleFileSelect(event) {
      const file = event.target.files[0];
      if (file) {
        this.uploadImage(file);
      }
    },
    /**
     * 处理拖拽事件
     * @param {Event} event 拖拽事件对象
     */
    handleDrop(event) {
      event.preventDefault();
      const file = event.dataTransfer.files[0];
      this.uploadImage(file);
    },
    /**
     * 处理粘贴事件
     * @param {Event} event 粘贴事件对象
     */
    handlePaste(event) {
      const items = event.clipboardData.items;
      for (let i = 0; i < items.length; i++) {
        if (items[i].type.indexOf('image') !== -1) {
          const file = items[i].getAsFile();
          this.uploadImage(file);
          break;
        }
      }
    },
    /**
     * 复制图片地址到剪贴板
     */
    copyUrl() {
      const input = this.$el.querySelector('.url-display input');
      input.select();
      document.execCommand('copy');
    },

    /**
     * 上传图片到服务器
     * @param {File} file 图片文件对象
     */
    async uploadImage(file) {
      const OSS = require('ali-oss');
      const config = require('../oss.json');
      const client = new OSS(config);

      try {
        const result = await client.put(`picbed/${file.name}`, file, {
          headers: {
            'Content-Type': file.type || 'image/jpeg'
          }
        });
        this.imageUrl = result.url;
      this.$nextTick(() => {
        this.copyUrl();
      });
      } catch (error) {
        console.error('上传失败:', error);
      }
    }
  }
};
</script>

<style scoped>
.upload-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.drop-area {
  border: 2px dashed #ccc;
  border-radius: 4px;
  padding: 40px;
  text-align: center;
  cursor: pointer;
  transition: border-color 0.3s;
}

.drop-area:hover {
  border-color: #409eff;
}

.preview-area {
  margin-top: 20px;
  text-align: center;
}

.preview-area img {
  max-width: 100%;
  max-height: 400px;
  border-radius: 4px;
}
</style>