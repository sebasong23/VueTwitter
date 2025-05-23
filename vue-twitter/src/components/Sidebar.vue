<template>
  <div class="sidebar">
    <div class="search-box">
      <div class="search-input-container">
        <span class="search-icon">🔍</span>
        <input
          type="text"
          v-model="searchQuery"
          placeholder="Search Twitter"
          @focus="showSearchResults = true"
          @keyup.enter="performSearch"
        >
        <span
          v-if="searchQuery"
          class="clear-search"
          @click="clearSearch"
        >✕</span>
      </div>
      <div class="search-results" v-if="showSearchResults && searchQuery">
        <div class="search-tabs">
          <div
            class="search-tab"
            :class="{ active: searchTab === 'top' }"
            @click="searchTab = 'top'"
          >Top</div>
          <div
            class="search-tab"
            :class="{ active: searchTab === 'latest' }"
            @click="searchTab = 'latest'"
          >Latest</div>
          <div
            class="search-tab"
            :class="{ active: searchTab === 'people' }"
            @click="searchTab = 'people'"
          >People</div>
          <div
            class="search-tab"
            :class="{ active: searchTab === 'media' }"
            @click="searchTab = 'media'"
          >Media</div>
        </div>
        <div class="search-results-content">
          <div v-if="filteredResults.length > 0">
            <div
              v-for="result in filteredResults"
              :key="result.id"
              class="search-result-item"
              @click="selectSearchResult(result)"
            >
              <div v-if="searchTab === 'people'" class="user-result">
                <img :src="result.avatar" :alt="result.name" class="user-avatar">
                <div class="user-info">
                  <div class="user-name">{{ result.name }}</div>
                  <div class="user-username">@{{ result.username }}</div>
                </div>
              </div>
              <div v-else class="tweet-result">
                <div class="result-content">
                  <span v-html="highlightSearchTerm(result.text)"></span>
                </div>
                <div class="result-meta">
                  <span class="result-user">{{ result.name }}</span>
                  <span class="result-time">· {{ result.time }}</span>
                </div>
              </div>
            </div>
          </div>
          <div v-else class="no-results">
            <p>No results for "{{ searchQuery }}"</p>
            <p class="no-results-suggestion">Try searching for something else</p>
          </div>
        </div>
      </div>
    </div>
    <div class="trends-container">
      <h2>Trends for you</h2>
      <div
        class="trend"
        v-for="trend in trends"
        :key="trend.id"
        @click="searchByTrend(trend.name)"
      >
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
      searchQuery: '',
      showSearchResults: false,
      searchTab: 'top',
      searchResults: [],
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
      ],
      allTweets: [
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
          text: 'Working on a new project with Vue.js. The component system is so intuitive! I\'m building a Twitter clone to practice my skills.',
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
          text: 'Vue 3 composition API is a game changer for large scale applications. What do you think? I\'ve been using it for a few months now and I\'m really impressed with the flexibility it provides.',
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
      ]
    }
  },
  computed: {
    filteredResults() {
      if (!this.searchQuery) return [];

      const query = this.searchQuery.toLowerCase();
      let results = [];

      if (this.searchTab === 'people') {
        // Filter users
        results = this.suggestedUsers.filter(user =>
          user.name.toLowerCase().includes(query) ||
          user.username.toLowerCase().includes(query)
        );

        // Add users from tweets
        const tweetUsers = this.allTweets.map(tweet => ({
          id: `user-${tweet.id}`,
          name: tweet.name,
          username: tweet.username,
          avatar: tweet.avatar
        }));

        // Filter and remove duplicates
        const uniqueUsernames = new Set(results.map(user => user.username));
        tweetUsers.forEach(user => {
          if (!uniqueUsernames.has(user.username) &&
              (user.name.toLowerCase().includes(query) ||
               user.username.toLowerCase().includes(query))) {
            results.push(user);
            uniqueUsernames.add(user.username);
          }
        });
      } else if (this.searchTab === 'media') {
        // Filter tweets with media
        results = this.allTweets.filter(tweet =>
          tweet.media &&
          tweet.media.length > 0 &&
          (tweet.text.toLowerCase().includes(query) ||
           tweet.name.toLowerCase().includes(query) ||
           tweet.username.toLowerCase().includes(query))
        );
      } else {
        // Filter all tweets
        results = this.allTweets.filter(tweet =>
          tweet.text.toLowerCase().includes(query) ||
          tweet.name.toLowerCase().includes(query) ||
          tweet.username.toLowerCase().includes(query)
        );

        // Sort by time for 'latest' tab
        if (this.searchTab === 'latest') {
          results.sort((a, b) => {
            // Simple sort by time string (in a real app, we'd use actual timestamps)
            return a.time.localeCompare(b.time);
          });
        }
      }

      return results;
    }
  },
  methods: {
    performSearch() {
      if (!this.searchQuery) return;

      // In a real app, this would make an API call to search
      this.searchResults = this.filteredResults;

      // Emit search event to parent component
      this.$emit('search', {
        query: this.searchQuery,
        results: this.searchResults,
        tab: this.searchTab
      });
    },
    clearSearch() {
      this.searchQuery = '';
      this.searchResults = [];
      this.showSearchResults = false;
    },
    highlightSearchTerm(text) {
      if (!this.searchQuery) return text;

      const regex = new RegExp(`(${this.searchQuery})`, 'gi');
      return text.replace(regex, '<span class="highlight">$1</span>');
    },
    selectSearchResult(result) {
      // In a real app, this would navigate to the tweet or user profile
      console.log('Selected result:', result);
      this.showSearchResults = false;

      // Emit select event to parent component
      this.$emit('select-result', result);
    },
    searchByTrend(trendName) {
      // Extract hashtag without the # symbol
      const hashtagText = trendName.startsWith('#') ? trendName.substring(1) : trendName;
      this.searchQuery = hashtagText;
      this.searchTab = 'top';
      this.showSearchResults = true;
      this.performSearch();
    }
  },
  mounted() {
    // Close search results when clicking outside
    document.addEventListener('click', (e) => {
      if (!this.$el.contains(e.target)) {
        this.showSearchResults = false;
      }
    });
  },
  beforeDestroy() {
    document.removeEventListener('click', this.closeSearchResults);
  }
}
</script>

