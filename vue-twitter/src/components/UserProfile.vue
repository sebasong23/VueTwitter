<template>
  <div class="user-profile">
    <div class="user-avatar">
      <img :src="user.avatar" :alt="user.name">
    </div>
    <div class="user-info">
      <div class="user-name">{{ user.name }}</div>
      <div class="user-username">@{{ user.username }}</div>
    </div>
    <div class="user-actions">
      <button class="logout-btn" @click="logout">Logout</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UserProfile',
  props: {
    user: {
      type: Object,
      required: true
    }
  },
  methods: {
    logout() {
      // Remove user from localStorage
      localStorage.removeItem('user');
      
      // Emit logout event to parent
      this.$emit('logout');
    }
  }
}
</script>

<style scoped>
.user-profile {
  display: flex;
  align-items: center;
  padding: 10px;
  border-radius: 10px;
  transition: background-color 0.3s ease;
  cursor: pointer;
}

.user-profile:hover {
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

.user-info {
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

.user-actions {
  margin-left: 10px;
}

.logout-btn {
  background-color: transparent;
  color: var(--text-secondary);
  border: 1px solid var(--border-color);
  border-radius: 20px;
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.3s ease, color 0.3s ease, border-color 0.3s ease;
}

.logout-btn:hover {
  background-color: rgba(244, 33, 46, 0.1);
  color: #e0245e;
  border-color: rgba(244, 33, 46, 0.1);
}
</style>
