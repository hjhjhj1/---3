<template>
  <button 
    class="verification-code-button"
    :disabled="isCountingDown"
    @click="handleClick"
  >
    {{ buttonText }}
  </button>
</template>

<script>
export default {
  name: 'VerificationCodeButton',
  props: {
    onResend: {
      type: Function,
      required: true
    },
    countdown: {
      type: Number,
      default: 60
    },
    text: {
      type: Object,
      default: () => ({
        default: '发送验证码',
        active: '秒后重试'
      })
    }
  },
  data() {
    return {
      remainingTime: 0,
      isCountingDown: false,
      timerId: null
    };
  },
  computed: {
    buttonText() {
      if (this.isCountingDown) {
        return `${this.remainingTime}${this.text.active}`;
      } else {
        return this.text.default;
      }
    }
  },
  mounted() {
    // 页面刷新后恢复倒计时
    const storedTime = sessionStorage.getItem('verificationCodeCountdown');
    if (storedTime) {
      const remaining = Math.max(0, parseInt(storedTime) - Date.now());
      if (remaining > 0) {
        this.remainingTime = Math.ceil(remaining / 1000);
        this.startCountdown();
      }
    }
  },
  beforeDestroy() {
    this.clearTimer();
  },
  methods: {
    handleClick() {
      if (this.isCountingDown) return;
      
      // 触发发送逻辑
      this.onResend();
      
      // 开始倒计时
      this.remainingTime = this.countdown;
      this.startCountdown();
    },
    startCountdown() {
      this.isCountingDown = true;
      
      // 存储结束时间到sessionStorage
      const endTime = Date.now() + this.remainingTime * 1000;
      sessionStorage.setItem('verificationCodeCountdown', endTime);
      
      // 每秒更新剩余时间
      this.timerId = setInterval(() => {
        this.remainingTime--;
        
        if (this.remainingTime <= 0) {
          this.clearTimer();
        }
      }, 1000);
    },
    clearTimer() {
      if (this.timerId) {
        clearInterval(this.timerId);
        this.timerId = null;
      }
      
      this.isCountingDown = false;
      this.remainingTime = 0;
      
      // 清除sessionStorage
      sessionStorage.removeItem('verificationCodeCountdown');
    }
  }
};
</script>

<style scoped>
.verification-code-button {
  width: 160px;
  height: 44px;
  font-size: 16px;
  border: none;
  border-radius: 8px;
  background-color: #007aff;
  color: white;
  cursor: pointer;
  margin: 0 auto;
  display: block;
  transition: background-color 0.3s, color 0.3s;
}

.verification-code-button:disabled {
  background-color: #cccccc;
  color: #999999;
  cursor: not-allowed;
}

/* 适配不同屏幕尺寸 */
@media (max-width: 768px) {
  .verification-code-button {
    width: 140px;
    height: 44px;
    font-size: 16px;
  }
}
</style>