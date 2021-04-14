<template>
  <view class="content">
    <view class="todo-header" v-if="list.length > 0">
      <!-- 状态栏的左侧 -->
      <view class="todo-header__left">
        <text class="active-text" v-if="activeIndex === 0">全部</text>
        <text class="active-text" v-if="activeIndex === 1">代办</text>
        <text class="active-text" v-if="activeIndex === 2">已完成</text>
        <text>{{ listData.length }}条</text>
      </view>
      <!-- 状态栏的右侧 -->
      <view class="todo-header__right">
        <view
          class="todo-header__right-item"
          :class="{ 'active-tab': activeIndex === 0 }"
          @click="activeIndex = 0"
          >全部</view
        >
        <view
          class="todo-header__right-item"
          :class="{ 'active-tab': activeIndex === 1 }"
          @click="activeIndex = 1"
          >代办</view
        >
        <view
          class="todo-header__right-item"
          :class="{ 'active-tab': activeIndex === 2 }"
          @click="activeIndex = 2"
          >已完成</view
        >
      </view>
    </view>
    <!-- 没有数据 -->
    <view v-if="list.length === 0" class="default">
      <view class="image-default">
        <image src="../../static/default.png" mode="aspectFit"></image>
      </view>
      <view class="default-info">
        <view class="default-info__text"> 您还没有创建任何代办事项 </view>
        <view class="default-info__text"> 点击下方+号创建一个吧 </view>
      </view>
    </view>
    <view v-else class="todo-content">
      <view
        :class="['todo-list', { 'todo--finish': item.status }]"
        v-for="(item, idx) in listData"
        :key="idx"
        @click="finish(item.id)"
      >
        <view class="todo-list__checkbox"><view class="checkbox"></view></view>
        <view class="todo-list__content">{{ item.content }}</view>
      </view>
    </view>
    <!-- 创建按钮 -->
    <view class="create-todo" @click="create">
      <text
        class="iconfont icon-jia"
        :class="{ 'create-todo-active': textShow }"
      ></text>
    </view>
    <!-- 输入框 -->
    <view
      class="create-content"
      v-if="active"
      :class="{ 'create--show': textShow }"
    >
      <view class="create-content-box">
        <view class="create-input">
          <input
            type="text"
            v-model="value"
            placeholder="请输入您要创建的todo"
          />
        </view>
        <!-- 发布按钮 -->
        <view class="create-button" @click="add"> 创建 </view>
      </view>
    </view>
  </view>
</template>
<script>
export default {
  data() {
    return {
      activeIndex: 0,
      list: [],
      active: false,
      value: "",
      textShow: false,
    };
  },
  onLoad() {},
  computed: {
    listData() {
      // 点击 全部
      if (this.activeIndex === 0) {
        return this.list;
      }
      // 点击 代办事项
      if (this.activeIndex === 1) {
        return this.list.filter((item) => !item.status);
      }
      // 点击 已完成
      if (this.activeIndex === 2) {
        return this.list.filter((item) => item.status);
      }
    },
  },
  methods: {
    // 打开输入框
    create() {
      this[this.active ? "createClose" : "createOpen"]();
    },
    createOpen() {
      this.active = true;
      //$nextTick 延迟执行函数
      this.$nextTick(function () {
        setTimeout(() => {
          this.textShow = true;
        }, 50);
      });
    },
    createClose() {
      this.textShow = false;
      //$nextTick 延迟执行函数
      this.$nextTick(function () {
        setTimeout(() => {
          this.active = false;
        }, 350);
      });
    },
    // 添加todo
    add() {
      if (this.value === "") {
        uni.showToast({
          title: "请输入内容",
          icon: "none",
        });
        return;
      }
      this.list.unshift({
        id: "id" + Date.now(),
        content: this.value,
        time: Date.now(),
        status: false,
      });
      this.value = "";
      this.createClose();
    },
    // 点击列表触发
    finish(id) {
      let index = this.list.findIndex((item) => item.id === id);
      this.list[index].status = !this.list[index].status;
    },
  },
};
</script>