<style scoped>
.sidebar {
  padding: 0 20px;
}

.search-box {
  margin-bottom: 20px;
  position: relative;
}

.search-input-container {
  position: relative;
  display: flex;
  align-items: center;
}

.search-icon {
  position: absolute;
  left: 15px;
  color: #657786;
  z-index: 1;
}

.search-box input {
  width: 100%;
  padding: 10px 15px 10px 40px;
  border-radius: 30px;
  border: 1px solid var(--border-color);
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  transition: all 0.3s ease;
}

.search-box input:focus {
  outline: none;
  border-color: var(--primary-color);
  background-color: var(--bg-primary);
  box-shadow: 0 0 0 1px var(--primary-color);
}

.clear-search {
  position: absolute;
  right: 15px;
  color: #657786;
  cursor: pointer;
  font-size: 14px;
}

.search-results {
  position: absolute;
  top: 45px;
  left: 0;
  right: 0;
  background-color: var(--bg-primary);
  border-radius: 8px;
  box-shadow: 0 1px 4px var(--shadow-color);
  z-index: 10;
  max-height: 400px;
  overflow-y: auto;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.search-tabs {
  display: flex;
  border-bottom: 1px solid #e1e8ed;
}

.search-tab {
  flex: 1;
  text-align: center;
  padding: 15px 0;
  font-weight: bold;
  cursor: pointer;
  color: #657786;
  transition: all 0.2s;
}

.search-tab.active {
  color: #1da1f2;
  border-bottom: 2px solid #1da1f2;
}

.search-tab:hover {
  background-color: rgba(29, 161, 242, 0.1);
}

.search-results-content {
  padding: 10px 0;
}

.search-result-item {
  padding: 10px 15px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.search-result-item:hover {
  background-color: #f5f8fa;
}

.user-result {
  display: flex;
  align-items: center;
}

.user-result .user-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
}

.tweet-result {
  padding: 5px 0;
}

.result-content {
  margin-bottom: 5px;
  line-height: 1.4;
}

.result-meta {
  font-size: 13px;
  color: #657786;
}

.result-user {
  font-weight: bold;
  color: #14171a;
}

.highlight {
  background-color: rgba(29, 161, 242, 0.2);
  font-weight: bold;
}

.no-results {
  padding: 20px;
  text-align: center;
  color: #657786;
}

.no-results-suggestion {
  font-size: 13px;
  margin-top: 5px;
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
  padding: 10px;
  margin-bottom: 5px;
  cursor: pointer;
  border-radius: 5px;
  transition: background-color 0.2s;
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
  transition: background-color 0.2s;
}

.follow-btn:hover {
  background-color: #333333;
}
</style>
