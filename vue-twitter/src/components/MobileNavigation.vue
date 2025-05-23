<template>
  <div class="mobile-navigation" :class="{ 'dark-mode': isDarkMode }">
    <div class="nav-item" :class="{ active: activeTab === 'home' }" @click="setActiveTab('home')">
      <span class="nav-icon">🏠</span>
      <span class="nav-label">Home</span>
    </div>
    <div class="nav-item" :class="{ active: activeTab === 'explore' }" @click="setActiveTab('explore')">
      <span class="nav-icon">🔍</span>
      <span class="nav-label">Explore</span>
    </div>
    <div class="nav-item" :class="{ active: activeTab === 'notifications' }" @click="setActiveTab('notifications')">
      <span class="nav-icon">
        🔔
        <NotificationBadge v-if="unreadNotifications > 0" :count="unreadNotifications" />
      </span>
      <span class="nav-label">Notifications</span>
    </div>
    <div class="nav-item" :class="{ active: activeTab === 'messages' }" @click="setActiveTab('messages')">
      <span class="nav-icon">✉️</span>
      <span class="nav-label">Messages</span>
    </div>
    <div class="nav-item" :class="{ active: activeTab === 'profile' }" @click="setActiveTab('profile')">
      <span class="nav-icon">👤</span>
      <span class="nav-label">Profile</span>
    </div>
  </div>
</template>

<script>
import NotificationBadge from './NotificationBadge.vue'

export default {
  name: 'MobileNavigation',
  components: {
    NotificationBadge
  },
  props: {
    isDarkMode: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      activeTab: 'home',
      unreadNotifications: 0
    }
  },
  methods: {
    setActiveTab(tab) {
      this.activeTab = tab;
      this.$emit('tab-change', tab);
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
  },
  mounted() {
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
  }
}
</script>

<style scoped>
.mobile-navigation {
  display: none;
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: var(--bg-primary);
  border-top: 1px solid var(--border-color);
  height: 60px;
  z-index: 100;
  justify-content: space-around;
  align-items: center;
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.nav-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 8px;
  cursor: pointer;
  color: var(--text-secondary);
  transition: color 0.3s ease;
  width: 20%;
}

.nav-item.active {
  color: var(--primary-color);
}

.nav-icon {
  font-size: 1.2rem;
  margin-bottom: 2px;
  position: relative;
}

.nav-label {
  font-size: 0.7rem;
}

@media (max-width: 768px) {
  .mobile-navigation {
    display: flex;
  }
}
</style>
