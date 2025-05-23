<template>
  <div class="twitter-app" :class="{ 'dark-theme': isDarkMode }">
    <TwitterHeader
      @theme-change="handleThemeChange"
      @login="showAuth = true"
      @logout="handleLogout"
      @signup="showAuth = true; authMode = 'register'"
      :user="currentUser"
    />
    <div class="main-content" v-if="currentUser || !requireAuth">
      <TweetList ref="tweetList" :filtered-tweets="filteredTweets" />
      <Sidebar
        @search="handleSearch"
        @select-result="handleSelectResult"
      />
    </div>
    <div class="welcome-screen" v-else-if="!showAuth">
      <div class="welcome-content">
        <h1>Welcome to Vue Twitter</h1>
        <p>Connect with friends and the world around you on Vue Twitter.</p>
        <div class="welcome-buttons">
          <button class="primary-btn" @click="showAuth = true">Log in</button>
          <button class="secondary-btn" @click="showAuth = true; authMode = 'register'">Sign up</button>
        </div>
      </div>
    </div>
    <div class="search-overlay" v-if="isSearching">
      <div class="search-info">
        <span class="search-query">Search results for: "{{ searchQuery }}"</span>
        <span class="search-count">{{ filteredTweets.length }} results</span>
        <button class="clear-search-btn" @click="clearSearch">Clear search</button>
      </div>
    </div>
    <Auth
      v-if="showAuth"
      @auth-success="handleAuthSuccess"
      @close="showAuth = false"
    />
    <MobileNavigation
      v-if="currentUser || !requireAuth"
      :isDarkMode="isDarkMode"
      @tab-change="handleTabChange"
    />
  </div>
</template>

<script>
import TwitterHeader from './components/Header.vue'
import TweetList from './components/TweetList.vue'
import Sidebar from './components/Sidebar.vue'
import Auth from './components/Auth.vue'
import MobileNavigation from './components/MobileNavigation.vue'

export default {
  name: 'App',
  components: {
    TwitterHeader,
    TweetList,
    Sidebar,
    Auth,
    MobileNavigation
  },
  data() {
    return {
      isSearching: false,
      searchQuery: '',
      searchTab: 'top',
      filteredTweets: [],
      allTweets: [],
      isDarkMode: false,
      currentUser: null,
      showAuth: false,
      authMode: 'login',
      requireAuth: false // Set to true to require authentication
    }
  },
  methods: {
    handleSearch(searchData) {
      this.searchQuery = searchData.query;
      this.searchTab = searchData.tab;
      this.filteredTweets = searchData.results;
      this.isSearching = this.searchQuery !== '';
    },
    handleSelectResult(result) {
      // If it's a user result, we could navigate to their profile
      // For now, just filter tweets by this user
      if (result.username) {
        this.searchQuery = '@' + result.username;
        this.isSearching = true;
        // Filter tweets by this user
        this.filteredTweets = this.allTweets.filter(tweet =>
          tweet.username === result.username
        );
      } else {
        // If it's a tweet result, just show that tweet
        this.filteredTweets = [result];
        this.isSearching = true;
      }
    },
    clearSearch() {
      this.searchQuery = '';
      this.isSearching = false;
      this.filteredTweets = [];
    },
    handleThemeChange(isDark) {
      this.isDarkMode = isDark;
    },
    handleAuthSuccess(user) {
      this.currentUser = user;
      this.showAuth = false;
    },
    handleLogout() {
      this.currentUser = null;
      localStorage.removeItem('user');
    },
    handleTabChange(tab) {
      console.log('Tab changed to:', tab);
      // In a real app, we would navigate to the selected tab
      // or update the UI accordingly
    }
  },
  mounted() {
    // In a real app, we would fetch tweets from an API
    // For now, we'll just get them from the TweetList component
    this.$nextTick(() => {
      if (this.$refs.tweetList) {
        this.allTweets = this.$refs.tweetList.tweets;
      }
    });

    // Check for saved theme preference
    const savedTheme = localStorage.getItem('darkMode');
    this.isDarkMode = savedTheme === 'true';

    // Check for saved user
    const savedUser = localStorage.getItem('user');
    if (savedUser) {
      try {
        this.currentUser = JSON.parse(savedUser);
      } catch (e) {
        console.error('Error parsing saved user:', e);
        localStorage.removeItem('user');
      }
    }
  }
}
</script>

