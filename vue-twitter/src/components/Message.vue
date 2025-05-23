<template>
  <div class="message" :class="{ 'outgoing': isOutgoing }">
    <div v-if="!isOutgoing" class="message-avatar">
      <img :src="message.senderAvatar" :alt="message.senderName">
    </div>
    <div class="message-content">
      <div class="message-bubble">
        <div v-if="message.text" class="message-text">{{ message.text }}</div>
        <div v-if="message.image" class="message-image">
          <img :src="message.image" alt="Message image" @click="openImage">
        </div>
      </div>
      <div class="message-time">{{ formatTime(message.timestamp) }}</div>
    </div>
    <div v-if="isOutgoing" class="message-status">
      <span v-if="message.status === 'sent'" class="status-sent">✓</span>
      <span v-else-if="message.status === 'delivered'" class="status-delivered">✓✓</span>
      <span v-else-if="message.status === 'read'" class="status-read">✓✓</span>
      <span v-else-if="message.status === 'failed'" class="status-failed">!</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
    message: {
      type: Object,
      required: true
    },
    currentUserId: {
      type: [Number, String],
      required: true
    }
  },
  computed: {
    isOutgoing() {
      return this.message.senderId === this.currentUserId;
    }
  },
  methods: {
    formatTime(timestamp) {
      const date = new Date(timestamp);
      const hours = date.getHours();
      const minutes = date.getMinutes();
      return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}`;
    },
    openImage() {
      // In a real app, this would open a full-screen image viewer
      window.open(this.message.image, '_blank');
    }
  }
}
</script>

<style scoped>
.message {
  display: flex;
  margin-bottom: 15px;
  position: relative;
}

.message-avatar {
  margin-right: 10px;
  align-self: flex-end;
}

.message-avatar img {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  object-fit: cover;
}

.message-content {
  max-width: 70%;
}

.message-bubble {
  padding: 10px;
  border-radius: 18px;
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  position: relative;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.message.outgoing .message-bubble {
  background-color: var(--primary-color);
  color: white;
}

.message-text {
  word-wrap: break-word;
  white-space: pre-wrap;
  line-height: 1.4;
}

.message-image {
  margin-top: 5px;
}

.message-image img {
  max-width: 100%;
  max-height: 300px;
  border-radius: 12px;
  cursor: pointer;
}

.message-time {
  font-size: 12px;
  color: var(--text-secondary);
  margin-top: 5px;
  text-align: right;
}

.message.outgoing {
  flex-direction: row-reverse;
}

.message.outgoing .message-content {
  margin-right: 10px;
}

.message-status {
  font-size: 12px;
  margin-left: 5px;
  align-self: flex-end;
  margin-bottom: 5px;
}

.status-sent, .status-delivered {
  color: var(--text-secondary);
}

.status-read {
  color: var(--primary-color);
}

.status-failed {
  color: #e0245e;
}

@media (max-width: 480px) {
  .message-content {
    max-width: 80%;
  }
  
  .message-bubble {
    padding: 8px;
  }
  
  .message-text {
    font-size: 14px;
  }
  
  .message-time {
    font-size: 10px;
  }
}
</style>
