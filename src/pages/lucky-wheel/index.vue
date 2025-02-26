<template>
  <view class="lucky-wheel-container">
    <view class="wheel-wrapper">
      <view class="wheel-title">今天吃什么?</view>

      <view class="wheel-container">
        <!-- 静态转盘 -->
        <view 
          class="wheel" 
          :style="{ 
            transform: `rotate(${wheelRotation}deg)`,
          }"
        >
          <view v-for="(item, idx) in foodList" :key="idx" class="wheel-section" :style="getSectionStyle(idx)">
            <text class="wheel-text" :style="getTextStyle(idx, wheelRotation)">{{ item }}</text>
          </view>

          <!-- 中心按钮 -->
          <view class="center-button" @click="startRotation" :class="{ 'rotating': isRotating }">
            {{ isRotating ? '转动中...' : 'GO!' }}
          </view>
        </view>
      </view>

      <!-- 结果容器 -->
      <view class="result-container" :class="{ 'show': currentResult }">
        <view class="result-title">建议吃：</view>
        <view class="result-text">{{ currentResult || '等待选择' }}</view>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const foodList = [
  '火锅',
  '烤肉',
  '面条',
  '米饭',
  '饺子',
  '汉堡',
  '披萨',
  '炒菜',
  '寿司',
  '麻辣烫',
  '盖浇饭',
  '煲仔饭'
]

const isRotating = ref(false)
const currentResult = ref('')
const wheelRotation = ref(0)

// 震动函数
const vibrate = () => {
  uni.vibrateShort({
    success: function () {
      console.log('震动成功');
    }
  });
}

// 开始旋转
const startRotation = () => {
  if (isRotating.value) return

  isRotating.value = true
  currentResult.value = ''

  const randomRotations = 5 + Math.floor(Math.random() * 5) // 5-10圈
  const extraDegrees = Math.random() * 360
  const totalRotation = randomRotations * 360 + extraDegrees + 360 // 额外加一圈确保始终是正向旋转

  wheelRotation.value = totalRotation

  // 设置震动间隔
  let vibrateInterval = setInterval(() => {
    if (!isRotating.value) {
      clearInterval(vibrateInterval)
      return
    }
    vibrate()
  }, 200)

  setTimeout(() => {
    isRotating.value = false
    clearInterval(vibrateInterval)
    vibrate()
    const finalPosition = totalRotation % 360
    const sectionSize = 360 / foodList.length
    const selectedIndex = Math.floor((360 - (finalPosition % 360)) / sectionSize)
    currentResult.value = foodList[selectedIndex]
  }, 3000)
}

// 计算扇区样式
const getSectionStyle = (index: number) => {
  const rotate = (index * 360) / foodList.length
  return {
    transform: `rotate(${rotate}deg)`,
    background: index % 2 === 0 ? '#FF9999' : '#99FF99'
  }
}

// 计算文字样式
const getTextStyle = (index: number, currentRotation: number) => {
  const baseRotation = (index * 360) / foodList.length
  return {
    transform: `rotate(${-baseRotation - currentRotation}deg)`,
    transition: 'transform 3s cubic-bezier(0.25, 0.1, 0.25, 1)'
  }
}
</script>

<style scoped>
.lucky-wheel-container {
  padding: 10px;
  /* 减小顶部间距 */
  min-height: 100vh;
  background: #f5f5f5;
  display: flex;
  align-items: flex-start;
  /* 改为顶部对齐 */
  justify-content: center;
}

.wheel-wrapper {
  max-width: 600px;
  width: 100%;
  padding: 20px;
  background: white;
  border-radius: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  margin-top: 20px;
  /* 添加适当的顶部间距 */
}

.wheel-title {
  font-size: 24px;
  font-weight: bold;
  text-align: center;
  color: #2c3e50;
  margin-bottom: 20px;
  /* 减小标题下方间距 */
}

.wheel-container {
  position: relative;
  width: 300px;
  height: 300px;
  margin: 0 auto;
}

.wheel-section {
  position: absolute;
  width: 50%;
  height: 50%;
  left: 50%;
  top: 50%;
  transform-origin: 0% 0%;
}

.wheel-text {
  position: absolute;
  left: 60%;
  top: 25%;
  font-size: 14px;
  color: #333;
  font-weight: bold;
  transform-origin: center;
  white-space: nowrap;
  transition: transform 3s cubic-bezier(0.25, 0.1, 0.25, 1);
}

.wheel {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  position: relative;
  border: 8px solid #2c3e50;
  overflow: hidden;
  transition: transform 3s cubic-bezier(0.25, 0.1, 0.25, 1);
}

/* 中心按钮样式 */
.center-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 70px;
  height: 70px;
  background: #e74c3c;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 22px;
  z-index: 10;
  border: 3px solid white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

.center-button:active {
  transform: translate(-50%, -50%) scale(0.95);
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.2);
}

.center-button.rotating {
  background: #95a5a6;
  cursor: not-allowed;
  font-size: 14px;
}

/* 结果容器 */
.result-container {
  margin-top: 20px;
  /* 减小上边距 */
  text-align: center;
  padding: 15px;
  background: #f8f9fa;
  border-radius: 12px;
  opacity: 0;
  transform: translateY(-10px);
  transition: all 0.3s ease;
  height: 90px;
  /* 固定高度 */
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.result-container.show {
  opacity: 1;
  transform: translateY(0);
}

.result-title {
  font-size: 16px;
  /* 稍微减小字号 */
  color: #2c3e50;
  margin-bottom: 8px;
}

.result-text {
  font-size: 22px;
  /* 稍微减小字号 */
  font-weight: bold;
  color: #e74c3c;
}
</style>
