<template>
  <div class="auth-container">
    <div class="auth-card">
      <div class="auth-header">
        <h2>Log in to Vue Twitter</h2>
      </div>
      <div class="auth-body">
        <form @submit.prevent="handleLogin">
          <div class="form-group">
            <label for="username">Username or email</label>
            <input 
              type="text" 
              id="username" 
              v-model="loginForm.username" 
              required
              :class="{ 'error': errors.username }"
            >
            <span class="error-message" v-if="errors.username">{{ errors.username }}</span>
          </div>
          <div class="form-group">
            <label for="password">Password</label>
            <div class="password-input">
              <input 
                :type="showPassword ? 'text' : 'password'" 
                id="password" 
                v-model="loginForm.password" 
                required
                :class="{ 'error': errors.password }"
              >
              <button 
                type="button" 
                class="toggle-password" 
                @click="showPassword = !showPassword"
              >
                {{ showPassword ? '🙈' : '👁️' }}
              </button>
            </div>
            <span class="error-message" v-if="errors.password">{{ errors.password }}</span>
          </div>
          <div class="form-group">
            <div class="remember-me">
              <input type="checkbox" id="remember" v-model="loginForm.remember">
              <label for="remember">Remember me</label>
            </div>
          </div>
          <div class="form-group">
            <button type="submit" class="auth-button" :disabled="isLoading">
              {{ isLoading ? 'Logging in...' : 'Log in' }}
            </button>
          </div>
          <div class="auth-links">
            <a href="#" @click.prevent="forgotPassword">Forgot password?</a>
            <a href="#" @click.prevent="$emit('switch-mode', 'register')">Sign up for Vue Twitter</a>
          </div>
        </form>
      </div>
      <div class="auth-footer" v-if="errorMessage">
        <div class="auth-error">{{ errorMessage }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      loginForm: {
        username: '',
        password: '',
        remember: false
      },
      showPassword: false,
      isLoading: false,
      errorMessage: '',
      errors: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    validateForm() {
      let isValid = true;
      this.errors = {
        username: '',
        password: ''
      };
      
      if (!this.loginForm.username.trim()) {
        this.errors.username = 'Username or email is required';
        isValid = false;
      }
      
      if (!this.loginForm.password) {
        this.errors.password = 'Password is required';
        isValid = false;
      } else if (this.loginForm.password.length < 6) {
        this.errors.password = 'Password must be at least 6 characters';
        isValid = false;
      }
      
      return isValid;
    },
    handleLogin() {
      if (!this.validateForm()) return;
      
      this.isLoading = true;
      this.errorMessage = '';
      
      // Simulate API call
      setTimeout(() => {
        // For demo purposes, we'll check for a specific username/password
        if (this.loginForm.username === 'demo' && this.loginForm.password === 'password') {
          // Login successful
          const user = {
            id: 1,
            username: 'demo',
            name: 'Demo User',
            email: 'demo@example.com',
            avatar: 'https://via.placeholder.com/50'
          };
          
          // Store user in localStorage if remember me is checked
          if (this.loginForm.remember) {
            localStorage.setItem('user', JSON.stringify(user));
          }
          
          // Emit login event to parent
          this.$emit('login-success', user);
        } else {
          // Login failed
          this.errorMessage = 'Invalid username or password';
        }
        
        this.isLoading = false;
      }, 1000);
    },
    forgotPassword() {
      alert('Password reset functionality would be implemented here');
    }
  }
}
</script>

<style scoped>
.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  padding: 20px;
  background-color: var(--bg-secondary);
}

.auth-card {
  width: 100%;
  max-width: 400px;
  background-color: var(--bg-primary);
  border-radius: 15px;
  box-shadow: 0 2px 10px var(--shadow-color);
  overflow: hidden;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.auth-header {
  padding: 20px;
  border-bottom: 1px solid var(--border-color);
  text-align: center;
}

.auth-header h2 {
  margin: 0;
  color: var(--text-primary);
  font-size: 1.5rem;
}

.auth-body {
  padding: 20px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: var(--text-primary);
  font-weight: bold;
}

.form-group input[type="text"],
.form-group input[type="password"] {
  width: 100%;
  padding: 12px;
  border: 1px solid var(--border-color);
  border-radius: 5px;
  font-size: 16px;
  background-color: var(--bg-primary);
  color: var(--text-primary);
  transition: border-color 0.3s ease, background-color 0.3s ease, color 0.3s ease;
}

.form-group input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.form-group input.error {
  border-color: #e0245e;
}

.error-message {
  display: block;
  color: #e0245e;
  font-size: 14px;
  margin-top: 5px;
}

.password-input {
  position: relative;
}

.toggle-password {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  font-size: 16px;
}

.remember-me {
  display: flex;
  align-items: center;
}

.remember-me input {
  margin-right: 8px;
}

.auth-button {
  width: 100%;
  padding: 12px;
  background-color: var(--primary-color);
  color: white;
  border: none;
  border-radius: 30px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.auth-button:hover {
  background-color: var(--primary-color-dark);
}

.auth-button:disabled {
  background-color: #9bd1f9;
  cursor: not-allowed;
}

.auth-links {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
}

.auth-links a {
  color: var(--primary-color);
  text-decoration: none;
  font-size: 14px;
  transition: color 0.3s ease;
}

.auth-links a:hover {
  text-decoration: underline;
}

.auth-footer {
  padding: 15px 20px;
  border-top: 1px solid var(--border-color);
}

.auth-error {
  color: #e0245e;
  text-align: center;
  font-weight: bold;
}
</style>
