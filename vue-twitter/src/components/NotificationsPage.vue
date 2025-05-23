<template>
  <div class="notifications-page">
    <div class="notifications-header">
      <h2>Notifications</h2>
      <div class="notifications-tabs">
        <div 
          class="tab" 
          :class="{ active: activeTab === 'all' }"
          @click="setActiveTab('all')"
        >
          All
        </div>
        <div 
          class="tab" 
          :class="{ active: activeTab === 'mentions' }"
          @click="setActiveTab('mentions')"
        >
          Mentions
        </div>
      </div>
      <div class="notifications-actions">
        <button class="mark-all-read" @click="markAllAsRead" v-if="hasUnreadNotifications">
          Mark all as read
        </button>
        <button class="settings-btn" @click="openSettings">
          ⚙️
        </button>
      </div>
    </div>
    
    <div class="notifications-content">
      <div v-if="loading" class="loading-indicator">
        Loading notifications...
      </div>
      <div v-else-if="filteredNotifications.length === 0" class="empty-state">
        <div v-if="activeTab === 'all'">
          <h3>No notifications yet</h3>
          <p>When someone interacts with you or your tweets, it'll show up here.</p>
        </div>
        <div v-else>
          <h3>No mentions yet</h3>
          <p>When someone mentions you in a tweet, it'll show up here.</p>
        </div>
      </div>
      <div v-else class="notifications-list">
        <Notification 
          v-for="notification in filteredNotifications" 
          :key="notification.id" 
          :notification="notification"
          @mark-read="markAsRead"
          @click="handleNotificationClick"
        />
      </div>
    </div>
    
    <div v-if="showSettings" class="settings-overlay" @click="showSettings = false">
      <div class="settings-panel" @click.stop>
        <div class="settings-header">
          <h3>Notification settings</h3>
          <button class="close-btn" @click="showSettings = false">✕</button>
        </div>
        <div class="settings-content">
          <div class="settings-group">
            <h4>Filter notifications</h4>
            <div class="setting-item">
              <input type="checkbox" id="quality" v-model="settings.filterQuality">
              <label for="quality">Quality filter</label>
              <p class="setting-description">Filter out notifications that appear to be low-quality</p>
            </div>
            <div class="setting-item">
              <input type="checkbox" id="mentions" v-model="settings.showMentionsOnly">
              <label for="mentions">Only show mentions</label>
              <p class="setting-description">Only receive notifications for tweets that mention you</p>
            </div>
          </div>
          <div class="settings-group">
            <h4>Preferences</h4>
            <div class="setting-item">
              <input type="checkbox" id="likes" v-model="settings.likes">
              <label for="likes">Likes</label>
            </div>
            <div class="setting-item">
              <input type="checkbox" id="replies" v-model="settings.replies">
              <label for="replies">Replies</label>
            </div>
            <div class="setting-item">
              <input type="checkbox" id="retweets" v-model="settings.retweets">
              <label for="retweets">Retweets</label>
            </div>
            <div class="setting-item">
              <input type="checkbox" id="follows" v-model="settings.follows">
              <label for="follows">New followers</label>
            </div>
          </div>
          <div class="settings-actions">
            <button class="save-btn" @click="saveSettings">Save</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Notification from './Notification.vue'

