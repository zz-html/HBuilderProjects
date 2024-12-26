<template>
	<view class="content">
		<view class="flex item-center" @click="open">
			初始化
		</view>
		<view class="flex item-center" @click="discovery">
			搜索
		</view>	
		<view class="flex item-center" @click="discoveryStop">
			停止
		</view>
		<view class="flex item-center" @click="sendData">
			发送
		</view>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Helloz',
				targetDeviceName: "JDY-33-SPP",
				targetDeviceName2: "JDY-33-BLE",
				deviceId: null,
				targetDeviceId: null,
				characteristicId: null
			}
		},
		onLoad() {

		},
		methods: {
			open() {
				uni.openBluetoothAdapter({
				  success: (res) => {
					this.title += '蓝牙模块初始化成功';
				  },
				  fail: (err) => {
					console.log('蓝牙模块初始化失败', err)
					this.title += '蓝牙模块初始化失败' + err.errMsg;
					if (err.errCode === 10001) {
					  uni.onBluetoothAdapterStateChange((state) => {
						if (state.available) {
						  this.title += '蓝牙模块已启用';
						}
					  });
					}
				  },
				});
			},
			// 解码 advertisData
			decodeAdvertisData(advertisData) {
				if (!advertisData) return '';
				const buffer = new Uint8Array(advertisData);
				return String.fromCharCode.apply(null, buffer);
			},
			discovery() {
				uni.startBluetoothDevicesDiscovery({
					allowDuplicatesKey: false,
					success: (res) => {
						this.title += '开始搜索蓝牙设备';
					},
					fail: (err) => {
						this.title += '搜索蓝牙设备失败' + err.errMsg;
					},
				});

				// 监听发现新设备的事件
				uni.onBluetoothDeviceFound((res) => {
				  res.devices.forEach((device) => {
					const name = device.name || device.localName || this.decodeAdvertisData(device.advertisData);
					console.log('设备名称:', name || '未知设备');
					if (name === this.targetDeviceName || name === this.targetDeviceName2) {
						this.deviceId = device.deviceId;
					    console.log('找到目标设备:', name, this.deviceId);
						// 停止搜索
						this.discoveryStop();
						this.connectToDevice(this.deviceId);
					}
				  });
				});
			},
			discoveryStop() {
				uni.stopBluetoothDevicesDiscovery({
				  success: (res) => {
					//console.log('停止搜索成功', res);
					this.title += '停止搜索成功';
				  },
				  fail: (err) => {
					//console.error('停止搜索失败', err);
					this.title += '停止搜索失败' + err.errMsg;
				  },
				});
			},
			connectToDevice(deviceId) {
				uni.createBLEConnection({
					deviceId,
					success: (res) => {
						console.log('连接成功:', res);
						// 获取设备服务
						this.getDeviceServices(deviceId);
					},
					fail: (err) => {
						console.error('连接失败:', err);
					}
				});				
			},
			// 获取设备服务
			getDeviceServices(deviceId) {
			  uni.getBLEDeviceServices({
				deviceId,
				success: (res) => {
				  console.log('设备服务:', res.services);

				  // 获取第一个服务的特征值
				  this.serviceId = res.services[0].uuid;
				  this.getDeviceCharacteristics(deviceId, this.serviceId);
				},
				fail: (err) => {
				  console.error('获取服务失败:', err);
				}
			  });
			},
			getDeviceCharacteristics(deviceId, serviceId) {
				uni.getBLEDeviceCharacteristics({
					deviceId,
					serviceId,
					success: (res) => {
					  console.log('特征值:', res.characteristics);
					  this.characteristicId = res.characteristics[0].uuid;
					  this.enableNotify();
					},
					fail: (err) => {
					  console.error('获取特征值失败:', err);
					}
				});
			},
			
			uint8ArrayToString(uint8Array) {
			  return String.fromCharCode.apply(null, uint8Array);
			},
			enableNotify() {

			  uni.notifyBLECharacteristicValueChange({
				deviceId: this.deviceId,
				serviceId: this.serviceId,
				characteristicId: this.characteristicId,
				state: true,
				success: () => {
				  console.log('通知已开启');
				  uni.onBLECharacteristicValueChange((res) => {
					const receivedData = new Uint8Array(res.value);
					console.log('接收到的数据:', receivedData);
					const str = this.uint8ArrayToString(receivedData);
					console.log('接收到的数据str:', str);
				  });
				},
				fail: (err) => {
				  console.error('开启通知失败:', err);
				}
			  });
				
			},
			sendData() {
				console.log('sendData deviceId:', this.deviceId);
				console.log('sendData serviceId:', this.serviceId);
				console.log('sendData characteristicId:', this.characteristicId);
				const data = "1";
				// 将数据转为 ArrayBuffer 格式
				const buffer = new ArrayBuffer(data.length);
				const dataView = new Uint8Array(buffer);
				for (let i = 0; i < data.length; i++) {
					dataView[i] = data.charCodeAt(i); // 将字符串转为字节
				}

				uni.writeBLECharacteristicValue({
					deviceId: this.deviceId,
					serviceId: this.serviceId,
					characteristicId: this.characteristicId,
					value: buffer,
					success: () => {
					  console.log('数据发送成功:', data);
					},
					fail: (err) => {
					  console.error('数据发送失败:', err);
					}
				});
			}

		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
