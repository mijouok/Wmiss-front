<template>
  <div class="chat-container">
    <!-- 消息列表 -->
    <div class="message-list" ref="messageList">
      <div
          v-for="(message, index) in messages"
          :key="index"
          :class="['message', message.sender]"
      >
        <div class="message-content">
          {{ message.content }}
        </div>
        <div class="message-time">
          {{ formatTime(message.timestamp) }}
        </div>
      </div>
    </div>

    <!-- 输入区域 -->
    <div class="input-area">
      <input
          v-model="inputMessage"
          @keyup.enter="sendMessage"
          placeholder="输入你的问题..."
      />
      <button @click="sendMessage">发送</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, nextTick } from 'vue'

// 消息数据
const messages = reactive([
  {
    sender: 'bot',
    content: '你好！我是智能助手，有什么可以帮您？',
    timestamp: new Date()
  }
])

// 输入消息
const inputMessage = ref('')
const messageList = ref(null)

// 发送消息
const sendMessage = async () => {
  if (!inputMessage.value.trim()) return

  // 添加用户消息
  messages.push({
    sender: 'user',
    content: inputMessage.value.trim(),
    timestamp: new Date()
  })

  // 模拟回复
  console.log(inputMessage.value)
  const botResponse = await getBotResponse(inputMessage.value)
  messages.push({
    sender: 'bot',
    content: botResponse,
    timestamp: new Date()
  })

  // 清空输入
  inputMessage.value = ''

  // 滚动到底部
  nextTick(() => {
    messageList.value.scrollTop = messageList.value.scrollHeight
  })
}

///api/v1/deepSeek/retrieveAnswer
const getBotResponse = async (message) => {
  let apiUrl = "/api/v1/deepSeek/retrieveAnswer";
  apiUrl += "?userMessage=" + message;
  const response = await fetch(apiUrl, {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json'
    },
  });
  console.log("fetch resp:",response.json)
  const responseData = await response.json();
  return responseData.data;
}

// 时间格式化
const formatTime = (date) => {
  return date.toLocaleTimeString('zh-CN', {
    hour: '2-digit',
    minute: '2-digit'
  })
}
</script>

<style scoped>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  max-width: 800px;
  margin: 0 auto;
  background-color: #f5f5f5;
}

.message-list {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
}

.message {
  margin: 10px 0;
  display: flex;
  flex-direction: column;
}

.message.user {
  align-items: flex-end;
}

.message.bot {
  align-items: flex-start;
}

.message-content {
  max-width: 70%;
  padding: 12px 16px;
  border-radius: 12px;
  margin: 4px 0;
}

.message.user .message-content {
  background-color: #1890ff;
  color: white;
}

.message.bot .message-content {
  background-color: white;
  border: 1px solid #ddd;
}

.message-time {
  font-size: 12px;
  color: #666;
  margin: 4px 8px;
}

.input-area {
  display: flex;
  padding: 20px;
  background-color: white;
  box-shadow: 0 -2px 10px rgba(0,0,0,0.05);
}

.input-area input {
  flex: 1;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-right: 10px;
}

.input-area button {
  padding: 12px 24px;
  background-color: #1890ff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.input-area button:hover {
  background-color: #40a9ff;
}
</style>
