<template>
  <div class="tweet-list">
    <div class="compose-tweet">
      <div class="compose-avatar">
        <img src="https://via.placeholder.com/50" alt="Your Avatar">
      </div>
      <div class="compose-input">
        <textarea
          v-model="newTweetText"
          placeholder="What's happening?"
          @keyup.ctrl.enter="postTweet"
        ></textarea>
        <div class="compose-actions">
          <div class="compose-tools">
            <span class="compose-tool" title="Add media">📷</span>
            <span class="compose-tool" title="Add GIF">🎥</span>
            <span class="compose-tool" title="Add poll">📊</span>
            <span class="compose-tool" title="Add emoji">😀</span>
            <span class="compose-tool" title="Schedule tweet">📅</span>
          </div>
          <div class="compose-submit">
            <span class="character-count" :class="{ 'warning': newTweetText.length > 240, 'error': newTweetText.length > 280 }">
              {{ newTweetText.length > 0 ? 280 - newTweetText.length : '' }}
            </span>
            <button
              class="tweet-btn"
              @click="postTweet"
              :disabled="newTweetText.trim() === '' || newTweetText.length > 280"
            >Tweet</button>
          </div>
        </div>
      </div>
    </div>
    <div class="tweets-container">
      <div v-if="filteredTweets && filteredTweets.length > 0">
        <Tweet
          v-for="tweet in filteredTweets"
          :key="tweet.id"
          :tweet="tweet"
          @reply="handleReply"
          @share="handleShare"
          @open-media="handleMediaView"
        />
      </div>
      <div v-else>
        <Tweet
          v-for="tweet in tweets"
          :key="tweet.id"
          :tweet="tweet"
          @reply="handleReply"
          @share="handleShare"
          @open-media="handleMediaView"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Tweet from './Tweet.vue'

export default {
  name: 'TweetList',
  components: {
    Tweet
  },
  props: {
    filteredTweets: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      tweets: [
        {
          id: 1,
          name: 'John Doe',
          username: 'johndoe',
          avatar: 'https://via.placeholder.com/50',
          text: 'Just started learning Vue.js and it\'s amazing! The component system makes it so easy to build interactive UIs. #vuejs #javascript',
          time: '2h',
          replies: 5,
          retweets: 10,
          likes: 25,
          media: [
            'https://via.placeholder.com/600/92c952'
          ]
        },
        {
          id: 2,
          name: 'Jane Smith',
          username: 'janesmith',
          avatar: 'https://via.placeholder.com/50',
          text: 'Working on a new project with Vue.js. The component system is so intuitive! I\'m building a Twitter clone to practice my skills. Here are some screenshots of my progress so far.',
          time: '4h',
          replies: 3,
          retweets: 7,
          likes: 18,
          media: [
            'https://via.placeholder.com/600/771796',
            'https://via.placeholder.com/600/24f355'
          ]
        },
        {
          id: 3,
          name: 'Code Ninja',
          username: 'codeninja',
          avatar: 'https://via.placeholder.com/50',
          text: 'Just released a new Vue.js tutorial on my YouTube channel. Check it out! In this video, I cover components, props, and state management with Vuex. Perfect for beginners! #vuejs #tutorial',
          time: '6h',
          replies: 8,
          retweets: 15,
          likes: 42,
          media: [
            'https://via.placeholder.com/600/d32776',
            'https://via.placeholder.com/600/f66b97',
            'https://via.placeholder.com/600/56a8c2'
          ]
        },
        {
          id: 4,
          name: 'Vue Master',
          username: 'vuemaster',
          avatar: 'https://via.placeholder.com/50',
          text: 'Vue 3 composition API is a game changer for large scale applications. What do you think? I\'ve been using it for a few months now and I\'m really impressed with the flexibility it provides. The ability to extract and reuse logic is amazing!',
          time: '8h',
          replies: 12,
          retweets: 20,
          likes: 35
        },
        {
          id: 5,
          name: 'Tech Enthusiast',
          username: 'techenthusiast',
          avatar: 'https://via.placeholder.com/50',
          text: 'Just compared React, Angular, and Vue for a new project. Going with Vue for its simplicity and performance! Here\'s a comparison chart I made showing the pros and cons of each framework.',
          time: '10h',
          replies: 15,
          retweets: 25,
          likes: 50,
          media: [
            'https://via.placeholder.com/600/b0f7cc',
            'https://via.placeholder.com/600/54176f',
            'https://via.placeholder.com/600/51aa97',
            'https://via.placeholder.com/600/810b14'
          ]
        }
      ],
      newTweetText: ''
    }
  },
  methods: {
    postTweet() {
      if (this.newTweetText.trim() === '') return;

      const newTweet = {
        id: Date.now(),
        name: 'Current User',
        username: 'currentuser',
        avatar: 'https://via.placeholder.com/50',
        text: this.newTweetText,
        time: 'now',
        replies: 0,
        retweets: 0,
        likes: 0
      };

      this.tweets.unshift(newTweet);
      this.newTweetText = '';
    },
    handleReply(tweet) {
      // In a real app, this would open a reply dialog
      console.log('Reply to tweet:', tweet.id);
    },
    handleShare(tweet) {
      // In a real app, this would open a share dialog
      console.log('Share tweet:', tweet.id);
    },
    handleMediaView(data) {
      // In a real app, this would open a media viewer
      console.log('View media:', data.tweet.id, 'index:', data.index);
    }
  }
}
</script>

