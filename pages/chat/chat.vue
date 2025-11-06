<template>
	<view class="chat-container">
		<!-- 顶部导航栏 -->
		<view class="chat-header">
			<view class="back-btn" @click="goBack">
				<text class="icon">←</text>
			</view>
			<view class="user-info">
				<image class="avatar" :src="chatUser.avatar" mode="aspectFill"></image>
				<view class="user-details">
					<text class="nickname">{{ chatUser.nickname }}</text>
					<view class="last-message-info">
						<text class="last-message">{{ getLastMessagePreview() }}</text>
						<text class="last-message-time">{{ formatTime(getLastMessageTime()) }}</text>
					</view>
				</view>
			</view>
			<view class="more-btn">
				<text class="icon">⋮</text>
			</view>
		</view>

		<!-- 聊天记录区域 -->
		<scroll-view 
			class="chat-messages" 
			scroll-y 
			@scrolltolower="loadMoreMessages" 
			@scrolltoupper="refreshMessages" 
			ref="chatScroll"
		>
			<!-- 加载更多提示 -->
			<view class="load-more" v-if="loadingMore">
				<text>加载中...</text>
			</view>

			<!-- 聊天消息列表 -->
			<view class="message-list">
				<view 
					v-for="(message, index) in messages" 
					:key="message.id" 
					class="message-item" 
					:class="{ 'sent': message.senderId === userId, 'received': message.senderId !== userId }"
				>
					<!-- 头像 -->
					<image 
						class="message-avatar" 
						:src="message.senderId === userId ? userAvatar : chatUser.avatar" 
						mode="aspectFill"
					></image>

					<!-- 消息内容 -->
					<view class="message-content">
						<!-- 文本消息 -->
						<view 
							v-if="message.type === 'text'" 
							class="message-bubble" 
							:class="{ 'sent': message.senderId === userId, 'received': message.senderId !== userId }"
						>
							<text class="message-text">{{ message.content }}</text>
						</view>

						<!-- 图片消息 -->
						<view 
							v-else-if="message.type === 'image'" 
							class="message-bubble" 
							:class="{ 'sent': message.senderId === userId, 'received': message.senderId !== userId }"
						>
							<image 
								class="message-image" 
								:src="message.content" 
								mode="aspectFill" 
								@click="previewImage(message.content)"
								lazy-load="true"
							></image>
						</view>

						<!-- 消息时间 -->
						<view class="message-time">
							<text>{{ formatTime(message.timestamp) }}</text>
						</view>
					</view>
				</view>
			</view>

			<!-- 下拉刷新提示 -->
			<view class="refresh-tip" v-if="refreshing">
				<text>刷新中...</text>
			</view>
		</scroll-view>

		<!-- 聊天输入区域 -->
		<view class="chat-input-container">
			<!-- 表情按钮 -->
			<view class="input-btn emoji-btn" @click="toggleEmojiPicker">
				<text class="icon">😊</text>
			</view>

			<!-- 图片按钮 -->
			<view class="input-btn image-btn" @click="selectImage">
				<text class="icon">🖼️</text>
			</view>

			<!-- 文本输入框 -->
			<textarea 
				class="chat-input" 
				v-model="inputContent" 
				placeholder="输入消息..." 
				auto-height 
				maxlength="500"
				@input="adjustInputHeight"
			></textarea>

			<!-- 发送按钮 -->
			<view class="send-btn" @click="sendMessage" :disabled="!inputContent.trim()">
				<text>发送</text>
			</view>
		</view>

		<!-- 表情选择器 -->
		<view class="emoji-picker" v-if="showEmojiPicker">
			<!-- 表情列表 -->
			<scroll-view class="emoji-list" scroll-x>
				<view class="emoji-item" v-for="emoji in emojis" :key="emoji" @click="insertEmoji(emoji)">
					<text>{{ emoji }}</text>
				</view>
			</scroll-view>
		</view>

		<!-- 图片预览模态框 -->
		<view class="modal" v-if="showImagePreview" @click="closeImagePreview">
			<view class="modal-content" @click.stop>
				<image class="preview-image" :src="previewImageUrl" mode="aspectFit"></image>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 聊天用户信息
			chatUser: {
				id: 2,
				nickname: '聊天对象',
				avatar: '/static/logo.png',
				lastMessage: '这是最后一条消息的内容',
				lastMessageTime: Date.now() - 300000 // 5分钟前
			},
			
			// 当前用户信息
			userId: 1,
			userAvatar: '/static/logo.png',
			
			// 聊天消息列表
			messages: [
				{
					id: 1,
					senderId: 2,
					type: 'text',
					content: '你好！',
					timestamp: Date.now() - 7200000 // 2小时前
				},
				{
					id: 2,
					senderId: 1,
					type: 'text',
					content: '你好！很高兴认识你。',
					timestamp: Date.now() - 7100000 // 1小时58分钟前
				},
				{
					id: 3,
					senderId: 2,
					type: 'image',
					content: '/static/logo.png',
					timestamp: Date.now() - 3600000 // 1小时前
				},
				{
					id: 4,
					senderId: 1,
					type: 'text',
					content: '这张图片很有趣！',
					timestamp: Date.now() - 3500000 // 58分钟前
				}
			],
			
			// 输入内容
			inputContent: '',
			
			// 加载状态
			loadingMore: false,
			refreshing: false,
			
			// 表情选择器
			showEmojiPicker: false,
			emojis: ['😀', '😃', '😄', '😁', '😆', '😅', '😂', '🤣', '😊', '😇', '🙂', '🙃', '😉', '😌', '😍', '🥰', '😘', '😗', '😙', '😚', '😋', '😛', '😝', '😜', '🤪', '🤨', '🧐', '🤓', '😎', '🤩', '🥳', '😏', '😒', '😞', '😔', '😟', '😕', '🙁', '☹️', '😣', '😖', '😫', '😩', '🥺', '😢', '😭', '😤', '😠', '😡', '🤬', '🤯', '😳', '🥵', '🥶', '😱', '😨', '😰', '😥', '😓', '🤗', '🤔', '🤭', '🤫', '🤥', '😶', '😐', '😑', '😬', '🙄', '😯', '😴', '🤤', '😪', '😵', '🤐', '🥴', '🤢', '🤮', '🤧', '😷', '🤒', '🤕', '🤑'],
			
			// 图片预览
			showImagePreview: false,
			previewImageUrl: ''
		};
	},
	
	onLoad() {
		// 页面加载时滚动到底部
		this.scrollToBottom();
	},
	
	methods: {
		// 返回上一页
		goBack() {
			uni.navigateBack();
		},
		
		// 获取最后一条消息预览
		getLastMessagePreview() {
			if (this.messages.length === 0) return '';
			const lastMessage = this.messages[this.messages.length - 1];
			if (lastMessage.type === 'image') {
				return '[图片]';
			} else {
				return lastMessage.content.length > 20 ? lastMessage.content.substring(0, 20) + '...' : lastMessage.content;
			}
		},
		
		// 获取最后一条消息时间
		getLastMessageTime() {
			if (this.messages.length === 0) return Date.now();
			return this.messages[this.messages.length - 1].timestamp;
		},
		
		// 格式化时间为HH:MM
		formatTime(timestamp) {
			const date = new Date(timestamp);
			const hours = date.getHours().toString().padStart(2, '0');
			const minutes = date.getMinutes().toString().padStart(2, '0');
			return `${hours}:${minutes}`;
		},
		
		// 加载更多历史消息
		loadMoreMessages() {
			if (this.loadingMore) return;
			
			this.loadingMore = true;
			
			// 模拟加载更多数据
			setTimeout(() => {
				// 这里可以添加API调用获取更多历史消息
				console.log('加载更多历史消息');
				this.loadingMore = false;
			}, 1000);
		},
		
		// 刷新获取最新消息
		refreshMessages() {
			if (this.refreshing) return;
			
			this.refreshing = true;
			
			// 模拟刷新数据
			setTimeout(() => {
				// 这里可以添加API调用获取最新消息
				console.log('刷新获取最新消息');
				this.refreshing = false;
			}, 1000);
		},
		
		// 发送消息
		sendMessage() {
			if (!this.inputContent.trim()) return;
			
			// 创建新消息
			const newMessage = {
				id: Date.now(),
				senderId: this.userId,
				type: 'text',
				content: this.inputContent.trim(),
				timestamp: Date.now()
			};
			
			// 添加到消息列表
			this.messages.push(newMessage);
			
			// 清空输入框
			this.inputContent = '';
			
			// 滚动到底部
			this.scrollToBottom();
			
			// 模拟发送消息API调用
			setTimeout(() => {
				// 这里可以添加发送消息的API调用
				console.log('发送消息:', newMessage);
			}, 500);
		},
		
		// 选择图片
		selectImage() {
			uni.chooseImage({
				count: 1,
				sizeType: ['compressed'],
				sourceType: ['album', 'camera'],
				success: (res) => {
					// 创建图片消息
					const newMessage = {
						id: Date.now(),
						senderId: this.userId,
						type: 'image',
						content: res.tempFilePaths[0],
						timestamp: Date.now()
					};
					
					// 添加到消息列表
					this.messages.push(newMessage);
					
					// 滚动到底部
					this.scrollToBottom();
					
					// 模拟上传图片API调用
					setTimeout(() => {
						// 这里可以添加上传图片的API调用
						console.log('发送图片:', newMessage);
					}, 1000);
				}
			});
		},
		
		// 预览图片
		previewImage(imageUrl) {
			this.previewImageUrl = imageUrl;
			this.showImagePreview = true;
		},
		
		// 关闭图片预览
		closeImagePreview() {
			this.showImagePreview = false;
			this.previewImageUrl = '';
		},
		
		// 切换表情选择器
		toggleEmojiPicker() {
			this.showEmojiPicker = !this.showEmojiPicker;
		},
		
		// 插入表情
		insertEmoji(emoji) {
			this.inputContent += emoji;
		},
		
		// 调整输入框高度
		adjustInputHeight() {
			// 自动高度由uni-app的textarea组件处理
		},
		
		// 滚动到底部
		scrollToBottom() {
			this.$nextTick(() => {
				if (this.$refs.chatScroll) {
					this.$refs.chatScroll.scrollToBottom(300);
				}
			});
		}
	}
};
</script>

