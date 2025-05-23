<template>
  <div class="conversation">
    <div class="conversation-header">
      <button class="back-button" @click="$emit('back')">←</button>
      <div class="user-info" @click="viewProfile">
        <div class="user-avatar">
          <img :src="conversation.recipientAvatar" :alt="conversation.recipientName">
        </div>
        <div class="user-details">
          <div class="user-name">{{ conversation.recipientName }}</div>
          <div class="user-username">@{{ conversation.recipientUsername }}</div>
        </div>
      </div>
      <div class="conversation-actions">
        <button class="action-button" @click="showInfo = !showInfo">ℹ️</button>
      </div>
    </div>
    
    <div class="conversation-body" ref="messagesContainer">
      <div v-if="loading" class="loading-indicator">
        Loading messages...
      </div>
      <div v-else-if="messages.length === 0" class="empty-conversation">
        <p>No messages yet</p>
        <p>Send a message to start the conversation</p>
      </div>
      <div v-else class="messages-list">
        <div v-for="(message, index) in messages" :key="message.id" class="message-wrapper">
          <div v-if="showDateSeparator(index)" class="date-separator">
            {{ formatDate(message.timestamp) }}
          </div>
          <Message :message="message" :current-user-id="currentUserId" />
        </div>
      </div>
    </div>
    
    <div class="conversation-input">
      <div class="input-container">
        <button class="attachment-button" @click="triggerFileInput">📎</button>
        <input 
          type="text" 
          v-model="newMessage" 
          placeholder="Start a new message"
          @keyup.enter="sendMessage"
        >
        <button 
          class="send-button" 
          :disabled="!newMessage.trim() && !selectedFile" 
          @click="sendMessage"
        >
          📤
        </button>
      </div>
      <div v-if="selectedFile" class="file-preview">
        <img :src="filePreview" alt="Selected image">
        <button class="remove-file" @click="removeFile">✕</button>
      </div>
      <input 
        type="file" 
        ref="fileInput" 
        class="hidden-input" 
        accept="image/*" 
        @change="handleFileSelect"
      >
    </div>
    
    <div v-if="showInfo" class="conversation-info" @click="showInfo = false">
      <div class="info-panel" @click.stop>
        <div class="info-header">
          <h3>Conversation info</h3>
          <button class="close-button" @click="showInfo = false">✕</button>
        </div>
        <div class="info-content">
          <div class="user-profile">
            <img :src="conversation.recipientAvatar" :alt="conversation.recipientName" class="profile-avatar">
            <h4>{{ conversation.recipientName }}</h4>
            <p>@{{ conversation.recipientUsername }}</p>
            <button class="profile-button" @click="viewProfile">View profile</button>
          </div>
          <div class="conversation-options">
            <div class="option">
              <input type="checkbox" id="mute" v-model="muteConversation">
              <label for="mute">Mute conversation</label>
            </div>
            <div class="option">
              <input type="checkbox" id="block" v-model="blockUser">
              <label for="block">Block @{{ conversation.recipientUsername }}</label>
            </div>
            <div class="option">
              <button class="delete-button" @click="confirmDelete">Delete conversation</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <div v-if="showDeleteConfirm" class="delete-confirm" @click="showDeleteConfirm = false">
      <div class="confirm-panel" @click.stop>
        <h3>Delete conversation?</h3>
        <p>This conversation will be deleted from your inbox. Other people in the conversation will still be able to see it.</p>
        <div class="confirm-actions">
          <button class="cancel-button" @click="showDeleteConfirm = false">Cancel</button>
          <button class="delete-button" @click="deleteConversation">Delete</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from './Message.vue'

