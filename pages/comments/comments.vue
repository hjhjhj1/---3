<template>
	<view class="comments-container">
		<view class="header">
			<text class="title">收到的评论</text>
		</view>
		<view class="comments-list">
			<view class="comment-item" v-for="(comment, index) in comments" :key="index">
				<image class="avatar" :src="comment.avatar" mode="aspectFill"></image>
				<view class="comment-content">
					<view class="user-info">
						<text class="username">{{comment.username}}</text>
						<text class="time">{{comment.time}}</text>
					</view>
					<text class="comment-text">{{comment.text}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			comments: [
				{
					avatar: '/static/logo.png',
					username: '张三',
					time: '2022-01-01 12:00:00',
					text: '这篇帖子写得真好！'
				},
				{
					avatar: '/static/logo.png',
					username: '李四',
					time: '2022-01-02 13:00:00',
					text: '我也这么觉得。'
				}
			]
		};
	},
	onLoad() {
		// 加载用户收到的评论
	},
	methods: {
		// 跳转到用户个人主页
		goToUserProfile(comment) {
			uni.navigateTo({
				url: '/pages/user-profile/user-profile?username=' + comment.username
			});
		}
	}
}
</script>

<style scoped>
.comments-container {
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

.comments-list {
	padding: 20rpx;
}

.comment-item {
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

.comment-content {
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

.comment-text {
	font-size: 28rpx;
	color: #666;
}
</style>