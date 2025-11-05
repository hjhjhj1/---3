<template>
	<view class="container" @click="hideMessageInput">
		<!-- 帖子列表 -->
		<scroll-view class="post-list" scroll-y>
			<view class="post-card" v-for="post in posts" :key="post.id" @click.stop="showMessageInput(post.id)">
				<!-- 帖子标题 -->
				<view class="post-title">{{ post.title }}</view>
				
				<!-- 帖子图片 -->
				<image class="post-image" :src="post.image" @click.stop="previewImage(post.image)"></image>
				
				<!-- 帖子元信息 -->
				<view class="post-meta">
					<text class="post-time">{{ formatTime(post.time) }}</text>
					<text class="post-author">by {{ post.author }}</text>
				</view>
				
				<!-- 点赞按钮 -->
				<view class="post-actions">
					<button class="like-btn" :class="{ 'liked': post.liked }" @click.stop="likePost(post.id)">
						{{ post.likes }}
					</button>
				</view>
				
				<!-- 私信输入框 -->
				<view class="message-input" v-if="showingInputId === post.id">
					<input 
						type="text" 
						v-model="messageContent[post.id]" 
						placeholder="发送私信..." 
						class="message-input-field"
						@click.stop
					/>
					<button class="send-btn" @click.stop="sendMessage(post.id)">发送</button>
				</view>
			</view>
		</scroll-view>
		
		<!-- 图片预览模态框 -->
		<view class="modal" v-if="showPreview" @click="closePreview">
			<view class="modal-content" @click.stop>
				<button class="close-btn" @click.stop="closePreview">×</button>
				<image class="preview-image" :src="previewImageUrl" mode="aspectFit"></image>
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
					title: '这是第一个帖子',
					image: '/static/logo.png',
					time: Date.now() - 3600000, // 1小时前
					author: '用户1',
					likes: 10,
					liked: false
				},
				{
					id: 2,
					title: '这是第二个帖子',
					image: '/static/logo.png',
					time: Date.now() - 7200000, // 2小时前
					author: '用户2',
					likes: 5,
					liked: false
				},
				{
					id: 3,
					title: '这是第三个帖子',
					image: '/static/logo.png',
					time: Date.now() - 86400000, // 1天前
					author: '用户3',
					likes: 20,
					liked: false
				}
			],
			showingInputId: null,
			messageContent: {},
			previewImageUrl: '',
			showPreview: false
		};
	},
	methods: {
		// 格式化时间为几分钟/小时/天之前
		formatTime(timestamp) {
			const now = Date.now();
			const diff = now - timestamp;
			const minutes = Math.floor(diff / 60000);
			const hours = Math.floor(diff / 3600000);
			const days = Math.floor(diff / 86400000);
			
			if (days > 0) {
				return `${days}天前`;
			} else if (hours > 0) {
				return `${hours}小时前`;
			} else if (minutes > 0) {
				return `${minutes}分钟前`;
			} else {
				return '刚刚';
			}
		},
		
		// 显示私信输入框
		showMessageInput(postId) {
			this.showingInputId = postId;
			// 自动聚焦输入框
			this.$nextTick(() => {
				uni.createSelectorQuery().select(`.message-input-field`).focus();
			});
		},
		
		// 隐藏私信输入框
		hideMessageInput() {
			this.showingInputId = null;
		},
		
		// 发送私信
		sendMessage(postId) {
			const content = this.messageContent[postId] || '';
			if (!content.trim()) {
				uni.showToast({
					title: '请输入消息内容',
					icon: 'none'
				});
				return;
			}
			
			// 这里可以添加发送私信的API调用
			console.log('发送私信给', this.posts.find(p => p.id === postId).author, '内容:', content);
			
			// 显示发送成功提示
			uni.showToast({
				title: '发送成功',
				icon: 'success'
			});
			
			// 清空输入框
			this.messageContent[postId] = '';
		},
		
		// 点赞帖子
		likePost(postId) {
			const post = this.posts.find(p => p.id === postId);
			if (!post || post.liked) return; // 防重复点击
			
			post.likes++;
			post.liked = true;
			
			// 这里可以添加点赞的API调用
			console.log('点赞帖子', postId);
		},
		
		// 预览图片
		previewImage(imageUrl) {
			this.previewImageUrl = imageUrl;
			this.showPreview = true;
		},
		
		// 关闭图片预览
		closePreview() {
			this.showPreview = false;
			this.previewImageUrl = '';
		}
	}
};
</script>

<style scoped>
.container {
	padding: 20rpx;
	background-color: #f5f5f5;
	min-height: 100vh;
}

.post-list {
	height: calc(100vh - 40rpx);
}

.post-card {
	background-color: #fff;
	border-radius: 10rpx;
	padding: 20rpx;
	margin-bottom: 20rpx;
	box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.1);
}

.post-title {
	font-size: 32rpx;
	font-weight: bold;
	margin-bottom: 15rpx;
	color: #333;
}

.post-image {
	width: 100%;
	height: 400rpx;
	object-fit: cover;
	border-radius: 8rpx;
	margin-bottom: 15rpx;
}

.post-meta {
	font-size: 24rpx;
	color: #999;
	margin-bottom: 15rpx;
	display: flex;
	justify-content: space-between;
}

.post-actions {
	display: flex;
	justify-content: flex-end;
	margin-bottom: 15rpx;
}

.like-btn {
	background-color: #fff;
	border: 2rpx solid #ddd;
	border-radius: 20rpx;
	padding: 8rpx 20rpx;
	font-size: 24rpx;
	color: #666;
}

.like-btn.liked {
	background-color: #ff4444;
	border-color: #ff4444;
	color: #fff;
}

.message-input {
	display: flex;
	margin-top: 15rpx;
}

.message-input-field {
	flex: 1;
	border: 2rpx solid #ddd;
	border-radius: 20rpx;
	padding: 10rpx 15rpx;
	font-size: 24rpx;
	margin-right: 10rpx;
}

.send-btn {
	background-color: #007aff;
	color: #fff;
	border: none;
	border-radius: 20rpx;
	padding: 0 25rpx;
	font-size: 24rpx;
}

.modal {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.8);
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 1000;
}

.modal-content {
	position: relative;
	width: 90%;
	height: 80%;
}

.close-btn {
	position: absolute;
	top: -40rpx;
	right: -40rpx;
	background-color: rgba(0, 0, 0, 0.5);
	color: #fff;
	border: none;
	border-radius: 50%;
	width: 60rpx;
	height: 60rpx;
	font-size: 40rpx;
	line-height: 60rpx;
	text-align: center;
}

.preview-image {
	width: 100%;
	height: 100%;
	object-fit: contain;
}
</style>
