<template>
  <view class="content">
    <image class="logo" src="/static/logo.png"></image>
    <view class="text-area">
      <text class="title">Hello</text>
    </view>
    <view>这是scss 全局变量的样式</view>
    <view>这是less 全局变量的样式</view>
    <view>unocss demo</view>
    <view class="text-green">查看其它页面示例↓</view>
    <view>Pinia Demo</view>
    <view>Axios Demo</view>
    <view>uView Demo</view>
    <view>UnoCSS Demo</view>

    <!-- 添加跳转按钮 -->
    <navigator url="/pages/lucky-wheel/index" class="nav-btn">
      <button type="primary">今天吃什么?</button>
    </navigator>
  </view>
</template>

<style>
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.logo {
  height: 200rpx;
  width: 200rpx;
  margin-top: 200rpx;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 50rpx;
}

.text-area {
  display: flex;
  justify-content: center;
}

.title {
  font-size: 36rpx;
  color: #8f8f94;
}

.nav-btn {
  margin-top: 20px;
}
</style>
