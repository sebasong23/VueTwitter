<template>
  <div class="conversations-list">
    <div class="conversations-header">
      <h2>Messages</h2>
      <button class="new-message-btn" @click="showNewMessage = true">
        <span class="new-message-icon">✉️</span>
        <span class="new-message-text">New message</span>
      </button>
    </div>
    
    <div class="search-container">
      <div class="search-input">
        <span class="search-icon">🔍</span>
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Search for people and groups"
        >
        <span 
          v-if="searchQuery" 
          class="clear-search" 
          @click="searchQuery = ''"
        >✕</span>
      </div>
    </div>
    
    <div class="conversations-content">
      <div v-if="loading" class="loading-indicator">
        Loading conversations...
      </div>
      <div v-else-if="filteredConversations.length === 0" class="empty-state">
        <div v-if="searchQuery">
          <h3>No results found</h3>
          <p>We couldn't find any results for "{{ searchQuery }}"</p>
        </div>
        <div v-else>
          <h3>No messages yet</h3>
          <p>When you send or receive messages, they'll show up here.</p>
          <button class="start-message-btn" @click="showNewMessage = true">Start a conversation</button>
        </div>
      </div>
      <div v-else class="conversations-items">
        <div 
          v-for="conversation in filteredConversations" 
          :key="conversation.id" 
          class="conversation-item"
          :class="{ 'unread': conversation.unread, 'active': selectedConversationId === conversation.id }"
          @click="selectConversation(conversation.id)"
        >
          <div class="conversation-avatar">
            <img :src="conversation.recipientAvatar" :alt="conversation.recipientName">
          </div>
          <div class="conversation-details">
            <div class="conversation-header-row">
              <div class="conversation-name">{{ conversation.recipientName }}</div>
              <div class="conversation-time">{{ formatTime(conversation.timestamp) }}</div>
            </div>
            <div class="conversation-preview">
              <div class="conversation-last-message">{{ conversation.lastMessage }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <div v-if="showNewMessage" class="new-message-overlay" @click="showNewMessage = false">
      <div class="new-message-panel" @click.stop>
        <div class="new-message-header">
          <h3>New message</h3>
          <button class="close-button" @click="showNewMessage = false">✕</button>
        </div>
        <div class="new-message-search">
          <input 
            type="text" 
            v-model="newMessageSearch" 
            placeholder="Search for people"
            ref="newMessageInput"
          >
        </div>
        <div class="new-message-results">
          <div v-if="newMessageSearch && filteredUsers.length === 0" class="no-results">
            <p>No users found</p>
          </div>
          <div v-else class="user-list">
            <div 
              v-for="user in filteredUsers" 
              :key="user.id" 
              class="user-item"
              @click="startConversation(user)"
            >
              <div class="user-avatar">
                <img :src="user.avatar" :alt="user.name">
              </div>
              <div class="user-details">
                <div class="user-name">{{ user.name }}</div>
                <div class="user-username">@{{ user.username }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ConversationsList',
  props: {
    currentUserId: {
      type: [Number, String],
      required: true
    },
    selectedConversationId: {
      type: [Number, String],
      default: null
    }
  },
  data() {
    return {
      conversations: [],
      loading: true,
      searchQuery: '',
      showNewMessage: false,
      newMessageSearch: '',
      users: []
    }
  },
  computed: {
    filteredConversations() {
      if (!this.searchQuery) return this.conversations;
      
      const query = this.searchQuery.toLowerCase();
      return this.conversations.filter(conversation => 
        conversation.recipientName.toLowerCase().includes(query) ||
        conversation.recipientUsername.toLowerCase().includes(query) ||
        conversation.lastMessage.toLowerCase().includes(query)
      );
    },
    filteredUsers() {
      if (!this.newMessageSearch) return this.users;
      
      const query = this.newMessageSearch.toLowerCase();
      return this.users.filter(user => 
        user.name.toLowerCase().includes(query) ||
        user.username.toLowerCase().includes(query)
      );
    }
  },
  methods: {
    loadConversations() {
      this.loading = true;
      
      // In a real app, we would fetch conversations from the server
      // For now, we'll simulate an API call with setTimeout
      setTimeout(() => {
        // Check if we have saved conversations in localStorage
        const savedConversations = localStorage.getItem('conversations');
        if (savedConversations) {
          try {
            this.conversations = JSON.parse(savedConversations);
            this.loading = false;
            return;
          } catch (e) {
            console.error('Error parsing saved conversations:', e);
          }
        }
        
        // If no saved conversations or error parsing, use mock data
        this.conversations = [
          {
            id: '1',
            recipientId: 2,
            recipientName: 'Jane Smith',
            recipientUsername: 'janesmith',
            recipientAvatar: 'https://via.placeholder.com/50',
            lastMessage: 'That looks great! I\'ll have to try that approach in my project.',
            timestamp: new Date(Date.now() - 1000 * 60 * 30).toISOString(), // 30 minutes ago
            unread: false
          },
          {
            id: '2',
            recipientId: 3,
            recipientName: 'John Doe',
            recipientUsername: 'johndoe',
            recipientAvatar: 'https://via.placeholder.com/50',
            lastMessage: 'Thanks for the feedback on my Vue.js project!',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 2).toISOString(), // 2 hours ago
            unread: true
          },
          {
            id: '3',
            recipientId: 4,
            recipientName: 'Vue Master',
            recipientUsername: 'vuemaster',
            recipientAvatar: 'https://via.placeholder.com/50',
            lastMessage: 'I\'ll send you the link to my new Vue.js course when it\'s ready.',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 24).toISOString(), // 1 day ago
            unread: false
          }
        ];
        
        // Save to localStorage
        localStorage.setItem('conversations', JSON.stringify(this.conversations));
        
        this.loading = false;
      }, 1000);
    },
    loadUsers() {
      // In a real app, we would fetch users from the server
      // For now, we'll use mock data
      this.users = [
        {
          id: 2,
          name: 'Jane Smith',
          username: 'janesmith',
          avatar: 'https://via.placeholder.com/50'
        },
        {
          id: 3,
          name: 'John Doe',
          username: 'johndoe',
          avatar: 'https://via.placeholder.com/50'
        },
        {
          id: 4,
          name: 'Vue Master',
          username: 'vuemaster',
          avatar: 'https://via.placeholder.com/50'
        },
        {
          id: 5,
          name: 'Code Ninja',
          username: 'codeninja',
          avatar: 'https://via.placeholder.com/50'
        },
        {
          id: 6,
          name: 'Tech Enthusiast',
          username: 'techenthusiast',
          avatar: 'https://via.placeholder.com/50'
        }
      ];
    },
    formatTime(timestamp) {
      const date = new Date(timestamp);
      const now = new Date();
      const diffInSeconds = Math.floor((now - date) / 1000);
      
      if (diffInSeconds < 60) {
        return 'now';
      } else if (diffInSeconds < 3600) {
        return `${Math.floor(diffInSeconds / 60)}m`;
      } else if (diffInSeconds < 86400) {
        return `${Math.floor(diffInSeconds / 3600)}h`;
      } else if (diffInSeconds < 604800) {
        return `${Math.floor(diffInSeconds / 86400)}d`;
      } else {
        // Format as MM/DD/YYYY
        return date.toLocaleDateString();
      }
    },
    selectConversation(conversationId) {
      // Mark conversation as read
      const index = this.conversations.findIndex(c => c.id === conversationId);
      if (index !== -1 && this.conversations[index].unread) {
        this.conversations[index].unread = false;
        localStorage.setItem('conversations', JSON.stringify(this.conversations));
      }
      
      this.$emit('select-conversation', conversationId);
    },
    startConversation(user) {
      // Check if conversation already exists
      const existingConversation = this.conversations.find(c => c.recipientId === user.id);
      if (existingConversation) {
        this.selectConversation(existingConversation.id);
        this.showNewMessage = false;
        return;
      }
      
      // Create new conversation
      const newConversation = {
        id: Date.now().toString(),
        recipientId: user.id,
        recipientName: user.name,
        recipientUsername: user.username,
        recipientAvatar: user.avatar,
        lastMessage: 'Start a new conversation',
        timestamp: new Date().toISOString(),
        unread: false,
        messages: []
      };
      
      this.conversations.unshift(newConversation);
      localStorage.setItem('conversations', JSON.stringify(this.conversations));
      
      this.selectConversation(newConversation.id);
      this.showNewMessage = false;
    },
    deleteConversation(conversationId) {
      const index = this.conversations.findIndex(c => c.id === conversationId);
      if (index !== -1) {
        this.conversations.splice(index, 1);
        localStorage.setItem('conversations', JSON.stringify(this.conversations));
        
        if (this.selectedConversationId === conversationId) {
          this.$emit('select-conversation', null);
        }
      }
    }
  },
  mounted() {
    this.loadConversations();
    this.loadUsers();
  },
  watch: {
    showNewMessage(val) {
      if (val) {
        this.$nextTick(() => {
          this.$refs.newMessageInput.focus();
        });
      } else {
        this.newMessageSearch = '';
      }
    }
  }
}
</script>

