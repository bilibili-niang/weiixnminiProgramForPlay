<template>
  <view class="lucky-wheel-container">
    <view class="wheels-container">
      <view v-for="(wheel, index) in wheels" :key="index" class="wheel-item">
        <view class="wheel-title">{{ wheelTitles[index] }}</view>
        <view
          class="wheel"
          :style="{ transform: `rotate(${wheel.rotation}deg)` }"
        >
          <view
            v-for="(item, idx) in foodList"
            :key="idx"
            class="wheel-section"
            :style="getWheelSectionStyle(idx)"
          >
            {{ item }}
          </view>
        </view>
        <button @click="startRotation(index)" :disabled="wheel.isRotating">
          {{ wheel.isRotating ? '旋转中...' : '开始' }}
        </button>
      </view>
    </view>

    <view class="results-container">
      <view class="result-title">今日推荐</view>
      <view class="result-item"
        >早餐: {{ todayResults.breakfast || '未选择' }}</view
      >
      <view class="result-item"
        >午餐: {{ todayResults.lunch || '未选择' }}</view
      >
      <view class="result-item"
        >晚餐: {{ todayResults.dinner || '未选择' }}</view
      >
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      foodList: ['面条', '米饭', '馒头', '饺子', '汉堡', '披萨', '沙拉', '粥'],
      wheels: [
        { rotation: 0, isRotating: false },
        { rotation: 0, isRotating: false },
        { rotation: 0, isRotating: false }
      ],
      wheelTitles: ['早餐', '午餐', '晚餐'],
      todayResults: {
        breakfast: '',
        lunch: '',
        dinner: ''
      }
    }
  },
  mounted() {
    // 从本地存储加载今日结果
    const savedResults = uni.getStorageSync('todayResults')
    if (savedResults) {
      this.todayResults = JSON.parse(savedResults)
    }
  },
  methods: {
    getWheelSectionStyle(index) {
      const rotate = (index * 360) / this.foodList.length
      return {
        transform: `rotate(${rotate}deg)`,
        'transform-origin': '50% 50%'
      }
    },
    startRotation(wheelIndex) {
      if (this.wheels[wheelIndex].isRotating) return

      this.wheels[wheelIndex].isRotating = true
      const randomRotations = 5 + Math.floor(Math.random() * 5) // 5-10圈
      const targetRotation = randomRotations * 360 + Math.random() * 360

      this.wheels[wheelIndex].rotation = targetRotation

      setTimeout(() => {
        this.wheels[wheelIndex].isRotating = false
        this.saveResult(wheelIndex, this.calculateResult(targetRotation))
      }, 3000)
    },
    calculateResult(rotation) {
      const normalizedRotation = rotation % 360
      const sectionSize = 360 / this.foodList.length
      const index = Math.floor(normalizedRotation / sectionSize)
      return this.foodList[index]
    },
    saveResult(wheelIndex, result) {
      const mealTypes = ['breakfast', 'lunch', 'dinner']
      this.todayResults[mealTypes[wheelIndex]] = result

      // 保存到本地存储
      uni.setStorageSync('todayResults', JSON.stringify(this.todayResults))
    }
  }
}
</script>

<style>
.lucky-wheel-container {
  padding: 20px;
}

.wheels-container {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.wheel-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
}

.wheel-title {
  font-size: 18px;
  font-weight: bold;
}

.wheel {
  width: 300px;
  height: 300px;
  border-radius: 50%;
  position: relative;
  border: 2px solid #333;
  transition: transform 3s cubic-bezier(0.25, 0.1, 0.25, 1);
}

.wheel-section {
  position: absolute;
  width: 50%;
  height: 2px;
  left: 50%;
  top: 50%;
  transform-origin: left center;
  display: flex;
  align-items: center;
  justify-content: center;
  padding-left: 60px;
}

.results-container {
  margin-top: 30px;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.result-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.result-item {
  margin: 8px 0;
}
</style>