export default {
  name: 'NotificationsPage',
  components: {
    Notification
  },
  data() {
    return {
      notifications: [],
      activeTab: 'all',
      loading: true,
      showSettings: false,
      settings: {
        filterQuality: true,
        showMentionsOnly: false,
        likes: true,
        replies: true,
        retweets: true,
        follows: true
      }
    }
  },
  computed: {
    filteredNotifications() {
      if (this.activeTab === 'mentions') {
        return this.notifications.filter(notification => notification.type === 'mention');
      }
      
      // Apply settings filters
      return this.notifications.filter(notification => {
        if (this.settings.showMentionsOnly && notification.type !== 'mention') {
          return false;
        }
        
        switch (notification.type) {
          case 'like':
            return this.settings.likes;
          case 'reply':
            return this.settings.replies;
          case 'retweet':
            return this.settings.retweets;
          case 'follow':
            return this.settings.follows;
          default:
            return true;
        }
      });
    },
    hasUnreadNotifications() {
      return this.filteredNotifications.some(notification => !notification.read);
    }
  },
  methods: {
    setActiveTab(tab) {
      this.activeTab = tab;
    },
    markAsRead(notificationId) {
      const index = this.notifications.findIndex(n => n.id === notificationId);
      if (index !== -1) {
        this.notifications[index].read = true;
        this.saveNotifications();
      }
    },
    markAllAsRead() {
      this.notifications.forEach(notification => {
        notification.read = true;
      });
      this.saveNotifications();
    },
    openSettings() {
      this.showSettings = true;
    },
    saveSettings() {
      // In a real app, we would save settings to the server
      // For now, we'll just save to localStorage
      localStorage.setItem('notificationSettings', JSON.stringify(this.settings));
      this.showSettings = false;
    },
    saveNotifications() {
      // In a real app, we would save to the server
      // For now, we'll just save to localStorage
      localStorage.setItem('notifications', JSON.stringify(this.notifications));
    },
    handleNotificationClick(notification) {
      // In a real app, we would navigate to the relevant content
      console.log('Clicked notification:', notification);
      
      // Emit event to parent component to handle navigation
      this.$emit('notification-click', notification);
    },
    loadNotifications() {
      this.loading = true;
      
      // In a real app, we would fetch notifications from the server
      // For now, we'll simulate an API call with setTimeout
      setTimeout(() => {
        // Check if we have saved notifications in localStorage
        const savedNotifications = localStorage.getItem('notifications');
        if (savedNotifications) {
          try {
            this.notifications = JSON.parse(savedNotifications);
            this.loading = false;
            return;
          } catch (e) {
            console.error('Error parsing saved notifications:', e);
          }
        }
        
        // If no saved notifications or error parsing, use mock data
        this.notifications = [
          {
            id: 1,
            type: 'like',
            name: 'Jane Smith',
            username: 'janesmith',
            avatar: 'https://via.placeholder.com/50',
            time: new Date(Date.now() - 1000 * 60 * 5).toISOString(), // 5 minutes ago
            read: false,
            tweetId: 123,
            tweetPreview: 'Just launched my new Vue.js project! Check it out and let me know what you think.'
          },
          {
            id: 2,
            type: 'retweet',
            name: 'John Doe',
            username: 'johndoe',
            avatar: 'https://via.placeholder.com/50',
            time: new Date(Date.now() - 1000 * 60 * 30).toISOString(), // 30 minutes ago
            read: false,
            tweetId: 123,
            tweetPreview: 'Just launched my new Vue.js project! Check it out and let me know what you think.'
          },
          {
            id: 3,
            type: 'follow',
            name: 'Vue Master',
            username: 'vuemaster',
            avatar: 'https://via.placeholder.com/50',
            time: new Date(Date.now() - 1000 * 60 * 60 * 2).toISOString(), // 2 hours ago
            read: true
          },
          {
            id: 4,
            type: 'mention',
            name: 'Code Ninja',
            username: 'codeninja',
            avatar: 'https://via.placeholder.com/50',
            time: new Date(Date.now() - 1000 * 60 * 60 * 5).toISOString(), // 5 hours ago
            read: true,
            tweetId: 456,
            tweetPreview: 'Hey @currentuser, what do you think about the new Vue 3 Composition API?'
          },
          {
            id: 5,
            type: 'reply',
            name: 'Tech Enthusiast',
            username: 'techenthusiast',
            avatar: 'https://via.placeholder.com/50',
            time: new Date(Date.now() - 1000 * 60 * 60 * 24).toISOString(), // 1 day ago
            read: true,
            tweetId: 123,
            tweetPreview: 'This looks amazing! How long did it take you to build it?'
          }
        ];
        
        this.loading = false;
        this.saveNotifications();
      }, 1000);
    },
    loadSettings() {
      // Load settings from localStorage
      const savedSettings = localStorage.getItem('notificationSettings');
      if (savedSettings) {
        try {
          const parsedSettings = JSON.parse(savedSettings);
          this.settings = { ...this.settings, ...parsedSettings };
        } catch (e) {
          console.error('Error parsing saved notification settings:', e);
        }
      }
    }
  },
  mounted() {
    this.loadNotifications();
    this.loadSettings();
  }
}
</script>

