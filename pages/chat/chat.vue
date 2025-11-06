<template>
	<view class="chat-container">
		<view class="chat-header">
			<image class="avatar" src="/static/logo.png" mode="aspectFill"></image>
			<view class="user-info">
				<text class="username">张三</text>
				<text class="status">在线</text>
			</view>
		</view>
		<view class="chat-messages">
			<view class="message-item" v-for="(message, index) in messages" :key="index">
				<image class="message-avatar" :src="message.avatar" mode="aspectFill"></image>
				<view class="message-content" :class="message.sender === 'me' ? 'me' : 'other'">
					<text class="message-text">{{message.text}}</text>
				</view>
			</view>
		</view>
		<view class="chat-input">
			<view class="input-container">
				<input type="text" v-model="inputText" placeholder="输入消息" class="input-text" />
				<view class="input-actions">
					<button class="action-btn" @click="sendImage">
						<text class="action-icon">📷</text>
					</button>
					<button class="action-btn" @click="sendVoice">
						<text class="action-icon">🎤</text>
					</button>
				</view>
			</view>
			<button class="send-btn" @click="sendMessage">发送</button>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			inputText: '',
			messages: [
				{
					avatar: '/static/logo.png',
					text: '你好，今天天气怎么样？',
					sender: 'other'
				},
				{
					avatar: '/static/logo.png',
					text: '今天天气很好，适合出去散步。',
					sender: 'me'
				}
			]
		};
	},
	onLoad() {
		// 加载聊天记录
	},
	methods: {
		// 发送消息
		sendMessage() {
			if (this.inputText.trim() === '') return;
			const newMessage = {
				avatar: '/static/logo.png',
				text: this.inputText,
				sender: 'me'
			};
			this.messages.push(newMessage);
			this.inputText = '';
			// 发送消息到服务器
		},
		// 发送图片
		sendImage() {
			uni.chooseImage({
				count: 1,
				sizeType: ['original', 'compressed'],
				sourceType: ['album', 'camera'],
				success: (res) => {
					const tempFilePaths = res.tempFilePaths;
					// 上传图片到服务器
					uni.uploadFile({
						url: 'https://example.com/upload',
						filePath: tempFilePaths[0],
						name: 'file',
						success: (res) => {
							const imageUrl = JSON.parse(res.data).url;
							const newMessage = {
								avatar: '/static/logo.png',
								text: imageUrl,
								sender: 'me',
								type: 'image'
							};
							this.messages.push(newMessage);
						}
					});
				}
			});
		},
		// 发送语音
		sendVoice() {
			uni.startRecord({
				success: (res) => {
					const tempFilePath = res.tempFilePath;
					// 上传语音到服务器
					uni.uploadFile({
						url: 'https://example.com/upload',
						filePath: tempFilePath,
						name: 'file',
						success: (res) => {
							const voiceUrl = JSON.parse(res.data).url;
							const newMessage = {
								avatar: '/static/logo.png',
								text: voiceUrl,
								sender: 'me',
								type: 'voice'
							};
							this.messages.push(newMessage);
						}
					});
				}
			});
		}
	}
}
</script>

<style scoped>
.chat-container {
	min-height: 100vh;
	background-color: #f5f5f5;
}

.chat-header {
	display: flex;
	align-items: center;
	padding: 20rpx;
	background-color: #fff;
	border-bottom: 1px solid #eee;
}

.avatar {
	width: 80rpx;
	height: 80rpx;
	border-radius: 50%;
	margin-right: 20rpx;
}

.user-info {
	display: flex;
	flex-direction: column;
}

.username {
	font-size: 32rpx;
	font-weight: bold;
}

.status {
	font-size: 24rpx;
	color: #999;
}

.chat-messages {
	padding: 20rpx;
}

.message-item {
	display: flex;
	align-items: flex-start;
	margin-bottom: 20rpx;
}

.message-avatar {
	width: 60rpx;
	height: 60rpx;
	border-radius: 50%;
	margin-right: 10rpx;
}

.message-content {
	max-width: 60%;
	padding: 15rpx;
	border-radius: 10rpx;
}

.message-content.me {
	background-color: #007aff;
	color: #fff;
	margin-left: auto;
}

.message-content.other {
	background-color: #fff;
	color: #333;
}

.chat-input {
	display: flex;
	align-items: center;
	padding: 20rpx;
	background-color: #fff;
	border-top: 1px solid #eee;
}

.input-container {
	display: flex;
	align-items: center;
	flex: 1;
	margin-right: 20rpx;
	border: 1px solid #eee;
	border-radius: 20rpx;
	padding: 0 20rpx;
}

.input-text {
	flex: 1;
	height: 60rpx;
	font-size: 28rpx;
}

.input-actions {
	display: flex;
	align-items: center;
}

.action-btn {
	background-color: transparent;
	border: none;
	margin-left: 20rpx;
}

.action-icon {
	font-size: 36rpx;
}

.send-btn {
	background-color: #007aff;
	color: #fff;
	border: none;
	border-radius: 20rpx;
	padding: 0 40rpx;
	height: 60rpx;
	font-size: 28rpx;
}
</style>