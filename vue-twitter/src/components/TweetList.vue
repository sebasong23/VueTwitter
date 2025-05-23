<template>
  <div class="tweet-list">
    <div class="compose-tweet">
      <div class="compose-avatar">
        <img src="https://via.placeholder.com/50" alt="Your Avatar">
      </div>
      <div class="compose-input">
        <textarea placeholder="What's happening?"></textarea>
        <div class="compose-actions">
          <button class="tweet-btn">Tweet</button>
        </div>
      </div>
    </div>
    <Tweet v-for="tweet in tweets" :key="tweet.id" :tweet="tweet" />
  </div>
</template>

<script>
import Tweet from './Tweet.vue'

export default {
  name: 'TweetList',
  components: {
    Tweet
  },
  data() {
    return {
      tweets: [
        {
          id: 1,
          name: 'John Doe',
          username: 'johndoe',
          avatar: 'https://via.placeholder.com/50',
          text: 'Just started learning Vue.js and it\'s amazing! #vuejs #javascript',
          time: '2h',
          replies: 5,
          retweets: 10,
          likes: 25
        },
        {
          id: 2,
          name: 'Jane Smith',
          username: 'janesmith',
          avatar: 'https://via.placeholder.com/50',
          text: 'Working on a new project with Vue.js. The component system is so intuitive!',
          time: '4h',
          replies: 3,
          retweets: 7,
          likes: 18
        },
        {
          id: 3,
          name: 'Code Ninja',
          username: 'codeninja',
          avatar: 'https://via.placeholder.com/50',
          text: 'Just released a new Vue.js tutorial on my YouTube channel. Check it out! #vuejs #tutorial',
          time: '6h',
          replies: 8,
          retweets: 15,
          likes: 42
        },
        {
          id: 4,
          name: 'Vue Master',
          username: 'vuemaster',
          avatar: 'https://via.placeholder.com/50',
          text: 'Vue 3 composition API is a game changer for large scale applications. What do you think?',
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
          text: 'Just compared React, Angular, and Vue for a new project. Going with Vue for its simplicity and performance!',
          time: '10h',
          replies: 15,
          retweets: 25,
          likes: 50
        }
      ]
    }
  }
}
</script>

<style scoped>
.tweet-list {
  background-color: #ffffff;
  border-left: 1px solid #e1e8ed;
  border-right: 1px solid #e1e8ed;
}

.compose-tweet {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid #e1e8ed;
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
}

.compose-input textarea:focus {
  outline: none;
}

.compose-actions {
  display: flex;
  justify-content: flex-end;
}

.tweet-btn {
  background-color: #1da1f2;
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
}

.tweet-btn:hover {
  background-color: #0c8bd9;
}
</style>
