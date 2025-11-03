<template>
	<view class="settings-container">
		<view class="header">
			<text class="title">设置</text>
		</view>
		<view class="settings-list">
			<view class="setting-item">
				<text class="setting-label">头像</text>
				<view class="setting-content">
					<image class="avatar" :src="userInfo.avatar" mode="aspectFill" @click="chooseAvatar"></image>
					<text class="upload-text">上传头像</text>
				</view>
			</view>
			<view class="setting-item">
				<text class="setting-label">昵称</text>
				<view class="setting-content">
					<input type="text" v-model="userInfo.nickname" class="setting-input" placeholder="请输入昵称" />
				</view>
			</view>
			<view class="setting-item">
				<text class="setting-label">性别</text>
				<view class="setting-content">
					<radio-group @change="e => userInfo.gender = e.detail.value">
						<radio value="男" class="radio-item" :checked="userInfo.gender === '男'">男</radio>
						<radio value="女" class="radio-item" :checked="userInfo.gender === '女'">女</radio>
					</radio-group>
				</view>
			</view>
			<view class="setting-item">
				<text class="setting-label">个性签名</text>
				<view class="setting-content">
					<input type="text" v-model="userInfo.signature" class="setting-input" placeholder="请输入个性签名" />
				</view>
			</view>
			<view class="setting-item">
				<text class="setting-label">生日</text>
				<view class="setting-content">
					<input type="date" v-model="userInfo.birthday" class="setting-input" />
				</view>
			</view>
			<view class="setting-item">
				<text class="setting-label">所在地</text>
				<view class="setting-content">
					<view class="city-picker">
						<select v-model="selectedProvince" @change="onProvinceChange" class="city-select">
							<option value="">请选择省份</option>
							<option v-for="province in provinces" :key="province.id" :value="province.id">{{ province.name }}</option>
						</select>
						<select v-model="selectedCity" @change="onCityChange" class="city-select">
							<option value="">请选择城市</option>
							<option v-for="city in cities" :key="city.id" :value="city.id">{{ city.name }}</option>
						</select>
						<select v-model="selectedDistrict" @change="onDistrictChange" class="city-select">
							<option value="">请选择区县</option>
							<option v-for="district in districts" :key="district.id" :value="district.id">{{ district.name }}</option>
						</select>
					</view>
				</view>
			</view>
		</view>
		<view class="save-btn-container">
			<button class="save-btn" @click="saveUserInfo">保存</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
			return {
				userInfo: {
					avatar: '/static/logo.png',
					nickname: '',
					gender: '男',
					signature: '',
					birthday: '',
					location: ''
				},
				provinces: [],
				cities: [],
				districts: [],
				selectedProvince: '',
				selectedCity: '',
				selectedDistrict: ''
			};
		},
	onLoad() {
			// 加载用户信息
			this.loadUserInfo();
			// 加载省份数据
			this.loadProvinces();
		},
	methods: {
			// 加载用户信息
			loadUserInfo() {
				uni.getUserInfo({
					success: (res) => {
						this.userInfo.avatar = res.userInfo.avatarUrl;
						this.userInfo.nickname = res.userInfo.nickName;
					}
				});
				// 从本地存储加载用户信息（假设已保存）
				const savedUserInfo = uni.getStorageSync('userInfo');
				if (savedUserInfo) {
					this.userInfo = savedUserInfo;
					// 延迟执行回显操作，确保省份数据已加载
					setTimeout(() => {
						this.echoLocation(savedUserInfo.location);
					}, 1000);
				}
			},
			// 回显用户所在地
			echoLocation(location) {
				if (!location) return;
				const locationParts = location.split(' ');
				if (locationParts.length === 0) return;
				
				// 匹配省份
				const province = this.provinces.find(p => locationParts[0].includes(p.name));
				if (province) {
					this.selectedProvince = province.id;
					// 延迟加载城市数据
					setTimeout(() => {
						// 匹配城市
						if (locationParts.length > 1) {
							const city = this.cities.find(c => locationParts[1].includes(c.name));
							if (city) {
								this.selectedCity = city.id;
								// 延迟加载区县数据
								setTimeout(() => {
									// 匹配区县
									if (locationParts.length > 2) {
										const district = this.districts.find(d => locationParts[2].includes(d.name));
										if (district) {
											this.selectedDistrict = district.id;
										}
									}
								}, 500);
							}
						}
					}, 500);
				}
			},
			// 加载省份数据
			loadProvinces() {
				uni.request({
					url: 'https://way.jd.com/jisuapi/area',
					method: 'GET',
					data: {
						type: 'province',
						appkey: 'your_appkey_here' // 请替换为您自己的京东万象API密钥
					},
					success: (res) => {
						if (res.data.result.status === 0) {
							this.provinces = res.data.result.result;
						}
					}
				});
			},
			// 加载城市数据
			loadCities(provinceId) {
				uni.request({
					url: 'https://way.jd.com/jisuapi/area',
					method: 'GET',
					data: {
						type: 'city',
						parentid: provinceId,
						appkey: 'your_appkey_here' // 请替换为您自己的京东万象API密钥
					},
					success: (res) => {
						if (res.data.result.status === 0) {
							this.cities = res.data.result.result;
						}
					}
				});
			},
			// 加载区县数据
			loadDistricts(cityId) {
				uni.request({
					url: 'https://way.jd.com/jisuapi/area',
					method: 'GET',
					data: {
						type: 'county',
						parentid: cityId,
						appkey: 'your_appkey_here' // 请替换为您自己的京东万象API密钥
					},
					success: (res) => {
						if (res.data.result.status === 0) {
							this.districts = res.data.result.result;
						}
					}
				});
			},
			// 省份选择变化
			onProvinceChange() {
				this.selectedCity = '';
				this.selectedDistrict = '';
				this.cities = [];
				this.districts = [];
				if (this.selectedProvince) {
					this.loadCities(this.selectedProvince);
				}
				this.updateLocation();
			},
			// 城市选择变化
			onCityChange() {
				this.selectedDistrict = '';
				this.districts = [];
				if (this.selectedCity) {
					this.loadDistricts(this.selectedCity);
				}
				this.updateLocation();
			},
			// 区县选择变化
			onDistrictChange() {
				this.updateLocation();
			},
			// 更新用户所在地
			updateLocation() {
				const province = this.provinces.find(p => p.id === this.selectedProvince);
				const city = this.cities.find(c => c.id === this.selectedCity);
				const district = this.districts.find(d => d.id === this.selectedDistrict);
				const locationParts = [];
				if (province) locationParts.push(province.name);
				if (city) locationParts.push(city.name);
				if (district) locationParts.push(district.name);
				this.userInfo.location = locationParts.join(' ');
			},
		// 选择头像
		chooseAvatar() {
			uni.chooseImage({
				count: 1,
				sizeType: ['original', 'compressed'],
				sourceType: ['album', 'camera'],
				success: (res) => {
					const tempFilePaths = res.tempFilePaths;
					// 上传头像到服务器
					uni.uploadFile({
						url: 'https://example.com/upload',
						filePath: tempFilePaths[0],
						name: 'file',
						success: (res) => {
							const avatarUrl = JSON.parse(res.data).url;
							this.userInfo.avatar = avatarUrl;
						}
					});
				}
			});
		},
		// 保存用户信息
			saveUserInfo() {
				uni.request({
					url: 'https://example.com/updateUserInfo',
					method: 'POST',
					data: this.userInfo,
					success: (res) => {
						uni.showToast({
							title: '保存成功',
							icon: 'success'
						});
						// 保存到本地存储
						uni.setStorageSync('userInfo', this.userInfo);
					}
				});
			}
	}
}
</script>

