<template>
  <div class="message-list-container">
    <div class="message-list">
      <div
        v-for="item in messageList"
        :key="item.ID"
        :class="['message-item', `${'out' === item.flow ? 'is-me' : ''}`]"
      >
        <div class="message-header">
          {{ item.nick || item.from }}
        </div>
        <div class="message-body">
          <message-text v-if="item.type === 'TIMTextElem'" :data="item.payload.text" />
        </div>
      </div>
    </div>
    <div ref="messageBottomEl" class="message-bottom" />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, nextTick, onMounted, onUnmounted } from 'vue';
import { storeToRefs } from 'pinia';
import { ElMessage } from 'element-plus';

import TUIRoomCore from '../../tui-room-core';
import { useChatStore } from '../../stores/chat';
import MessageText from './MessageTypes/MessageText.vue';

const chatStore = useChatStore();
const { messageList } = storeToRefs(chatStore);
const messageBottomEl = ref<HTMLInputElement | null>(null);
// 为了解决自己向上滚动浏览消息, 防止别人发的消息不停向下滚消息列表
let isScrollNotAtBottom = false;

const handleMessageListScroll = (e: Event) => {
  const messageContainer = e.target as HTMLElement;
  const bottom = messageContainer.scrollHeight - messageContainer.scrollTop - messageContainer.clientHeight;
  if (bottom > 80) {
    // 30 为判断是否向上滚动浏览消息的阈值
    isScrollNotAtBottom = true;
  } else {
    isScrollNotAtBottom = false;
  }
};

watch(messageList, async (newMessageList, oldMessageList) => { // eslint-disable-line
  await nextTick();
  if (isScrollNotAtBottom) {
    if (newMessageList.length >= 1) {
      const lastMessage = newMessageList[newMessageList.length - 1];
      if ((lastMessage as any).flow === 'out') {
        // 最新一条是自己发送的
        messageBottomEl.value && messageBottomEl.value.scrollIntoView();
      }
    }
    return;
  }
  // 如果没进行滚动一直在底部, 直接展示最新消息
  messageBottomEl.value && messageBottomEl.value.scrollIntoView();
});

async function fetchChatHistoryMessage(nextReqMessageID: string, isCompleted: boolean) {
  try {
      if (isCompleted) {
        return;
      }
      const response = await TUIRoomCore.getChatMessageList(nextReqMessageID);
      if (response.code === 0 && response.data) {
        const { messageList, nextReqMessageID, isCompleted } = response.data;
        chatStore.addHistoryMessages(messageList);
        await fetchChatHistoryMessage(nextReqMessageID, isCompleted);
      }
  } catch (e) {
    ElMessage.error('获取聊天消息失败');
  }
}

onMounted(() => {
  fetchChatHistoryMessage('', false);
  window.addEventListener('scroll', handleMessageListScroll, true);
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleMessageListScroll, true);
});
</script>

<style lang="scss" scoped>
.message-list-container {
  height: calc(100% - 188px);
  ;
  padding: 10px 23px 10px 32px;
  overflow: auto;

  &::-webkit-scrollbar {
    display: none;
  }

  .message-item {
    margin-bottom: 20px;
    word-break: break-all;
    display: flex;
    flex-direction: column;
    align-items: start;
    &:last-of-type {
      margin-bottom: 0;
    }
    &.is-me {
      align-items: end;
      .message-body {
        background-color: #373D4D;
      }
    }
    .message-header {
      font-size: 14px;
      color: #7C85A6;
      margin-bottom: 10px;
    }
    .message-body {
      background-color: #1883FF;
      display: inline-block;
      padding: 7px;
      border-radius: 4px;
      font-weight: 400;
      font-size: 14px;
      color: #FFFFFF;
    }
  }
  .message-bottom {
    width: 0;
    height: 0;
  }
}
</style>
