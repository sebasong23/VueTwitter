<template>
  <div class="tweet" :class="{ 'tweet-expanded': expanded }">
    <div class="tweet-avatar">
      <img :src="tweet.avatar" :alt="tweet.username">
    </div>
    <div class="tweet-content">
      <div class="tweet-header">
        <span class="tweet-name">{{ tweet.name }}</span>
        <span class="tweet-username">@{{ tweet.username }}</span>
        <span class="tweet-time">· {{ formatTime(tweet.time) }}</span>
        <div class="tweet-menu" @click.stop="showMenu = !showMenu">
          <span>•••</span>
          <div class="tweet-menu-dropdown" v-if="showMenu">
            <div class="menu-item">Copy link to Tweet</div>
            <div class="menu-item">Embed Tweet</div>
            <div class="menu-item">Mute @{{ tweet.username }}</div>
            <div class="menu-item">Block @{{ tweet.username }}</div>
            <div class="menu-item">Report Tweet</div>
          </div>
        </div>
      </div>
      <div class="tweet-text" @click="expandTweet">
        <p v-if="!expanded && tweet.text.length > 280">{{ tweet.text.substring(0, 280) }}... <span class="show-more">Show more</span></p>
        <p v-else v-html="formatTweetText(tweet.text)"></p>
      </div>
      <div class="tweet-media" v-if="tweet.media && tweet.media.length > 0">
        <div :class="'media-grid-' + tweet.media.length">
          <div v-for="(media, index) in tweet.media" :key="index" class="media-item">
            <img :src="media" alt="Tweet media" @click="openMediaViewer(index)">
          </div>
        </div>
      </div>
      <div class="tweet-actions">
        <div class="tweet-action" @click="replyToTweet">
          <span class="action-icon" :class="{ 'active': hasReplied }">💬</span>
          <span class="action-count">{{ formatCount(tweet.replies) }}</span>
        </div>
        <div class="tweet-action" @click="retweetTweet">
          <span class="action-icon" :class="{ 'active': hasRetweeted }">🔄</span>
          <span class="action-count">{{ formatCount(tweet.retweets) }}</span>
        </div>
        <div class="tweet-action" @click="likeTweet">
          <span class="action-icon" :class="{ 'active': hasLiked }">❤️</span>
          <span class="action-count">{{ formatCount(tweet.likes) }}</span>
        </div>
        <div class="tweet-action" @click="shareTweet">
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
  },
  data() {
    return {
      expanded: false,
      showMenu: false,
      hasLiked: false,
      hasRetweeted: false,
      hasReplied: false,
      mediaViewerOpen: false,
      currentMediaIndex: 0
    }
  },
  methods: {
    expandTweet() {
      if (this.tweet.text.length > 280) {
        this.expanded = !this.expanded;
      }
    },
    formatTweetText(text) {
      // Convert URLs to clickable links
      let formattedText = text.replace(
        /(https?:\/\/[^\s]+)/g,
        '<a href="$1" target="_blank" rel="noopener">$1</a>'
      );

      // Convert hashtags to clickable links
      formattedText = formattedText.replace(
        /#(\w+)/g,
        '<a href="#/hashtag/$1" class="hashtag">#$1</a>'
      );

      // Convert mentions to clickable links
      formattedText = formattedText.replace(
        /@(\w+)/g,
        '<a href="#/user/$1" class="mention">@$1</a>'
      );

      return formattedText;
    },
    formatTime(time) {
      // For demo purposes, just return the time string
      // In a real app, we would format relative to current time
      return time;
    },
    formatCount(count) {
      // Format large numbers (e.g., 1.5K, 2.3M)
      if (count >= 1000000) {
        return (count / 1000000).toFixed(1) + 'M';
      } else if (count >= 1000) {
        return (count / 1000).toFixed(1) + 'K';
      }
      return count;
    },
    likeTweet() {
      this.hasLiked = !this.hasLiked;
      if (this.hasLiked) {
        this.tweet.likes++;
      } else {
        this.tweet.likes--;
      }
    },
    retweetTweet() {
      this.hasRetweeted = !this.hasRetweeted;
      if (this.hasRetweeted) {
        this.tweet.retweets++;
      } else {
        this.tweet.retweets--;
      }
    },
    replyToTweet() {
      // In a real app, this would open a reply dialog
      this.$emit('reply', this.tweet);
    },
    shareTweet() {
      // In a real app, this would open a share dialog
      if (navigator.share) {
        navigator.share({
          title: `Tweet by ${this.tweet.name}`,
          text: this.tweet.text,
          url: `https://twitter.com/${this.tweet.username}/status/${this.tweet.id}`
        });
      } else {
        // Fallback for browsers that don't support Web Share API
        this.$emit('share', this.tweet);
      }
    },
    openMediaViewer(index) {
      this.currentMediaIndex = index;
      this.mediaViewerOpen = true;
      this.$emit('open-media', { tweet: this.tweet, index });
    }
  }
}
</script>

