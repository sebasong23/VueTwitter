<template>
  <header class="twitter-header">
    <div class="header-left">
      <div class="logo">
        <h1>Vue Twitter</h1>
      </div>
    </div>
    <nav class="main-nav">
      <ul>
        <li><a href="#" :class="{ active: activeTab === 'home' }" @click.prevent="setActiveTab('home')">Home</a></li>
        <li><a href="#" :class="{ active: activeTab === 'explore' }" @click.prevent="setActiveTab('explore')">Explore</a></li>
        <li>
          <a href="#" :class="{ active: activeTab === 'notifications' }" @click.prevent="setActiveTab('notifications')">
            Notifications
            <span class="notification-badge-container">
              <NotificationBadge :count="unreadNotifications" />
            </span>
          </a>
        </li>
        <li><a href="#" :class="{ active: activeTab === 'messages' }" @click.prevent="setActiveTab('messages')">Messages</a></li>
        <li v-if="user"><a href="#" :class="{ active: activeTab === 'profile' }" @click.prevent="setActiveTab('profile')">Profile</a></li>
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
        @view-profile="handleViewProfile"
        class="desktop-only"
      />
    </div>
    <button class="mobile-menu-btn" @click="toggleMobileMenu">
      <span>☰</span>
    </button>
    <div class="mobile-menu" v-if="showMobileMenu" @click="toggleMobileMenu">
      <div class="mobile-menu-content" @click.stop>
        <div class="mobile-menu-header">
          <h2>Account info</h2>
          <button class="close-btn" @click="toggleMobileMenu">✕</button>
        </div>
        <UserProfile
          v-if="user"
          :user="user"
          @logout="handleLogout"
          @view-profile="handleViewProfile"
        />
        <div v-else class="mobile-auth-buttons">
          <button class="login-btn" @click="handleLogin">Log in</button>
          <button class="signup-btn" @click="handleSignup">Sign up</button>
        </div>
      </div>
    </div>
  </header>
</template>

<script>
import UserProfile from './UserProfile.vue'
import NotificationBadge from './NotificationBadge.vue'

export default {
  name: 'TwitterHeader',
  components: {
    UserProfile,
    NotificationBadge
  },
  props: {
    user: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      isDarkMode: false,
      showMobileMenu: false,
      activeTab: 'home',
      unreadNotifications: 0
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
    },
    toggleMobileMenu() {
      this.showMobileMenu = !this.showMobileMenu;
      if (this.showMobileMenu) {
        document.body.style.overflow = 'hidden';
      } else {
        document.body.style.overflow = '';
      }
    },
    handleLogout() {
      this.toggleMobileMenu();
      this.$emit('logout');
    },
    handleLogin() {
      this.toggleMobileMenu();
      this.$emit('login');
    },
    handleSignup() {
      this.toggleMobileMenu();
      this.$emit('signup');
    },
    setActiveTab(tab) {
      this.activeTab = tab;
      this.$emit('tab-change', tab);
    },
    handleViewProfile(username) {
      this.activeTab = 'profile';
      this.toggleMobileMenu();
      this.$emit('view-profile', username);
    }
  },
  mounted() {
    // Check for saved theme preference
    const savedTheme = localStorage.getItem('darkMode');
    if (savedTheme === 'true') {
      this.isDarkMode = true;
      document.body.classList.add('dark-mode');
    }

    // Fetch unread notifications count
    this.fetchUnreadNotificationsCount();

    // Set up interval to check for new notifications
    this.notificationInterval = setInterval(() => {
      this.fetchUnreadNotificationsCount();
    }, 60000); // Check every minute
  },

  beforeDestroy() {
    // Clear the notification interval when component is destroyed
    if (this.notificationInterval) {
      clearInterval(this.notificationInterval);
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
    },
    toggleMobileMenu() {
      this.showMobileMenu = !this.showMobileMenu;
      if (this.showMobileMenu) {
        document.body.style.overflow = 'hidden';
      } else {
        document.body.style.overflow = '';
      }
    },
    handleLogout() {
      this.toggleMobileMenu();
      this.$emit('logout');
    },
    handleLogin() {
      this.toggleMobileMenu();
      this.$emit('login');
    },
    handleSignup() {
      this.toggleMobileMenu();
      this.$emit('signup');
    },
    setActiveTab(tab) {
      this.activeTab = tab;
      this.$emit('tab-change', tab);
    },
    handleViewProfile(username) {
      this.activeTab = 'profile';
      this.toggleMobileMenu();
      this.$emit('view-profile', username);
    },
    fetchUnreadNotificationsCount() {
      // In a real app, we would make an API call to get the count
      // For now, we'll check localStorage
      const savedNotifications = localStorage.getItem('notifications');
      if (savedNotifications) {
        try {
          const notifications = JSON.parse(savedNotifications);
          this.unreadNotifications = notifications.filter(n => !n.read).length;
        } catch (e) {
          console.error('Error parsing saved notifications:', e);
        }
      }
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
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-left {
  display: flex;
  align-items: center;
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

.notification-badge-container {
  position: relative;
  display: inline-block;
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

.mobile-menu-btn {
  display: none;
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: var(--text-primary);
}

.mobile-menu {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  justify-content: flex-start;
}

.mobile-menu-content {
  background-color: var(--bg-primary);
  width: 80%;
  max-width: 300px;
  height: 100%;
  overflow-y: auto;
  transition: background-color 0.3s ease;
  animation: slideIn 0.3s forwards;
}

@keyframes slideIn {
  from { transform: translateX(-100%); }
  to { transform: translateX(0); }
}

.mobile-menu-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
}

.mobile-menu-header h2 {
  margin: 0;
  font-size: 1.2rem;
  color: var(--text-primary);
}

.close-btn {
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
  color: var(--text-primary);
}

.mobile-auth-buttons {
  padding: 15px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.signup-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.signup-btn:hover {
  background-color: var(--primary-color-dark);
}

@media (max-width: 1024px) {
  .twitter-header {
    padding: 10px 15px;
  }

  .main-nav li {
    margin: 0 10px;
  }

  .tweet-btn {
    padding: 8px 16px;
  }
}

@media (max-width: 768px) {
  .main-nav {
    display: none;
  }

  .mobile-menu-btn {
    display: block;
  }

  .desktop-only {
    display: none;
  }

  .tweet-btn {
    display: none;
  }
}

@media (max-width: 480px) {
  .twitter-header {
    padding: 10px;
  }

  .logo h1 {
    font-size: 1.2rem;
  }

  .theme-toggle {
    margin-right: 5px;
  }

  .login-btn {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
}
</style>
