<template>
  <div class="tweet">
    <div class="tweet-avatar">
      <img :src="tweet.avatar" :alt="tweet.username">
    </div>
    <div class="tweet-content">
      <div class="tweet-header">
        <span class="tweet-name">{{ tweet.name }}</span>
        <span class="tweet-username">@{{ tweet.username }}</span>
        <span class="tweet-time">· {{ tweet.time }}</span>
      </div>
      <div class="tweet-text">
        {{ tweet.text }}
      </div>
      <div class="tweet-actions">
        <div class="tweet-action">
          <span class="action-icon">💬</span>
          <span class="action-count">{{ tweet.replies }}</span>
        </div>
        <div class="tweet-action">
          <span class="action-icon">🔄</span>
          <span class="action-count">{{ tweet.retweets }}</span>
        </div>
        <div class="tweet-action">
          <span class="action-icon">❤️</span>
          <span class="action-count">{{ tweet.likes }}</span>
        </div>
        <div class="tweet-action">
          <span class="action-icon">📤</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Tweet',
  props: {
    tweet: {
      type: Object,
      required: true
    }
  }
}
</script>

<style scoped>
.tweet {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid #e1e8ed;
}

.tweet-avatar {
  margin-right: 10px;
}

.tweet-avatar img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

.tweet-content {
  flex: 1;
}

.tweet-header {
  margin-bottom: 5px;
}

.tweet-name {
  font-weight: bold;
  margin-right: 5px;
}

.tweet-username, .tweet-time {
  color: #657786;
}

.tweet-text {
  margin-bottom: 10px;
  line-height: 1.4;
}

.tweet-actions {
  display: flex;
  justify-content: space-between;
  max-width: 425px;
}

.tweet-action {
  display: flex;
  align-items: center;
  color: #657786;
  cursor: pointer;
}

.action-icon {
  margin-right: 5px;
}

.tweet-action:hover {
  color: #1da1f2;
}
</style>