<style scoped>
.tweet {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.tweet:hover {
  background-color: var(--hover-color);
}

.tweet-expanded {
  background-color: var(--bg-primary);
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
  display: flex;
  align-items: center;
  margin-bottom: 5px;
  position: relative;
}

.tweet-name {
  font-weight: bold;
  margin-right: 5px;
}

.tweet-username, .tweet-time {
  color: var(--text-secondary);
  margin-right: 5px;
  transition: color 0.3s ease;
}

.tweet-menu {
  margin-left: auto;
  color: var(--text-secondary);
  cursor: pointer;
  position: relative;
  transition: color 0.3s ease;
}

.tweet-menu-dropdown {
  position: absolute;
  right: 0;
  top: 20px;
  background-color: var(--bg-primary);
  border-radius: 4px;
  box-shadow: 0 1px 4px var(--shadow-color);
  width: 200px;
  z-index: 10;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.menu-item {
  padding: 12px 16px;
  font-size: 14px;
  cursor: pointer;
}

.menu-item:hover {
  background-color: var(--hover-color);
}

.tweet-text {
  margin-bottom: 10px;
  line-height: 1.4;
  word-wrap: break-word;
}

.tweet-text a {
  color: var(--primary-color);
  text-decoration: none;
  transition: color 0.3s ease;
}

.tweet-text a:hover {
  text-decoration: underline;
}

.show-more {
  color: var(--primary-color);
  cursor: pointer;
  transition: color 0.3s ease;
}

.tweet-media {
  margin-bottom: 10px;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.media-grid-1 .media-item {
  width: 100%;
}

.media-grid-2, .media-grid-3, .media-grid-4 {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 2px;
}

.media-grid-3 .media-item:first-child {
  grid-column: span 2;
}

.media-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  cursor: pointer;
}

.tweet-actions {
  display: flex;
  justify-content: space-between;
  max-width: 425px;
  margin-top: 10px;
}

@media (max-width: 480px) {
  .tweet {
    padding: 10px;
  }

  .tweet-avatar img {
    width: 40px;
    height: 40px;
  }

  .tweet-text {
    font-size: 14px;
  }

  .tweet-actions {
    max-width: 100%;
  }

  .action-count {
    display: none;
  }
}

.tweet-action {
  display: flex;
  align-items: center;
  color: var(--text-secondary);
  cursor: pointer;
  transition: color 0.3s ease;
}

.action-icon {
  margin-right: 5px;
}

.tweet-action:hover {
  color: var(--primary-color);
}

.tweet-action:nth-child(1):hover {
  color: var(--primary-color);
}

.tweet-action:nth-child(2):hover {
  color: #17bf63;
}

.tweet-action:nth-child(3):hover {
  color: #e0245e;
}

.action-icon.active {
  color: #e0245e;
}
</style>