<style>
@import "../../common/icon.css";
page {
  background: white;
}
.todo-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: #333333;
  height: 45px;
  box-shadow: -1px 1px 5px 0 rgba(0, 0, 0, 0.1);
  background-color: white;
  padding: 0 15px;
  position: fixed;
  width: 100%;
  box-sizing: border-box;
  z-index: 666;
}
.active-text {
  color: #279abf;
  font-size: 14px;
  padding-right: 10px;
}
.todo-header__right {
  display: flex;
}
.todo-header__right-item {
  padding: 0 5px;
  font-size: 14px;
}
.active-tab {
  color: #279abf;
}
.todo-content {
  position: relative;
  padding-top: 50px;
  padding-bottom: 100px;
}
.todo-list {
  position: relative;
  display: flex;
  align-items: center;
  padding: 15px;
  margin: 15px;
  color: #0c3854;
  font-size: 14px;
  border-radius: 10px;
  background-color: #cfebfd;
  box-shadow: -1px 1px 5px 1px rgba(0, 0, 0, 0.1),
    -1px 2px 1px 0 rgba(255, 255, 255) inset;
  overflow: hidden;
  transition: all 0.3m;
}
.todo-list::after {
  position: absolute;
  content: "";
  top: 0;
  bottom: 0;
  left: 0;
  width: 5px;
  background-color: #91d1e8;
  transition: all 0.3m;
}
.todo-list::before {
  content: "";
  position: absolute;
  top: 0;
  right: 10px;
  bottom: 0;
  left: 40px;
  height: 2px;
  margin: auto 0;
  background-color: #bdcdd8;
  transition: all 0.3m;
  opacity: 0;
}
.todo-list__checkbox {
  padding-right: 15px;
}
.checkbox {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background-color: white;
  box-shadow: 0 0 5px 1px rgba(0, 0, 0, 0.1);
  transition: all 0.3m;
}
.todo--finish .checkbox {
  background-color: #eeeeee;
  position: relative;
}
.checkbox::after {
  content: "";
  position: absolute;
  width: 10px;
  height: 10px;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
  border-radius: 50%;
  background-color: #cfebfd;
  box-shadow: 0 0 2px 0 rgba(0, 0, 0, 0.2) inset;
  opacity: 0;
  transition: all 0.3m;
}
.todo--finish .checkbox::after {
  opacity: 1;
}
.todo-list__content {
  transition: all 0.3m;
}
.todo--finish .todo-list__content {
  color: #999999;
}
.todo--finish.todo-list::after {
  background-color: #cccccc;
}
.todo--finish.todo-list::before {
  opacity: 1;
}
.create-todo {
  position: fixed;
  right: 0;
  bottom: 20px;
  left: 0;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #deeff5;
  box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1),
    -1px 1px 1px 0 rgb(255, 255, 255) inset;
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: center;
}
.create-todo .icon-jia {
  font-size: 30px;
  color: #add8e6;
  transition: transform 0.3s;
}
.create-content {
  position: fixed;
  right: 20px;
  bottom: 95px;
  left: 20px;
  transition: all 0.3s;
  opacity: 0;
  transform: scale(0) translateY(200%);
}
.create-content-box {
  position: relative;
  height: 100%;
  z-index: 6;
  display: flex;
  align-items: center;
  padding: 0 0 0 15px;
  border-radius: 50px;
  background-color: #deeff5;
  box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1),
    -1px 1px 1px 0 rgb(255, 255, 255) inset;
}
.create-input {
  width: 100%;
  height: 100%;
  padding-right: 15px;
  color: #62bedc;
}
.create-input input {
  width: 100%;
  height: 100%;
  background-color: transparent;
}
.create-button {
  flex-shrink: 0;
  width: 80px;
  height: 50px;
  border-radius: 50px;
  font-size: 16px;
  color: #62bedc;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: -2px 0 2px 1px rgba(0, 0, 0, 0.1);
}
.create-content::after {
  content: "";
  position: absolute;
  right: 0;
  bottom: -8px;
  left: 0;
  /*
 三角形
	width: 0px;
	height: 0;
	border-top:12px solid #deeff5;
	border-right:12px solid transparent;
	border-left:12px solid transparent; */
  width: 20px;
  height: 20px;
  background-color: #deeff5;
  transform: rotate(45deg);
  margin: 0 auto;
  box-shadow: 1px 1px 5px 2px rgba(0, 0, 0, 0.1);
  z-index: 1;
}
.create-content-box::after {
  content: "";
  position: absolute;
  right: 0;
  bottom: -8px;
  left: 0;
  width: 20px;
  height: 20px;
  background-color: #deeff5;
  transform: rotate(45deg);
  margin: 0 auto;
}
.default {
  padding-top: 100px;
}
.image-default {
  display: flex;
  justify-content: center;
}
.image-default image {
  width: 100%;
}
.default-info {
  text-align: center;
  font-size: 14px;
  color: #cccccc;
}
.create-todo-active {
  transform: rotate(135deg);
}
.create--show {
  opacity: 1;
  transform: scale(1) translateY(0);
}
</style>