<style scoped>
/* 全局样式 */
.chat-container {
	width: 100%;
	height: 100vh;
	display: flex;
	flex-direction: column;
	background-color: #f5f5f5;
}

/* 顶部导航栏 */
.chat-header {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10rpx 20rpx;
	background-color: #fff;
	border-bottom: 1rpx solid #eee;
	height: 80rpx;
	box-sizing: border-box;
}

.back-btn, .more-btn {
	width: 60rpx;
	height: 60rpx;
	display: flex;
	align-items: center;
	justify-content: center;
}

.icon {
	font-size: 32rpx;
	color: #333;
}

.user-info {
	display: flex;
	align-items: center;
	flex: 1;
	margin: 0 20rpx;
}

.avatar {
	width: 60rpx;
	height: 60rpx;
	border-radius: 50%;
	margin-right: 15rpx;
}

.user-details {
	display: flex;
	flex-direction: column;
	justify-content: center;
}

.nickname {
	font-size: 28rpx;
	font-weight: bold;
	color: #333;
	margin-bottom: 4rpx;
}

.last-message-info {
	display: flex;
	align-items: center;
	font-size: 22rpx;
	color: #999;
}

.last-message {
	flex: 1;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
	margin-right: 10rpx;
}

.last-message-time {
	min-width: 80rpx;
}

