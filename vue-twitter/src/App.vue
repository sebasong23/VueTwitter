<template>
  <div class="twitter-app">
    <TwitterHeader />
    <div class="main-content">
      <TweetList ref="tweetList" :filtered-tweets="filteredTweets" />
      <Sidebar
        @search="handleSearch"
        @select-result="handleSelectResult"
      />
    </div>
    <div class="search-overlay" v-if="isSearching">
      <div class="search-info">
        <span class="search-query">Search results for: "{{ searchQuery }}"</span>
        <span class="search-count">{{ filteredTweets.length }} results</span>
        <button class="clear-search-btn" @click="clearSearch">Clear search</button>
      </div>
    </div>
  </div>
</template>

<script>
import TwitterHeader from './components/Header.vue'
import TweetList from './components/TweetList.vue'
import Sidebar from './components/Sidebar.vue'

export default {
  name: 'App',
  components: {
    TwitterHeader,
    TweetList,
    Sidebar
  },
  data() {
    return {
      isSearching: false,
      searchQuery: '',
      searchTab: 'top',
      filteredTweets: [],
      allTweets: []
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
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  background-color: #f5f8fa;
  color: #14171a;
  line-height: 1.3;
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
}

.search-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background-color: rgba(29, 161, 242, 0.9);
  color: white;
  padding: 10px 0;
  z-index: 100;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.25);
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
  color: #1da1f2;
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
}
</style>
