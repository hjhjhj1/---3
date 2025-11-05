<template>
	<view class="message-container">
		<view class="message-header">
			<text class="header-title">消息</text>
		</view>
		<view class="message-list">
			<view class="message-item" v-for="(message, index) in messages" :key="index" @click="goToChat(message)">
				<image class="avatar" :src="message.avatar" mode="aspectFill"></image>
				<view class="message-content">
					<view class="user-info">
						<text class="username">{{message.username}}</text>
						<text class="time">{{message.time}}</text>
					</view>
					<text class="last-message">{{message.lastMessage}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			messages: [
				{
					avatar: '/static/logo.png',
					username: '张三',
					time: '10:30',
					lastMessage: '你好，今天天气怎么样？'
				},
				{
					avatar: '/static/logo.png',
					username: '李四',
					time: '9:15',
					lastMessage: '明天一起去看电影吗？'
				}
			]
		};
	},
	onLoad() {
		// 加载消息列表
	},
	methods: {
		// 跳转到聊天页面
		goToChat(message) {
			console.log('跳转到聊天页面：', message);
			uni.navigateTo({
				url: '/pages/chat/chat?username=' + message.username
			});
		}
	}
}
</script>

<style scoped>
.message-container {
	min-height: 100vh;
	background-color: #f5f5f5;
}

.message-header {
	padding: 20rpx;
	background-color: #fff;
	border-bottom: 1px solid #eee;
}

.header-title {
	font-size: 36rpx;
	font-weight: bold;
}

.message-list {
	padding: 20rpx;
}

.message-item {
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

.last-message {
	font-size: 28rpx;
	color: #666;
}
</style>