<style scoped>
.conversations-list {
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border-color);
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.conversations-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.conversations-header h2 {
  margin: 0;
  font-size: 20px;
  color: var(--text-primary);
}

.new-message-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  display: flex;
  align-items: center;
  transition: background-color 0.3s ease;
}

.new-message-btn:hover {
  background-color: var(--primary-color-dark);
}

.new-message-icon {
  margin-right: 5px;
}

.search-container {
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.search-input {
  position: relative;
  display: flex;
  align-items: center;
}

.search-icon {
  position: absolute;
  left: 10px;
  color: var(--text-secondary);
}

.search-input input {
  width: 100%;
  padding: 10px 30px 10px 35px;
  border-radius: 30px;
  border: 1px solid var(--border-color);
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  transition: all 0.3s ease;
}

.search-input input:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 1px var(--primary-color);
}

.clear-search {
  position: absolute;
  right: 10px;
  color: var(--text-secondary);
  cursor: pointer;
  font-size: 14px;
}

.conversations-content {
  flex: 1;
  overflow-y: auto;
}

.loading-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 200px;
  color: var(--text-secondary);
}

.empty-state {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 200px;
  text-align: center;
  padding: 0 20px;
}

.empty-state h3 {
  color: var(--text-primary);
  margin-bottom: 8px;
}

