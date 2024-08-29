<template>
	<view class="container">
		<view class="header">当前天气</view>
		<view class="weather-info">
			<view>城市：{{ weatherData.city }}</view>
			<view>温度：{{ weatherData.temp }}°C</view>
			<view>体感温度：{{ weatherData.feelsLike }}°C</view>
			<view>天气：{{ weatherData.text }}</view>
			<view>风向：{{ weatherData.windDir }}</view>
			<view>风速：{{ weatherData.windSpeed }} km/h</view>
			<view>相对湿度：{{ weatherData.humidity }} %</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				weatherData: {
					city: '',
					temp: '',
					feelsLike: '',
					text: '',
					windDir: '',
					windSpeed: '',
					humidity: ''
				}
			};
		},
		onLoad() {
			this.getWeatherData();
		},
		methods: {
			async getWeatherData() {
				// 替换为你自己的和风天气 API Key https://devapi.qweather.com/v7/weather/now?location=101230101&key=0ada432f3dc74bc0b7cc87f0f64203a0
				const apiKey = '0ada432f3dc74bc0b7cc87f0f64203a0';
				// 北京的 location ID 为 101010100，其他城市可在和风天气获取
				const location = '101230101';
				const url = `https://devapi.qweather.com/v7/weather/now?location=${location}&key=${apiKey}`;

				try {
					// 发送请求获取天气数据
					const res = await uni.request({
						url: url,
						method: 'GET'
					});
					if (res.data.code === "200") {
						this.weatherData.city = '福州'; // 可根据接口中的 location 获取更详细的城市名称
						this.weatherData.temp = res.data.now.temp;
						this.weatherData.feelsLike = res.data.now.feelsLike;
						this.weatherData.text = res.data.now.text;
						this.weatherData.windDir = res.data.now.windDir;
						this.weatherData.windSpeed = res.data.now.windSpeed;
						this.weatherData.humidity = res.data.now.humidity;
					} else {
						console.error('获取天气数据失败', res);
					}
				} catch (error) {
					console.error('请求失败', error);
				}
			}
		}
	};
</script>

<style>
	.container {
		padding: 20rpx;
	}

	.header {
		font-size: 30rpx;
		font-weight: bold;
		margin-bottom: 20rpx;
	}

	.weather-info {
		font-size: 26rpx;
		line-height: 40rpx;
		height: 800rpx;
		background-image: url('/static/0.jpg');
		background-size: 100% 100%;
		background-position: center;
		background-repeat: no-repeat;
	}
</style>