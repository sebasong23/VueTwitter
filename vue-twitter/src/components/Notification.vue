<template>
  <div class="notification" :class="{ 'unread': !notification.read }" @click="markAsRead">
    <div class="notification-avatar">
      <img :src="notification.avatar" :alt="notification.username">
    </div>
    <div class="notification-content">
      <div class="notification-header">
        <span class="notification-username">{{ notification.username }}</span>
        <span class="notification-time">{{ formatTime(notification.time) }}</span>
      </div>
      <div class="notification-text">
        <span v-html="formatNotificationText(notification)"></span>
      </div>
      <div v-if="notification.tweetPreview" class="notification-preview">
        {{ notification.tweetPreview }}
      </div>
    </div>
    <div class="notification-icon">
      <span v-if="notification.type === 'like'" class="icon-like">❤️</span>
      <span v-else-if="notification.type === 'retweet'" class="icon-retweet">🔄</span>
      <span v-else-if="notification.type === 'reply'" class="icon-reply">💬</span>
      <span v-else-if="notification.type === 'follow'" class="icon-follow">👤</span>
      <span v-else-if="notification.type === 'mention'" class="icon-mention">@</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Notification',
  props: {
    notification: {
      type: Object,
      required: true
    }
  },
  methods: {
    formatTime(timestamp) {
      // For demo purposes, we'll use a simple time formatter
      // In a real app, we would use a library like date-fns or moment.js
      const now = new Date();
      const notificationTime = new Date(timestamp);
      const diffInSeconds = Math.floor((now - notificationTime) / 1000);
      
      if (diffInSeconds < 60) {
        return `${diffInSeconds}s`;
      } else if (diffInSeconds < 3600) {
        return `${Math.floor(diffInSeconds / 60)}m`;
      } else if (diffInSeconds < 86400) {
        return `${Math.floor(diffInSeconds / 3600)}h`;
      } else if (diffInSeconds < 604800) {
        return `${Math.floor(diffInSeconds / 86400)}d`;
      } else {
        // Format as MM/DD/YYYY
        return notificationTime.toLocaleDateString();
      }
    },
    formatNotificationText(notification) {
      let text = '';
      
      switch (notification.type) {
        case 'like':
          text = `<strong>${notification.name}</strong> liked your tweet`;
          break;
        case 'retweet':
          text = `<strong>${notification.name}</strong> retweeted your tweet`;
          break;
        case 'reply':
          text = `<strong>${notification.name}</strong> replied to your tweet`;
          break;
        case 'follow':
          text = `<strong>${notification.name}</strong> followed you`;
          break;
        case 'mention':
          text = `<strong>${notification.name}</strong> mentioned you in a tweet`;
          break;
        default:
          text = notification.text || '';
      }
      
      return text;
    },
    markAsRead() {
      if (!this.notification.read) {
        this.$emit('mark-read', this.notification.id);
      }
      
      // Navigate to the relevant content based on notification type
      this.$emit('click', this.notification);
    }
  }
}
</script>

<style scoped>
.notification {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  cursor: pointer;
  transition: background-color 0.3s ease;
  position: relative;
}

.notification:hover {
  background-color: var(--hover-color);
}

.notification.unread {
  background-color: rgba(29, 161, 242, 0.1);
}

.notification.unread:hover {
  background-color: rgba(29, 161, 242, 0.15);
}

.notification-avatar {
  margin-right: 10px;
}

.notification-avatar img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

.notification-content {
  flex: 1;
}

.notification-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 5px;
}

.notification-username {
  font-weight: bold;
  color: var(--text-primary);
}

.notification-time {
  color: var(--text-secondary);
  font-size: 14px;
}

.notification-text {
  margin-bottom: 5px;
  color: var(--text-primary);
  line-height: 1.4;
}

.notification-preview {
  padding: 10px;
  border: 1px solid var(--border-color);
  border-radius: 10px;
  color: var(--text-secondary);
  font-size: 14px;
  margin-top: 5px;
  background-color: var(--bg-primary);
}

.notification-icon {
  margin-left: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 30px;
}

.icon-like, .icon-retweet, .icon-reply, .icon-follow, .icon-mention {
  font-size: 18px;
}

.icon-mention {
  font-weight: bold;
  color: var(--primary-color);
}

@media (max-width: 480px) {
  .notification {
    padding: 10px;
  }
  
  .notification-avatar img {
    width: 30px;
    height: 30px;
  }
  
  .notification-text, .notification-preview {
    font-size: 14px;
  }
  
  .notification-icon {
    width: 20px;
  }
  
  .icon-like, .icon-retweet, .icon-reply, .icon-follow, .icon-mention {
    font-size: 16px;
  }
}
</style>
