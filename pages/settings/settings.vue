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
					<input type="text" v-model="userInfo.location" class="setting-input" placeholder="请输入所在地" />
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
			}
		};
	},
	onLoad() {
		// 加载用户信息
		this.loadUserInfo();
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
</style>