/* 聊天记录区域 */
.chat-messages {
	flex: 1;
	padding: 20rpx;
	box-sizing: border-box;
}

.load-more, .refresh-tip {
	text-align: center;
	padding: 10rpx 0;
	font-size: 24rpx;
	color: #999;
}

.message-list {
	margin-bottom: 20rpx;
}

.message-item {
	display: flex;
	margin-bottom: 20rpx;
	align-items: flex-end;
}

.message-item.sent {
	justify-content: flex-end;
}

.message-item.received {
	justify-content: flex-start;
}

.message-avatar {
	width: 50rpx;
	height: 50rpx;
	border-radius: 50%;
	margin: 0 10rpx;
}

.message-content {
	display: flex;
	flex-direction: column;
	align-items: flex-end;
}

.message-item.received .message-content {
	align-items: flex-start;
}

.message-bubble {
	max-width: 60%;
	padding: 15rpx 20rpx;
	border-radius: 20rpx;
	margin-bottom: 5rpx;
}

.message-bubble.sent {
	background-color: #007aff;
	color: #fff;
	border-bottom-right-radius: 5rpx;
}

.message-bubble.received {
	background-color: #fff;
	color: #333;
	border-bottom-left-radius: 5rpx;
}

.message-text {
	font-size: 28rpx;
	line-height: 1.5;
	word-break: break-all;
}

.message-image {
	max-width: 200rpx;
	max-height: 200rpx;
	border-radius: 15rpx;
}

.message-time {
	font-size: 22rpx;
	color: #999;
}

/* 聊天输入区域 */
.chat-input-container {
	display: flex;
	align-items: flex-end;
	padding: 10rpx 20rpx;
	background-color: #fff;
	border-top: 1rpx solid #eee;
}

.input-btn {
	width: 60rpx;
	height: 60rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-right: 10rpx;
}

.chat-input {
	flex: 1;
	min-height: 60rpx;
	max-height: 180rpx;
	padding: 15rpx;
	border: 2rpx solid #eee;
	border-radius: 30rpx;
	font-size: 28rpx;
	resize: none;
	margin-right: 10rpx;
}

.send-btn {
	background-color: #007aff;
	color: #fff;
	padding: 15rpx 30rpx;
	border-radius: 30rpx;
	font-size: 28rpx;
}

.send-btn:disabled {
	background-color: #ccc;
}

/* 表情选择器 */
.emoji-picker {
	background-color: #fff;
	border-top: 1rpx solid #eee;
	padding: 20rpx;
}

.emoji-list {
	display: flex;
}

.emoji-item {
	width: 60rpx;
	height: 60rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	font-size: 40rpx;
	margin-right: 20rpx;
}

/* 图片预览模态框 */
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
	width: 90%;
	height: 80%;
}

.preview-image {
	width: 100%;
	height: 100%;
	object-fit: contain;
}

/* 响应式设计 */
@media (min-width: 768px) {
	.chat-container {
		max-width: 600px;
		margin: 0 auto;
		border-left: 1rpx solid #eee;
		border-right: 1rpx solid #eee;
	}
}
</style>