<template>
  <div class="more-container">
    <div class="more-notice">如果有任何问题欢迎加入我们的QQ群或者发送邮件</div>
    <div class="more-content">
      <div class="more-item">
        <span class="more-title">QQ群</span>
        <div class="input-area">
          <input class="input" type="text" :value="groupNumber">
          <svg-icon icon-name="copy-icon" class="copy" @click="onCopy(groupNumber)"></svg-icon>
        </div>
      </div>
      <div class="more-item">
        <span class="more-title">邮箱地址</span>
        <div class="input-area">
          <input class="input" type="text" :value="email">
          <svg-icon icon-name="copy-icon" class="copy" @click="onCopy(email)"></svg-icon>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { useBasicStore } from '../../stores/basic';
import { ElMessage } from 'element-plus';

const basicStore = useBasicStore();

const groupNumber = '592465424';
const email = 'tylerding@tencent.com';

function onCopy(value: string | number) {
  navigator.clipboard.writeText(`${value}`);
  ElMessage({
    message: '复制成功',
    type: 'success',
  });
}
</script>

<style lang="scss" scoped>
.more-container{
  padding: 20px 32px;
}
.more-notice{
  font-size: 14px;
  width: 100%;
  height: 22px;
  line-height: 22px;
  font-weight: 400;
  color: #7C85A6;
  font-family: PingFangSC-Regular;
}
.more-content{
  width: 100%;
  margin-top: 20px;
  .more-item {
    &:not(:first-child) {
      margin-top: 20px;
    }
    .more-title {
      font-size: 14px;
      color: #CFD4E6;
      width: 100%;
    }
    .input-area {
      margin-top: 10px;
      position: relative;
      .input{
        -webkit-appearance: none;
          background-color: #2E323D;
          background-image: none;
          border-radius: 2px;
          border: 1px solid #2E323D;
          box-sizing: border-box;
          color: #7C85A6;
          display: inline-block;
          font-size: 14px;
          height: 32px;
          line-height: 32px;
          outline: none;
          padding: 0 40px 0 10px;
          transition: border-color .2s cubic-bezier(.645,.045,.355,1);
          width: 416px;
      }
      .copy {
        width: 14px;
        height: 14px;
        position: absolute;
        top: 50%;
        right: 10px;
        transform: translateY(-50%);
        cursor: pointer;
      }
    }
  }
}
</style>
