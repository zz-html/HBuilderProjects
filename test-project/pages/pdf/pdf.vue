<template>
  <view class="page">	  
	<view class="input-area">
	  <input
		class="url-input"
		type="text"
		placeholder="请输入PDF文件URL"
		v-model="pdfUrl"
	  />
	</view>  
    <button @click="downloadAndPreview" style="margin-top:20rpx">PDF 预览</button>
    <button @click="downloadAndSave" style="margin-top:20rpx">{{ $t('pdfDownload') }}(预览时，可点击右上角保存)</button>

    <view v-if="progressVisible">
      下载进度：{{progress}}%
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      pdfUrl: 'https://cloudsafe.oss-cn-hangzhou.aliyuncs.com/wmp/test11.pdf',
      progressVisible: false,
      progress: 0,
    }
  },
  methods: {
    // 直接下载并预览（使用 tempFilePath）
    downloadAndPreview() {
      if (!this.pdfUrl) {
        return uni.showToast({ title: '请输入PDF地址', icon: 'none' })
      }	
      const url = this.pdfUrl
      this.progressVisible = true
      this.progress = 0
      const task = uni.downloadFile({
        url,
        success: (res) => {
          this.progressVisible = false
          if (res.statusCode === 200 && res.tempFilePath) {
            // 直接打开预览（临时文件）
            uni.openDocument({
              filePath: res.tempFilePath,
              fileType: 'pdf',
              success: () => { console.log('openDocument success') },
              fail: (err) => { uni.showToast({ title: '打开失败', icon: 'none' }); console.error(err) }
            })
          } else {
            uni.showToast({ title: '下载失败', icon: 'none' })
            console.error('downloadFile statusCode', res.statusCode, res)
          }
        },
        fail: (err) => {
          this.progressVisible = false
          uni.showToast({ title: '下载失败', icon: 'none' })
          console.error('downloadFile fail', err)
        }
      })

      // 监控进度（task 在 uni-app 下支持 onProgressUpdate）
      if (task && task.onProgressUpdate) {
        task.onProgressUpdate((res) => {
          this.progress = res.progress // 0-100
        })
      }
    },

    // 下载并保存到本地（持久化），然后预览
    downloadAndSave() {
      if (!this.pdfUrl) {
        return uni.showToast({ title: '请输入PDF地址', icon: 'none' })
      }	
      const url = this.pdfUrl
      this.progressVisible = true
      this.progress = 0

      const task = uni.downloadFile({
        url,
        success: (res) => {
          if (res.statusCode === 200 && res.tempFilePath) {
            // 持久化保存
            uni.saveFile({
              tempFilePath: res.tempFilePath,
              success: (saveRes) => {
                this.progressVisible = false
                const savedPath = saveRes.savedFilePath
				console.log('已保存' + res.tempFilePath);
                // uni.showToast({ title: '已保存' + res.tempFilePath, icon: 'success' })
                // 可选：打开预览
                uni.openDocument({
                  filePath: savedPath,
                  fileType: 'pdf',
				  showMenu: true,
                  success: () => { console.log('openDocument success') },
                  fail: (err) => { console.error(err); uni.showToast({ title: '打开失败', icon: 'none' }) }
                })
              },
              fail: (err) => {
                this.progressVisible = false
                console.error('saveFile fail', err)
                uni.showToast({ title: '保存失败', icon: 'none' })
              }
            })
          } else {
            this.progressVisible = false
            uni.showToast({ title: '下载失败', icon: 'none' })
            console.error('downloadFile statusCode', res.statusCode, res)
          }
        },
        fail: (err) => {
          this.progressVisible = false
          uni.showToast({ title: '下载失败', icon: 'none' })
          console.error('downloadFile fail', err)
        }
      })

      if (task && task.onProgressUpdate) {
        task.onProgressUpdate((res) => {
          this.progress = res.progress
        })
      }
    }
  }
}
</script>

<style scoped>
.page { padding: 20px; }
</style>