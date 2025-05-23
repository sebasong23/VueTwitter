<template>
  <div class="auth-wrapper">
    <Login 
      v-if="mode === 'login'" 
      @switch-mode="switchMode" 
      @login-success="handleAuthSuccess"
    />
    <Register 
      v-else 
      @switch-mode="switchMode" 
      @register-success="handleAuthSuccess"
    />
  </div>
</template>

<script>
import Login from './Login.vue'
import Register from './Register.vue'

export default {
  name: 'Auth',
  components: {
    Login,
    Register
  },
  data() {
    return {
      mode: 'login'
    }
  },
  methods: {
    switchMode(newMode) {
      this.mode = newMode;
    },
    handleAuthSuccess(user) {
      this.$emit('auth-success', user);
    }
  }
}
</script>

<style scoped>
.auth-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 1000;
  background-color: var(--bg-secondary);
}
</style>
