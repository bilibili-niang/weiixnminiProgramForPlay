<template>
  <view class="lucky-wheel-container">
    <view class="wheel-wrapper">
      <view class="wheel-title">今天吃什么?</view>

      <view class="wheel-container">
        <!-- 静态转盘 -->
        <view class="wheel" :class="{ 'rotating': isRotating }" :style="{ transform: `rotate(${wheelRotation}deg)` }">
          <view v-for="(item, idx) in foodList" :key="idx" class="wheel-section" :style="getSectionStyle(idx)">
            <text class="wheel-text" :style="getTextStyle(idx, wheelRotation)">{{ item }}</text>
          </view>

          <!-- 中心按钮 -->
          <view class="center-button" @click="startRotation" :class="{ 'rotating': isRotating }">
            {{ isRotating ? '转动中...' : 'GO!' }}
          </view>
        </view>

        <!-- 指针 -->
        <view class="pointer">
          <view class="pointer-head"></view>
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
import { ref, nextTick } from 'vue'

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

// 计算扇区样式
const getSectionStyle = (index: number) => {
  const rotate = (index * 360) / foodList.length
  return {
    transform: `rotate(${rotate}deg)`,
    background: index % 2 === 0 ? '#FF9999' : '#99FF99'
  }
}

// 计算文字样式
const getTextStyle = (index: number, wheelRotation: number) => {
  const rotate = (index * 360) / foodList.length
  return {
    transform: `rotate(${-rotate - wheelRotation + 90}deg)`
  }
}

// 开始旋转
const startRotation = () => {
  if (isRotating.value) return

  isRotating.value = true
  currentResult.value = ''

  // 重置当前角度
  wheelRotation.value = 0
  
  // 使用 nextTick 确保角度重置已生效
  nextTick(() => {
    const randomRotations = 5 + Math.floor(Math.random() * 5) // 5-10圈
    const extraDegrees = Math.random() * 360
    const totalRotation = randomRotations * 360 + extraDegrees

    // 设置新的旋转角度
    wheelRotation.value = totalRotation

    setTimeout(() => {
      isRotating.value = false
      const finalPosition = totalRotation % 360
      const sectionSize = 360 / foodList.length
      const selectedIndex = foodList.length - Math.floor(finalPosition / sectionSize) - 1
      currentResult.value = foodList[selectedIndex]
    }, 3000)
  })
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

.wheel {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  position: relative;
  border: 8px solid #2c3e50;
  overflow: hidden;
  transition: transform 3s cubic-bezier(0.25, 0.1, 0.25, 1);
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
  left: 70%;
  top: 30%;
  transform-origin: left center;
  font-size: 14px;
  color: #333;
  font-weight: bold;
  white-space: nowrap;
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

/* 指针样式 */
.pointer {
  position: absolute;
  top: -5px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 20;
  transition: transform 0.1s ease;
}

.pointer-head {
  width: 20px;
  height: 30px;
  background: #e74c3c;
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
  animation: pointerShake 0.3s ease infinite;
}

@keyframes pointerShake {
  0% { transform: translateY(0); }
  50% { transform: translateY(2px); }
  100% { transform: translateY(0); }
}

/* 转动时的指针动画 */
.wheel.rotating + .pointer {
  animation: pointerVibrate 0.1s ease-in-out infinite;
}

@keyframes pointerVibrate {
  0% { transform: translateX(-50%) rotate(-2deg); }
  50% { transform: translateX(-50%) rotate(2deg); }
  100% { transform: translateX(-50%) rotate(-2deg); }
}

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
