<template>
	<view class="profile-container">
		<view class="profile-header">
			<image class="avatar" src="/static/logo.png" mode="aspectFill"></image>
			<view class="user-info">
				<text class="username">张三</text>
				<text class="gender">男</text>
			</view>
		</view>
		<view class="profile-stats">
			<view class="stat-item">
				<text class="stat-number">10</text>
				<text class="stat-label">帖子</text>
			</view>
			<view class="stat-item">
				<text class="stat-number">20</text>
				<text class="stat-label">点赞</text>
			</view>
			<view class="stat-item">
				<text class="stat-number">5</text>
				<text class="stat-label">评论</text>
			</view>
			<view class="stat-item">
				<text class="stat-number">3</text>
				<text class="stat-label">私聊</text>
			</view>
		</view>
		<view class="profile-menu">
			<view class="menu-item" @click="goToMyPosts">
				<text class="menu-text">我的帖子</text>
			</view>
			<view class="menu-item" @click="goToLikes">
				<text class="menu-text">收到的点赞</text>
			</view>
			<view class="menu-item" @click="goToComments">
				<text class="menu-text">收到的评论</text>
			</view>
			<view class="menu-item" @click="goToSettings">
				<text class="menu-text">设置</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 用户信息
		};
	},
	onLoad() {
		// 加载用户信息
	},
	methods: {
		// 跳转到我的帖子页面
		goToMyPosts() {
			uni.navigateTo({
				url: '/pages/my-posts/my-posts'
			});
		},
		// 跳转到收到的点赞页面
		goToLikes() {
			uni.navigateTo({
				url: '/pages/likes/likes'
			});
		},
		// 跳转到收到的评论页面
		goToComments() {
			uni.navigateTo({
				url: '/pages/comments/comments'
			});
		},
		// 跳转到设置页面
		goToSettings() {
			uni.navigateTo({
				url: '/pages/settings/settings'
			});
		}
	}
}
</script>

<style scoped>
.profile-container {
	min-height: 100vh;
	background-color: #f5f5f5;
}

.profile-header {
	display: flex;
	align-items: center;
	padding: 40rpx;
	background-color: #fff;
}

.avatar {
	width: 120rpx;
	height: 120rpx;
	border-radius: 50%;
	margin-right: 30rpx;
}

.user-info {
	display: flex;
	flex-direction: column;
}

.username {
	font-size: 36rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
}

.gender {
	font-size: 28rpx;
	color: #666;
}

.profile-stats {
	display: flex;
	justify-content: space-around;
	padding: 20rpx;
	background-color: #fff;
	margin-bottom: 20rpx;
}

.stat-item {
	display: flex;
	flex-direction: column;
	align-items: center;
}

.stat-number {
	font-size: 32rpx;
	font-weight: bold;
	margin-bottom: 5rpx;
}

.stat-label {
	font-size: 24rpx;
	color: #999;
}

.profile-menu {
	background-color: #fff;
}

.menu-item {
	padding: 30rpx;
	border-bottom: 1px solid #eee;
}

.menu-text {
	font-size: 32rpx;
}
</style>