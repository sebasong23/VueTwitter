<template>
  <div class="messages-page">
    <div class="messages-container" :class="{ 'conversation-open': selectedConversationId }">
      <div class="conversations-container">
        <ConversationsList 
          :current-user-id="currentUserId"
          :selected-conversation-id="selectedConversationId"
          @select-conversation="selectConversation"
          @delete-conversation="deleteConversation"
        />
      </div>
      <div class="conversation-container" v-if="selectedConversationId">
        <Conversation 
          :conversation-id="selectedConversationId"
          :current-user-id="currentUserId"
          @back="closeConversation"
          @delete="deleteConversation"
          @view-profile="viewProfile"
        />
      </div>
      <div v-else class="empty-conversation-container">
        <div class="empty-state">
          <h3>Select a conversation</h3>
          <p>Choose an existing conversation or start a new one</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ConversationsList from './ConversationsList.vue'
import Conversation from './Conversation.vue'

export default {
  name: 'MessagesPage',
  components: {
    ConversationsList,
    Conversation
  },
  props: {
    currentUser: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      selectedConversationId: null
    }
  },
  computed: {
    currentUserId() {
      return this.currentUser ? this.currentUser.id : null;
    }
  },
  methods: {
    selectConversation(conversationId) {
      this.selectedConversationId = conversationId;
    },
    closeConversation() {
      this.selectedConversationId = null;
    },
    deleteConversation(conversationId) {
      // In a real app, we would make an API call to delete the conversation
      const savedConversations = localStorage.getItem('conversations');
      if (savedConversations) {
        try {
          let conversations = JSON.parse(savedConversations);
          conversations = conversations.filter(c => c.id !== conversationId);
          localStorage.setItem('conversations', JSON.stringify(conversations));
          
          if (this.selectedConversationId === conversationId) {
            this.selectedConversationId = null;
          }
        } catch (e) {
          console.error('Error parsing saved conversations:', e);
        }
      }
    },
    viewProfile(username) {
      this.$emit('view-profile', username);
    }
  }
}
</script>

<style scoped>
.messages-page {
  height: 100%;
}

.messages-container {
  display: grid;
  grid-template-columns: 350px 1fr;
  height: 100%;
  gap: 20px;
}

.conversations-container, .conversation-container, .empty-conversation-container {
  height: 100%;
  overflow: hidden;
}

.empty-conversation-container {
  background-color: var(--bg-primary);
  border-radius: 16px;
  border: 1px solid var(--border-color);
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.empty-state {
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

@media (max-width: 768px) {
  .messages-container {
    grid-template-columns: 1fr;
  }
  
  .conversations-container {
    display: block;
  }
  
  .conversation-container {
    display: none;
  }
  
  .empty-conversation-container {
    display: none;
  }
  
  .messages-container.conversation-open .conversations-container {
    display: none;
  }
  
  .messages-container.conversation-open .conversation-container {
    display: block;
  }
}
</style>