export default {
  name: 'Conversation',
  components: {
    Message
  },
  props: {
    conversationId: {
      type: [Number, String],
      required: true
    },
    currentUserId: {
      type: [Number, String],
      required: true
    }
  },
  data() {
    return {
      conversation: {},
      messages: [],
      loading: true,
      newMessage: '',
      showInfo: false,
      muteConversation: false,
      blockUser: false,
      showDeleteConfirm: false,
      selectedFile: null,
      filePreview: null
    }
  },
  methods: {
    loadConversation() {
      this.loading = true;
      
      // In a real app, we would fetch the conversation from the server
      // For now, we'll simulate an API call with setTimeout
      setTimeout(() => {
        // Check if we have saved conversations in localStorage
        const savedConversations = localStorage.getItem('conversations');
        if (savedConversations) {
          try {
            const conversations = JSON.parse(savedConversations);
            const conversation = conversations.find(c => c.id === this.conversationId);
            if (conversation) {
              this.conversation = conversation;
              this.messages = conversation.messages || [];
              this.loading = false;
              this.$nextTick(() => {
                this.scrollToBottom();
              });
              return;
            }
          } catch (e) {
            console.error('Error parsing saved conversations:', e);
          }
        }
        
        // If no saved conversation or error parsing, use mock data
        this.conversation = {
          id: this.conversationId,
          recipientId: 2,
          recipientName: 'Jane Smith',
          recipientUsername: 'janesmith',
          recipientAvatar: 'https://via.placeholder.com/50',
          lastMessage: 'Hello, how are you?',
          timestamp: new Date().toISOString(),
          unread: false
        };
        
        this.messages = [
          {
            id: 1,
            conversationId: this.conversationId,
            senderId: this.currentUserId,
            senderName: 'Current User',
            senderAvatar: 'https://via.placeholder.com/50',
            text: 'Hi Jane, I saw your tweet about Vue.js!',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 24).toISOString(), // 1 day ago
            status: 'read'
          },
          {
            id: 2,
            conversationId: this.conversationId,
            senderId: 2,
            senderName: 'Jane Smith',
            senderAvatar: 'https://via.placeholder.com/50',
            text: 'Yes! I\'m really enjoying working with Vue. Have you tried the Composition API yet?',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 23).toISOString(), // 23 hours ago
            status: 'read'
          },
          {
            id: 3,
            conversationId: this.conversationId,
            senderId: this.currentUserId,
            senderName: 'Current User',
            senderAvatar: 'https://via.placeholder.com/50',
            text: 'I\'ve been experimenting with it. It\'s a game changer for complex components!',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 22).toISOString(), // 22 hours ago
            status: 'read'
          },
          {
            id: 4,
            conversationId: this.conversationId,
            senderId: 2,
            senderName: 'Jane Smith',
            senderAvatar: 'https://via.placeholder.com/50',
            text: 'Absolutely! Here\'s a screenshot of a component I refactored using the Composition API.',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 2).toISOString(), // 2 hours ago
            status: 'read'
          },
          {
            id: 5,
            conversationId: this.conversationId,
            senderId: 2,
            senderName: 'Jane Smith',
            senderAvatar: 'https://via.placeholder.com/50',
            image: 'https://via.placeholder.com/600/92c952',
            timestamp: new Date(Date.now() - 1000 * 60 * 60 * 2).toISOString(), // 2 hours ago
            status: 'read'
          },
          {
            id: 6,
            conversationId: this.conversationId,
            senderId: this.currentUserId,
            senderName: 'Current User',
            senderAvatar: 'https://via.placeholder.com/50',
            text: 'That looks great! I\'ll have to try that approach in my project.',
            timestamp: new Date(Date.now() - 1000 * 60 * 30).toISOString(), // 30 minutes ago
            status: 'delivered'
          }
        ];
        
        this.loading = false;
        this.$nextTick(() => {
          this.scrollToBottom();
        });
        
        // Save to localStorage
        this.saveConversation();
      }, 1000);
    },
    sendMessage() {
      if (!this.newMessage.trim() && !this.selectedFile) return;
      
      const newMessage = {
        id: Date.now(),
        conversationId: this.conversationId,
        senderId: this.currentUserId,
        senderName: 'Current User',
        senderAvatar: 'https://via.placeholder.com/50',
        text: this.newMessage.trim() || null,
        image: this.filePreview || null,
        timestamp: new Date().toISOString(),
        status: 'sent'
      };
      
      this.messages.push(newMessage);
      this.newMessage = '';
      this.selectedFile = null;
      this.filePreview = null;
      
      this.$nextTick(() => {
        this.scrollToBottom();
      });
      
      // Update conversation last message
      this.conversation.lastMessage = newMessage.text || 'Sent an image';
      this.conversation.timestamp = newMessage.timestamp;
      
      // Save to localStorage
      this.saveConversation();
      
      // Simulate message status updates
      setTimeout(() => {
        const index = this.messages.findIndex(m => m.id === newMessage.id);
        if (index !== -1) {
          this.messages[index].status = 'delivered';
          this.saveConversation();
        }
      }, 2000);
      
      setTimeout(() => {
        const index = this.messages.findIndex(m => m.id === newMessage.id);
        if (index !== -1) {
          this.messages[index].status = 'read';
          this.saveConversation();
        }
      }, 5000);
    },
    scrollToBottom() {
      const container = this.$refs.messagesContainer;
      if (container) {
        container.scrollTop = container.scrollHeight;
      }
    },
    formatDate(timestamp) {
      const date = new Date(timestamp);
      const today = new Date();
      const yesterday = new Date(today);
      yesterday.setDate(yesterday.getDate() - 1);
      
      if (date.toDateString() === today.toDateString()) {
        return 'Today';
      } else if (date.toDateString() === yesterday.toDateString()) {
        return 'Yesterday';
      } else {
        return date.toLocaleDateString(undefined, { month: 'short', day: 'numeric', year: 'numeric' });
      }
    },
    showDateSeparator(index) {
      if (index === 0) return true;
      
      const currentDate = new Date(this.messages[index].timestamp).toDateString();
      const previousDate = new Date(this.messages[index - 1].timestamp).toDateString();
      
      return currentDate !== previousDate;
    },
    viewProfile() {
      this.$emit('view-profile', this.conversation.recipientUsername);
    },
    confirmDelete() {
      this.showDeleteConfirm = true;
      this.showInfo = false;
    },
    deleteConversation() {
      // In a real app, we would make an API call to delete the conversation
      this.$emit('delete', this.conversationId);
      this.showDeleteConfirm = false;
    },
    saveConversation() {
      // In a real app, we would save to the server
      // For now, we'll just save to localStorage
      const savedConversations = localStorage.getItem('conversations');
      let conversations = [];
      
      if (savedConversations) {
        try {
          conversations = JSON.parse(savedConversations);
          const index = conversations.findIndex(c => c.id === this.conversationId);
          if (index !== -1) {
            conversations[index] = {
              ...this.conversation,
              messages: this.messages
            };
          } else {
            conversations.push({
              ...this.conversation,
              messages: this.messages
            });
          }
        } catch (e) {
          console.error('Error parsing saved conversations:', e);
          conversations = [{
            ...this.conversation,
            messages: this.messages
          }];
        }
      } else {
        conversations = [{
          ...this.conversation,
          messages: this.messages
        }];
      }
      
      localStorage.setItem('conversations', JSON.stringify(conversations));
    },
    triggerFileInput() {
      this.$refs.fileInput.click();
    },
    handleFileSelect(event) {
      const file = event.target.files[0];
      if (file) {
        this.selectedFile = file;
        
        // Create a preview
        const reader = new FileReader();
        reader.onload = (e) => {
          this.filePreview = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    removeFile() {
      this.selectedFile = null;
      this.filePreview = null;
      this.$refs.fileInput.value = '';
    }
  },
  mounted() {
    this.loadConversation();
  },
  watch: {
    conversationId() {
      this.loadConversation();
    }
  }
}
</script>

<style scoped>
.conversation {
  display: flex;
  flex-direction: column;
  height: 100%;
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border-color);
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.conversation-header {
  display: flex;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.back-button {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: var(--text-primary);
  margin-right: 15px;
  padding: 5px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.3s ease;
}

.back-button:hover {
  background-color: var(--hover-color);
}

.user-info {
  display: flex;
  align-items: center;
  flex: 1;
  cursor: pointer;
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
  display: flex;
  flex-direction: column;
}

.user-name {
  font-weight: bold;
  color: var(--text-primary);
}

.user-username {
  color: var(--text-secondary);
  font-size: 14px;
}

.conversation-actions {
  display: flex;
}

.action-button {
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

.action-button:hover {
  background-color: var(--hover-color);
}

.conversation-body {
  flex: 1;
  overflow-y: auto;
  padding: 15px;
  background-color: var(--bg-primary);
  transition: background-color 0.3s ease;
}

.loading-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  color: var(--text-secondary);
}

.empty-conversation {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
  color: var(--text-secondary);
  text-align: center;
}

.date-separator {
  text-align: center;
  margin: 20px 0;
  color: var(--text-secondary);
  font-size: 14px;
  position: relative;
}

.date-separator::before,
.date-separator::after {
  content: '';
  position: absolute;
  top: 50%;
  width: 30%;
  height: 1px;
  background-color: var(--border-color);
}

.date-separator::before {
  left: 0;
}

.date-separator::after {
  right: 0;
}

.conversation-input {
  padding: 15px;
  border-top: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.input-container {
  display: flex;
  align-items: center;
  background-color: var(--bg-secondary);
  border-radius: 20px;
  padding: 5px 10px;
  transition: background-color 0.3s ease;
}

.attachment-button, .send-button {
  background: none;
  border: none;
  font-size: 18px;
  cursor: pointer;
  color: var(--primary-color);
  padding: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: opacity 0.3s ease;
}

.send-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.input-container input {
  flex: 1;
  border: none;
  background: none;
  padding: 8px;
  font-size: 16px;
  color: var(--text-primary);
  outline: none;
}

.file-preview {
  margin-top: 10px;
  position: relative;
  display: inline-block;
}

.file-preview img {
  max-width: 100px;
  max-height: 100px;
  border-radius: 10px;
}

.remove-file {
  position: absolute;
  top: -5px;
  right: -5px;
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  border: none;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  font-size: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.hidden-input {
  display: none;
}

.conversation-info {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
  display: flex;
  justify-content: flex-end;
}

.info-panel {
  background-color: var(--bg-primary);
  width: 300px;
  height: 100%;
  overflow-y: auto;
  transition: background-color 0.3s ease;
  animation: slideIn 0.3s forwards;
}

@keyframes slideIn {
  from { transform: translateX(100%); }
  to { transform: translateX(0); }
}

.info-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.info-header h3 {
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

.info-content {
  padding: 15px;
}

.user-profile {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.profile-avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  margin-bottom: 10px;
  object-fit: cover;
}

.user-profile h4 {
  margin: 0 0 5px 0;
  color: var(--text-primary);
}

.user-profile p {
  margin: 0 0 15px 0;
  color: var(--text-secondary);
}

.profile-button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.profile-button:hover {
  background-color: var(--primary-color-dark);
}

.conversation-options {
  margin-top: 20px;
}

.option {
  margin-bottom: 15px;
  display: flex;
  align-items: center;
}

.option input[type="checkbox"] {
  margin-right: 10px;
}

.option label {
  color: var(--text-primary);
}

.delete-button {
  background-color: #e0245e;
  color: white;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
  width: 100%;
}

.delete-button:hover {
  background-color: #c01e4e;
}

.delete-confirm {
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

.confirm-panel {
  background-color: var(--bg-primary);
  border-radius: 16px;
  padding: 20px;
  width: 90%;
  max-width: 400px;
  transition: background-color 0.3s ease;
}

.confirm-panel h3 {
  margin: 0 0 10px 0;
  color: var(--text-primary);
}

.confirm-panel p {
  margin: 0 0 20px 0;
  color: var(--text-secondary);
}

.confirm-actions {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}

.cancel-button {
  background-color: transparent;
  color: var(--primary-color);
  border: 1px solid var(--primary-color);
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.cancel-button:hover {
  background-color: rgba(29, 161, 242, 0.1);
}

@media (max-width: 768px) {
  .conversation {
    border-radius: 0;
    border-left: none;
    border-right: none;
  }
  
  .info-panel {
    width: 100%;
  }
}

@media (max-width: 480px) {
  .conversation-header {
    padding: 10px;
  }
  
  .user-avatar img {
    width: 30px;
    height: 30px;
  }
  
  .user-name {
    font-size: 14px;
  }
  
  .user-username {
    font-size: 12px;
  }
  
  .conversation-body {
    padding: 10px;
  }
  
  .conversation-input {
    padding: 10px;
  }
  
  .input-container input {
    font-size: 14px;
  }
}
</style>
