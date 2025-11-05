<template>
	<view class="login-container">
		<view class="login-form">
			<image class="logo" src="/static/logo.png" mode="aspectFit"></image>
			<view class="form-title">纸飞机交友</view>
			<uni-input
				v-model="username"
				placeholder="请输入用户名"
				class="input-item"
			></uni-input>
			<uni-input
				v-model="password"
				placeholder="请输入密码"
				type="password"
				class="input-item"
			></uni-input>
			<uni-button
				type="primary"
				class="login-btn"
				@click="login"
			>
				登录
			</uni-button>
			<view class="register-link">
				<text>还没有账号？</text>
				<text class="link" @click="goToRegister">立即注册</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			username: '',
			password: ''
		};
	},
	methods: {
		login() {
			// 登录逻辑
			uni.showToast({
				title: '登录功能开发中',
				icon: 'none'
			});
		},
		goToRegister() {
			// 跳转到注册页面
			uni.showToast({
				title: '注册功能开发中',
				icon: 'none'
			});
		}
	}
};
</script>

<style scoped>
.login-container {
	display: flex;
	justify-content: center;
	align-items: center;
	height: 100vh;
	background-color: #f5f5f5;
}

.login-form {
	width: 90%;
	max-width: 400px;
	background-color: #fff;
	padding: 40rpx;
	border-radius: 16rpx;
	box-shadow: 0 0 20rpx rgba(0, 0, 0, 0.1);
}

.logo {
	width: 120rpx;
	height: 120rpx;
	margin: 0 auto 30rpx;
	display: block;
}

.form-title {
	font-size: 36rpx;
	font-weight: bold;
	text-align: center;
	margin-bottom: 40rpx;
	color: #333;
}

.input-item {
	margin-bottom: 30rpx;
}

.login-btn {
	margin-bottom: 20rpx;
}

.register-link {
	text-align: center;
	font-size: 28rpx;
	color: #666;
}

.link {
	color: #3cc51f;
	margin-left: 10rpx;
}
</style>