.empty-state p {
  color: var(--text-secondary);
  margin-bottom: 15px;
}

.start-message-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.start-message-btn:hover {
  background-color: var(--primary-color-dark);
}

.conversation-item {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.conversation-item:hover {
  background-color: var(--hover-color);
}

.conversation-item.active {
  background-color: rgba(29, 161, 242, 0.1);
}

.conversation-item.unread {
  background-color: rgba(29, 161, 242, 0.05);
}

.conversation-avatar {
  margin-right: 10px;
}

.conversation-avatar img {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  object-fit: cover;
}

.conversation-details {
  flex: 1;
  min-width: 0; /* Needed for text-overflow to work */
}

.conversation-header-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 5px;
}

.conversation-name {
  font-weight: bold;
  color: var(--text-primary);
}

.conversation-item.unread .conversation-name {
  font-weight: 800;
}

.conversation-time {
  color: var(--text-secondary);
  font-size: 14px;
}

.conversation-preview {
  display: flex;
  align-items: center;
}

.conversation-last-message {
  color: var(--text-secondary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 100%;
}

.conversation-item.unread .conversation-last-message {
  color: var(--text-primary);
  font-weight: bold;
}

.new-message-overlay {
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

.new-message-panel {
  background-color: var(--bg-primary);
  border-radius: 16px;
  width: 90%;
  max-width: 500px;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: background-color 0.3s ease;
}

.new-message-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.new-message-header h3 {
  margin: 0;
  font-size: 18px;
  color: var(--text-primary);
}

.close-button {
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

.close-button:hover {
  background-color: var(--hover-color);
}

.new-message-search {
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.new-message-search input {
  width: 100%;
  padding: 10px 15px;
  border-radius: 30px;
  border: 1px solid var(--border-color);
  background-color: var(--bg-secondary);
  color: var(--text-primary);
  transition: all 0.3s ease;
}

.new-message-search input:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 1px var(--primary-color);
}

.new-message-results {
  flex: 1;
  overflow-y: auto;
  max-height: 400px;
}

.no-results {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100px;
  color: var(--text-secondary);
}

.user-item {
  display: flex;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.user-item:hover {
  background-color: var(--hover-color);
}

.user-avatar {
  margin-right: 10px;
}

.user-avatar img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

.user-details {
  flex: 1;
}

.user-name {
  font-weight: bold;
  color: var(--text-primary);
}

.user-username {
  color: var(--text-secondary);
  font-size: 14px;
}

@media (max-width: 768px) {
  .conversations-list {
    border-radius: 0;
    border-left: none;
    border-right: none;
  }
  
  .new-message-text {
    display: none;
  }
}

@media (max-width: 480px) {
  .conversations-header {
    padding: 10px;
  }
  
  .conversations-header h2 {
    font-size: 18px;
  }
  
  .search-container {
    padding: 10px;
  }
  
  .conversation-item {
    padding: 10px;
  }
  
  .conversation-avatar img {
    width: 40px;
    height: 40px;
  }
  
  .conversation-name, .conversation-last-message {
    font-size: 14px;
  }
  
  .conversation-time {
    font-size: 12px;
  }
}
</style>