<style scoped>
.tweet-list {
  background-color: var(--bg-primary);
  border-left: 1px solid var(--border-color);
  border-right: 1px solid var(--border-color);
  border-radius: 16px;
  overflow: hidden;
  transition: background-color 0.3s ease, border-color 0.3s ease;
  width: 100%;
}

@media (max-width: 768px) {
  .tweet-list {
    border-radius: 0;
    border-left: none;
    border-right: none;
  }
}

@media (max-width: 480px) {
  .compose-tweet {
    padding: 10px;
  }

  .compose-avatar img {
    width: 40px;
    height: 40px;
  }

  .compose-input textarea {
    height: 60px;
    font-size: 14px;
  }

  .compose-tool {
    font-size: 16px;
    margin-right: 10px;
  }

  .tweet-btn {
    padding: 6px 12px;
    font-size: 14px;
  }
}

.compose-tweet {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.compose-avatar {
  margin-right: 10px;
}

.compose-avatar img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

.compose-input {
  flex: 1;
}

.compose-input textarea {
  width: 100%;
  height: 80px;
  border: none;
  resize: none;
  font-size: 16px;
  margin-bottom: 10px;
  padding: 10px 0;
  font-family: inherit;
  background-color: var(--bg-primary);
  color: var(--text-primary);
  transition: background-color 0.3s ease, color 0.3s ease;
}

.compose-input textarea:focus {
  outline: none;
}

.compose-actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top: 1px solid var(--border-color);
  padding-top: 10px;
  transition: border-color 0.3s ease;
}

.compose-tools {
  display: flex;
}

.compose-tool {
  color: var(--primary-color);
  margin-right: 15px;
  cursor: pointer;
  font-size: 18px;
  transition: color 0.3s ease;
}

.compose-submit {
  display: flex;
  align-items: center;
}

.character-count {
  margin-right: 10px;
  color: var(--text-secondary);
  transition: color 0.3s ease;
}

.character-count.warning {
  color: #ffad1f;
}

.character-count.error {
  color: #e0245e;
}

.tweet-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.tweet-btn:hover {
  background-color: var(--primary-color-dark);
}

.tweet-btn:disabled {
  background-color: #9bd1f9;
  cursor: not-allowed;
}

.tweets-container {
  max-height: 800px;
  overflow-y: auto;
}

.tweets-container::-webkit-scrollbar {
  width: 8px;
}

.tweets-container::-webkit-scrollbar-track {
  background: #f1f1f1;
}

.tweets-container::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 4px;
}

.tweets-container::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>
