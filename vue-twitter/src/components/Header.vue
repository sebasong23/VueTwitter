<template>
  <header class="twitter-header">
    <div class="logo">
      <h1>Vue Twitter</h1>
    </div>
    <nav class="main-nav">
      <ul>
        <li><a href="#" class="active">Home</a></li>
        <li><a href="#">Explore</a></li>
        <li><a href="#">Notifications</a></li>
        <li><a href="#">Messages</a></li>
        <li v-if="user"><a href="#">Profile</a></li>
      </ul>
    </nav>
    <div class="user-actions">
      <button
        class="theme-toggle"
        @click="toggleTheme"
        :title="isDarkMode ? 'Switch to light mode' : 'Switch to dark mode'"
      >
        <span v-if="isDarkMode">☀️</span>
        <span v-else>🌙</span>
      </button>
      <button v-if="user" class="tweet-btn">Tweet</button>
      <button v-else class="login-btn" @click="$emit('login')">Log in</button>
      <UserProfile
        v-if="user"
        :user="user"
        @logout="$emit('logout')"
      />
    </div>
  </header>
</template>

<script>
import UserProfile from './UserProfile.vue'

export default {
  name: 'TwitterHeader',
  components: {
    UserProfile
  },
  props: {
    user: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      isDarkMode: false
    }
  },
  methods: {
    toggleTheme() {
      this.isDarkMode = !this.isDarkMode;
      document.body.classList.toggle('dark-mode', this.isDarkMode);

      // Save preference to localStorage
      localStorage.setItem('darkMode', this.isDarkMode ? 'true' : 'false');

      // Emit event to parent components
      this.$emit('theme-change', this.isDarkMode);
    }
  },
  mounted() {
    // Check for saved theme preference
    const savedTheme = localStorage.getItem('darkMode');
    if (savedTheme === 'true') {
      this.isDarkMode = true;
      document.body.classList.add('dark-mode');
    }
  }
}
</script>

<style scoped>
.twitter-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  border-bottom: 1px solid var(--border-color);
  background-color: var(--bg-primary);
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.logo h1 {
  color: var(--primary-color);
  font-size: 1.5rem;
  margin: 0;
}

.main-nav ul {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
}

.main-nav li {
  margin: 0 15px;
}

.main-nav a {
  text-decoration: none;
  color: var(--text-secondary);
  font-weight: bold;
  padding: 10px 0;
  transition: color 0.3s ease;
}

.main-nav a.active {
  color: var(--primary-color);
  border-bottom: 2px solid var(--primary-color);
}

.main-nav a:hover {
  color: var(--primary-color);
}

.user-actions {
  display: flex;
  align-items: center;
  gap: 10px;
}

.theme-toggle {
  background: none;
  border: none;
  font-size: 1.2rem;
  margin-right: 15px;
  cursor: pointer;
  padding: 5px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s ease;
}

.theme-toggle:hover {
  background-color: var(--hover-color);
}

.tweet-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 10px 20px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.tweet-btn:hover {
  background-color: var(--primary-color-dark);
}

.login-btn {
  background-color: transparent;
  color: var(--primary-color);
  border: 1px solid var(--primary-color);
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease;
}

.login-btn:hover {
  background-color: rgba(29, 161, 242, 0.1);
}
</style>