<style scoped>
	.settings-container {
		min-height: 100vh;
		background-color: #f5f5f5;
	}

	.header {
		padding: 20rpx;
		background-color: #fff;
		border-bottom: 1px solid #eee;
	}

	.title {
		font-size: 36rpx;
		font-weight: bold;
	}

	.settings-list {
		padding: 20rpx;
	}

	.setting-item {
		background-color: #fff;
		border-radius: 10rpx;
		margin-bottom: 20rpx;
		overflow: hidden;
	}

	.setting-label {
		padding: 20rpx;
		font-size: 32rpx;
		font-weight: bold;
	}

	.setting-content {
		padding: 20rpx;
		border-top: 1px solid #eee;
	}

	.avatar {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
		margin-right: 20rpx;
	}

	.upload-text {
		font-size: 28rpx;
		color: #007aff;
	}

	.setting-input {
		width: 100%;
		height: 60rpx;
		font-size: 28rpx;
	}

	.radio-group {
		display: flex;
		align-items: center;
	}

	.radio-item {
		margin-right: 40rpx;
	}

	.save-btn-container {
		padding: 20rpx;
	}

	.save-btn {
		background-color: #007aff;
		color: #fff;
		border: none;
		border-radius: 20rpx;
		padding: 0 40rpx;
		height: 60rpx;
		font-size: 28rpx;
		width: 100%;
	}

	.city-picker {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.city-select {
		width: 30%;
		height: 60rpx;
		font-size: 28rpx;
		border: 1px solid #eee;
		border-radius: 5rpx;
		padding: 0 10rpx;
	}
</style>