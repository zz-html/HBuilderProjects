<template>
	<view class="content">
		<image class="logo" src="/static/z.jpg"></image>
		<view class="btnContainer">
			<view class="indexBtn" @click="goPage('/pages/weather/weather')">
				{{ $t('weather') }}
			</view>	
			<view class="indexBtn" @click="goPage('/pages/bluetooth/bluetooth')">
				{{ $t('bluetooth') }}
			</view>
		</view>
		<view class="btnContainer">
			<view class="indexBtn" @click="goPage('/pages/pdf/pdf')">
				{{ $t('pdfDownload') }}
			</view>	
		</view>
		<view class="langBtn" @click="changeLang()">
			<view v-if="$i18n.locale === 'zh'">English</view>
			<view v-else>中文</view>
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
			console.log("goPage", url);
			uni.navigateTo({ url: url });
		},
		changeLang() {
			const currentLang = this.$i18n.locale || "zh";
			if (currentLang == "en") {
				this.$i18n.locale = 'zh';
			} else {
				this.$i18n.locale = 'en';
			}
			uni.setStorageSync('lang', this.$i18n.locale);
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
.langBtn {
	position: absolute;
	top: 0;
	right: 0;
	margin: 10rpx;
}
</style>
