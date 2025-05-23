<template>
  <div class="twitter-app">
    <TwitterHeader />
    <div class="main-content">
      <TweetList />
      <Sidebar />
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
}

.main-content {
  display: grid;
  grid-template-columns: 1fr 350px;
  gap: 20px;
  margin-top: 20px;
}

@media (max-width: 768px) {
  .main-content {
    grid-template-columns: 1fr;
  }
}
</style>