<style>
:root {
  /* Light theme (default) */
  --primary-color: #1da1f2;
  --primary-color-dark: #0c8bd9;
  --bg-primary: #ffffff;
  --bg-secondary: #f5f8fa;
  --text-primary: #14171a;
  --text-secondary: #657786;
  --border-color: #e1e8ed;
  --hover-color: rgba(29, 161, 242, 0.1);
  --overlay-bg: rgba(29, 161, 242, 0.9);
  --overlay-text: #ffffff;
  --shadow-color: rgba(0, 0, 0, 0.25);
}

.dark-theme {
  /* Dark theme */
  --primary-color: #1da1f2;
  --primary-color-dark: #0c8bd9;
  --bg-primary: #15202b;
  --bg-secondary: #192734;
  --text-primary: #ffffff;
  --text-secondary: #8899a6;
  --border-color: #38444d;
  --hover-color: rgba(29, 161, 242, 0.1);
  --overlay-bg: rgba(29, 161, 242, 0.9);
  --overlay-text: #ffffff;
  --shadow-color: rgba(0, 0, 0, 0.5);
}

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  line-height: 1.3;
  transition: background-color 0.3s ease, color 0.3s ease;
}

#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.twitter-app {
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
}

.main-content {
  display: grid;
  grid-template-columns: 1fr 350px;
  gap: 20px;
  margin-top: 20px;
  padding: 0 15px;
  transition: all 0.3s ease;
}

.search-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background-color: var(--overlay-bg);
  color: var(--overlay-text);
  padding: 10px 0;
  z-index: 100;
  box-shadow: 0 1px 3px var(--shadow-color);
}

.search-info {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  padding: 0 20px;
}

.search-query {
  font-weight: bold;
  margin-right: 15px;
}

.search-count {
  color: rgba(255, 255, 255, 0.8);
  margin-right: auto;
}

.clear-search-btn {
  background-color: white;
  color: var(--primary-color);
  border: none;
  border-radius: 30px;
  padding: 6px 15px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.2s;
}

.clear-search-btn:hover {
  background-color: rgba(255, 255, 255, 0.9);
}

.welcome-screen {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: calc(100vh - 60px);
  padding: 20px;
  background-color: var(--bg-secondary);
  transition: background-color 0.3s ease;
}

.welcome-content {
  text-align: center;
  max-width: 600px;
}

.welcome-content h1 {
  font-size: 2.5rem;
  margin-bottom: 20px;
  color: var(--primary-color);
}

.welcome-content p {
  font-size: 1.2rem;
  margin-bottom: 30px;
  color: var(--text-secondary);
}

.welcome-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.primary-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 12px 24px;
  font-size: 1.1rem;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.primary-btn:hover {
  background-color: var(--primary-color-dark);
}

.secondary-btn {
  background-color: transparent;
  color: var(--primary-color);
  border: 1px solid var(--primary-color);
  border-radius: 30px;
  padding: 12px 24px;
  font-size: 1.1rem;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.secondary-btn:hover {
  background-color: rgba(29, 161, 242, 0.1);
}

@media (max-width: 1024px) {
  .twitter-app {
    max-width: 100%;
  }

  .main-content {
    grid-template-columns: 1fr 300px;
  }
}

@media (max-width: 768px) {
  .main-content {
    grid-template-columns: 1fr;
  }

  .search-info {
    flex-direction: column;
    align-items: flex-start;
  }

  .search-query, .search-count {
    margin-bottom: 5px;
  }

  .clear-search-btn {
    align-self: flex-end;
  }

  .welcome-buttons {
    flex-direction: column;
    gap: 10px;
  }
}

@media (max-width: 480px) {
  .twitter-app {
    padding-bottom: 60px; /* Space for mobile navigation */
  }

  .main-content {
    padding: 0 10px;
    margin-top: 10px;
  }

  .welcome-content h1 {
    font-size: 2rem;
  }

  .welcome-content p {
    font-size: 1rem;
  }
}
</style>
