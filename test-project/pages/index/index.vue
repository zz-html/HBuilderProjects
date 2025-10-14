<template>
	<view class="content">
		<image class="logo" src="/static/z.jpg"></image>
		<view class="btnContainer">
			<view class="indexBtn" @click="goPage('/pages/weather/weather')">
				天气查询
			</view>	
			<view class="indexBtn" @click="goPage('/pages/bluetooth/bluetooth')">
				蓝牙连接
			</view>
		</view>
		<view class="btnContainer">
			<view class="indexBtn" @click="goPage('/pages/pdf/pdf')">
				pdf下载
			</view>	
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {
			goPage(url) {
				console.log("goPage");
				uni.navigateTo({ url: url });
			}
		}
	}
</script>

<style lang="scss" scoped>
.content {
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
}

.logo {
	height: 300rpx;
	width: 300rpx;
	margin-top: 200rpx;
	margin-left: auto;
	margin-right: auto;
	margin-bottom: 50rpx;
}

.btnContainer {
  display: flex;
  justify-content: space-between; /* 左右分布 */
  align-items: center; /* 垂直居中 */
  width: 100%;
  margin-top: 30rpx;
}

.indexBtn {
	width: 40%;
	margin: auto;
	border-radius: 16rpx;
	border: 1px solid #ebedef;
	text-align: center;
	height: 100rpx;
	line-height: 100rpx;
	box-shadow: 0 4px #C5C5C5; /* 模拟按钮下方的立体效果 */
	transition: all 0.2s ease;
}

</style>