<style scoped>
.notifications-page {
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border-color);
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.notifications-header {
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.notifications-header h2 {
  margin: 0 0 15px 0;
  font-size: 20px;
  color: var(--text-primary);
}

.notifications-tabs {
  display: flex;
  margin-bottom: 15px;
}

.tab {
  flex: 1;
  text-align: center;
  padding: 10px 0;
  font-weight: bold;
  color: var(--text-secondary);
  cursor: pointer;
  transition: color 0.3s ease, background-color 0.3s ease;
  position: relative;
}

.tab:hover {
  background-color: var(--hover-color);
  color: var(--primary-color);
}

.tab.active {
  color: var(--primary-color);
}

.tab.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 4px;
  background-color: var(--primary-color);
  border-radius: 4px 4px 0 0;
}

.notifications-actions {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

.mark-all-read {
  background-color: transparent;
  color: var(--primary-color);
  border: none;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  margin-right: 10px;
}

.mark-all-read:hover {
  text-decoration: underline;
}

.settings-btn {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
  color: var(--text-secondary);
  padding: 5px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s ease;
}

.settings-btn:hover {
  background-color: var(--hover-color);
}

.notifications-content {
  min-height: 300px;
}

.loading-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px;
  color: var(--text-secondary);
}

.empty-state {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 300px;
  text-align: center;
  padding: 0 20px;
}

.empty-state h3 {
  color: var(--text-primary);
  margin-bottom: 8px;
}

.empty-state p {
  color: var(--text-secondary);
}

.settings-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  justify-content: center;
  align-items: center;
}

.settings-panel {
  width: 100%;
  max-width: 500px;
  max-height: 90vh;
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease;
}

.settings-header {
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: border-color 0.3s ease;
}

.settings-header h3 {
  margin: 0;
  font-size: 18px;
  color: var(--text-primary);
}

.close-btn {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
  color: var(--text-primary);
  padding: 5px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s ease;
}

.close-btn:hover {
  background-color: var(--hover-color);
}

.settings-content {
  padding: 15px;
  overflow-y: auto;
}

.settings-group {
  margin-bottom: 20px;
}

.settings-group h4 {
  margin: 0 0 10px 0;
  font-size: 16px;
  color: var(--text-primary);
}

.setting-item {
  margin-bottom: 15px;
  display: flex;
  align-items: flex-start;
}

.setting-item input[type="checkbox"] {
  margin-right: 10px;
  margin-top: 3px;
}

.setting-item label {
  font-weight: bold;
  color: var(--text-primary);
}

.setting-description {
  margin: 5px 0 0 25px;
  font-size: 14px;
  color: var(--text-secondary);
}

.settings-actions {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.save-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.save-btn:hover {
  background-color: var(--primary-color-dark);
}

@media (max-width: 768px) {
  .notifications-page {
    border-radius: 0;
    border-left: none;
    border-right: none;
  }
  
  .notifications-header {
    padding: 10px;
  }
  
  .settings-panel {
    max-width: 100%;
    height: 100%;
    max-height: 100%;
    border-radius: 0;
  }
}

@media (max-width: 480px) {
  .notifications-header h2 {
    font-size: 18px;
  }
  
  .tab {
    padding: 8px 0;
    font-size: 14px;
  }
  
  .mark-all-read {
    font-size: 12px;
  }
  
  .settings-btn {
    font-size: 16px;
  }
}
</style>
