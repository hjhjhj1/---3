<template>
	<view class="my-posts-container">
		<view class="header">
			<text class="title">我的帖子</text>
		</view>
		<view class="posts-list">
			<view class="post-item" v-for="(post, index) in posts" :key="index" @click="goToPostDetail(post)">
				<image class="post-image" :src="post.image" mode="aspectFill"></image>
				<view class="post-content">
					<text class="post-title">{{post.title}}</text>
					<text class="post-text">{{post.text}}</text>
					<view class="post-meta">
						<text class="post-time">{{post.time}}</text>
						<view class="post-stats">
							<text class="stat-item">👍 {{post.likes}}</text>
							<text class="stat-item">💬 {{post.comments}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			posts: [
				{
					id: 1,
					title: '今天天气很好',
					text: '今天天气真的很好，适合出去散步。',
					image: '/static/logo.png',
					time: '2022-01-01 12:00:00',
					likes: 10,
					comments: 5
				},
				{
					id: 2,
					title: '分享一张照片',
					text: '这是我昨天拍的照片，是不是很好看？',
					image: '/static/logo.png',
					time: '2022-01-02 13:00:00',
					likes: 20,
					comments: 8
				}
			]
		};
	},
	onLoad() {
		// 加载用户发布的帖子
	},
	methods: {
		// 跳转到帖子详情页
		goToPostDetail(post) {
			uni.navigateTo({
				url: '/pages/post-detail/post-detail?id=' + post.id
			});
		}
	}
}
</script>

<style scoped>
.my-posts-container {
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

.posts-list {
	padding: 20rpx;
}

.post-item {
	background-color: #fff;
	border-radius: 10rpx;
	margin-bottom: 20rpx;
	overflow: hidden;
}

.post-image {
	width: 100%;
	height: 400rpx;
}

.post-content {
	padding: 20rpx;
}

.post-title {
	font-size: 32rpx;
	font-weight: bold;
	margin-bottom: 10rpx;
}

.post-text {
	font-size: 28rpx;
	color: #666;
	margin-bottom: 20rpx;
}

.post-meta {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.post-time {
	font-size: 24rpx;
	color: #999;
}

.post-stats {
	display: flex;
	align-items: center;
}

.stat-item {
	font-size: 24rpx;
	color: #999;
	margin-left: 20rpx;
}
</style>