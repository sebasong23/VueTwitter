<template>
  <div class="sidebar">
    <div class="search-box">
      <input type="text" placeholder="Search Twitter">
    </div>
    <div class="trends-container">
      <h2>Trends for you</h2>
      <div class="trend" v-for="trend in trends" :key="trend.id">
        <div class="trend-location">{{ trend.location }}</div>
        <div class="trend-name">{{ trend.name }}</div>
        <div class="trend-tweets">{{ trend.tweets }} Tweets</div>
      </div>
    </div>
    <div class="who-to-follow">
      <h2>Who to follow</h2>
      <div class="follow-suggestion" v-for="user in suggestedUsers" :key="user.id">
        <div class="user-avatar">
          <img :src="user.avatar" :alt="user.name">
        </div>
        <div class="user-info">
          <div class="user-name">{{ user.name }}</div>
          <div class="user-username">@{{ user.username }}</div>
        </div>
        <button class="follow-btn">Follow</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Sidebar',
  data() {
    return {
      trends: [
        { id: 1, name: '#VueJS', location: 'Technology', tweets: '25.5K' },
        { id: 2, name: '#JavaScript', location: 'Programming', tweets: '100K' },
        { id: 3, name: '#WebDevelopment', location: 'Technology', tweets: '50K' },
        { id: 4, name: '#FrontendDev', location: 'Technology', tweets: '15K' },
        { id: 5, name: '#CodeNewbie', location: 'Programming', tweets: '10K' }
      ],
      suggestedUsers: [
        { id: 1, name: 'Vue.js', username: 'vuejs', avatar: 'https://via.placeholder.com/40' },
        { id: 2, name: 'Evan You', username: 'youyuxi', avatar: 'https://via.placeholder.com/40' },
        { id: 3, name: 'Sarah Drasner', username: 'sarah_edo', avatar: 'https://via.placeholder.com/40' }
      ]
    }
  }
}
</script>

<style scoped>
.sidebar {
  padding: 0 20px;
}

.search-box {
  margin-bottom: 20px;
}

.search-box input {
  width: 100%;
  padding: 10px 15px;
  border-radius: 30px;
  border: 1px solid #e1e8ed;
  background-color: #f5f8fa;
}

.search-box input:focus {
  outline: none;
  border-color: #1da1f2;
  background-color: #ffffff;
}

.trends-container, .who-to-follow {
  background-color: #f5f8fa;
  border-radius: 15px;
  padding: 15px;
  margin-bottom: 20px;
}

h2 {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 15px;
}

.trend {
  margin-bottom: 15px;
  cursor: pointer;
}

.trend:hover {
  background-color: rgba(0, 0, 0, 0.03);
}

.trend-location, .trend-tweets {
  font-size: 13px;
  color: #657786;
}

.trend-name {
  font-weight: bold;
  margin: 3px 0;
}

.follow-suggestion {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  padding: 5px 0;
}

.user-avatar {
  margin-right: 10px;
}

.user-avatar img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.user-info {
  flex: 1;
}

.user-name {
  font-weight: bold;
}

.user-username {
  font-size: 13px;
  color: #657786;
}

.follow-btn {
  background-color: #000000;
  color: white;
  border: none;
  border-radius: 30px;
  padding: 6px 15px;
  font-weight: bold;
  cursor: pointer;
}

.follow-btn:hover {
  background-color: #333333;
}
</style>
