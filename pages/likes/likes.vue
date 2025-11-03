<template>
	<view class="likes-container">
		<view class="header">
			<text class="title">收到的点赞</text>
		</view>
		<view class="likes-list">
			<view class="like-item" v-for="(like, index) in likes" :key="index">
				<image class="avatar" :src="like.avatar" mode="aspectFill"></image>
				<view class="like-content">
					<view class="user-info">
						<text class="username">{{like.username}}</text>
						<text class="time">{{like.time}}</text>
					</view>
					<text class="like-text">点赞了你的帖子</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			likes: [
				{
					avatar: '/static/logo.png',
					username: '张三',
					time: '2022-01-01 12:00:00'
				},
				{
					avatar: '/static/logo.png',
					username: '李四',
					time: '2022-01-02 13:00:00'
				}
			]
		};
	},
	onLoad() {
		// 加载用户收到的点赞
	},
	methods: {
		// 跳转到用户个人主页
		goToUserProfile(like) {
			uni.navigateTo({
				url: '/pages/user-profile/user-profile?username=' + like.username
			});
		}
	}
}
</script>

<style scoped>
.likes-container {
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

.likes-list {
	padding: 20rpx;
}

.like-item {
	display: flex;
	align-items: flex-start;
	padding: 20rpx;
	background-color: #fff;
	border-radius: 10rpx;
	margin-bottom: 20rpx;
}

.avatar {
	width: 80rpx;
	height: 80rpx;
	border-radius: 50%;
	margin-right: 20rpx;
}

.like-content {
	display: flex;
	flex-direction: column;
	flex: 1;
}

.user-info {
	display: flex;
	justify-content: space-between;
	align-items: center;
	margin-bottom: 10rpx;
}

.username {
	font-size: 32rpx;
	font-weight: bold;
}

.time {
	font-size: 24rpx;
	color: #999;
}

.like-text {
	font-size: 28rpx;
	color: #666;
}
</style>