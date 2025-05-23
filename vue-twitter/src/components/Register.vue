<template>
  <div class="auth-container">
    <div class="auth-card">
      <div class="auth-header">
        <h2>Create your account</h2>
      </div>
      <div class="auth-body">
        <form @submit.prevent="handleRegister">
          <div class="form-group">
            <label for="name">Name</label>
            <input
              type="text"
              id="name"
              v-model="registerForm.name"
              required
              :class="{ 'error': errors.name }"
            >
            <span class="error-message" v-if="errors.name">{{ errors.name }}</span>
          </div>
          <div class="form-group">
            <label for="email">Email</label>
            <input
              type="email"
              id="email"
              v-model="registerForm.email"
              required
              :class="{ 'error': errors.email }"
            >
            <span class="error-message" v-if="errors.email">{{ errors.email }}</span>
          </div>
          <div class="form-group">
            <label for="username">Username</label>
            <input
              type="text"
              id="username"
              v-model="registerForm.username"
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
                v-model="registerForm.password"
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
            <label for="confirmPassword">Confirm Password</label>
            <div class="password-input">
              <input
                :type="showConfirmPassword ? 'text' : 'password'"
                id="confirmPassword"
                v-model="registerForm.confirmPassword"
                required
                :class="{ 'error': errors.confirmPassword }"
              >
              <button
                type="button"
                class="toggle-password"
                @click="showConfirmPassword = !showConfirmPassword"
              >
                {{ showConfirmPassword ? '🙈' : '👁️' }}
              </button>
            </div>
            <span class="error-message" v-if="errors.confirmPassword">{{ errors.confirmPassword }}</span>
          </div>
          <div class="form-group">
            <div class="terms">
              <input type="checkbox" id="terms" v-model="registerForm.terms" :class="{ 'error': errors.terms }">
              <label for="terms">I agree to the <a href="#" @click.prevent="showTerms">Terms of Service</a> and <a href="#" @click.prevent="showPrivacy">Privacy Policy</a></label>
            </div>
            <span class="error-message" v-if="errors.terms">{{ errors.terms }}</span>
          </div>
          <div class="form-group">
            <button type="submit" class="auth-button" :disabled="isLoading">
              {{ isLoading ? 'Creating account...' : 'Sign up' }}
            </button>
          </div>
          <div class="auth-links">
            <a href="#" @click.prevent="$emit('switch-mode', 'login')">Already have an account? Log in</a>
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
  name: 'Register',
  data() {
    return {
      registerForm: {
        name: '',
        email: '',
        username: '',
        password: '',
        confirmPassword: '',
        terms: false
      },
      showPassword: false,
      showConfirmPassword: false,
      isLoading: false,
      errorMessage: '',
      errors: {
        name: '',
        email: '',
        username: '',
        password: '',
        confirmPassword: '',
        terms: ''
      }
    }
  },
  methods: {
    validateForm() {
      let isValid = true;
      this.errors = {
        name: '',
        email: '',
        username: '',
        password: '',
        confirmPassword: '',
        terms: ''
      };

      if (!this.registerForm.name.trim()) {
        this.errors.name = 'Name is required';
        isValid = false;
      }

      if (!this.registerForm.email.trim()) {
        this.errors.email = 'Email is required';
        isValid = false;
      } else if (!this.validateEmail(this.registerForm.email)) {
        this.errors.email = 'Please enter a valid email address';
        isValid = false;
      }

      if (!this.registerForm.username.trim()) {
        this.errors.username = 'Username is required';
        isValid = false;
      } else if (this.registerForm.username.length < 3) {
        this.errors.username = 'Username must be at least 3 characters';
        isValid = false;
      }

      if (!this.registerForm.password) {
        this.errors.password = 'Password is required';
        isValid = false;
      } else if (this.registerForm.password.length < 6) {
        this.errors.password = 'Password must be at least 6 characters';
        isValid = false;
      }

      if (!this.registerForm.confirmPassword) {
        this.errors.confirmPassword = 'Please confirm your password';
        isValid = false;
      } else if (this.registerForm.password !== this.registerForm.confirmPassword) {
        this.errors.confirmPassword = 'Passwords do not match';
        isValid = false;
      }

      if (!this.registerForm.terms) {
        this.errors.terms = 'You must agree to the Terms of Service and Privacy Policy';
        isValid = false;
      }

      return isValid;
    },
    validateEmail(email) {
      const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(String(email).toLowerCase());
    },
    handleRegister() {
      if (!this.validateForm()) return;

      this.isLoading = true;
      this.errorMessage = '';

      // Simulate API call
      setTimeout(() => {
        // For demo purposes, we'll check if the username is already taken
        if (this.registerForm.username === 'demo') {
          this.errorMessage = 'Username is already taken';
        } else {
          // Registration successful
          const user = {
            id: 2,
            username: this.registerForm.username,
            name: this.registerForm.name,
            email: this.registerForm.email,
            avatar: 'https://via.placeholder.com/50'
          };

          // Store user in localStorage
          localStorage.setItem('user', JSON.stringify(user));

          // Emit register success event to parent
          this.$emit('register-success', user);
        }

        this.isLoading = false;
      }, 1500);
    },
    showTerms() {
      alert('Terms of Service would be displayed here');
    },
    showPrivacy() {
      alert('Privacy Policy would be displayed here');
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
  margin: 0 15px;
}

@media (max-width: 480px) {
  .auth-card {
    border-radius: 0;
    max-width: 100%;
    height: 100%;
    margin: 0;
  }

  .auth-container {
    padding: 0;
  }
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
.form-group input[type="email"],
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

.terms {
  display: flex;
  align-items: flex-start;
}

.terms input {
  margin-right: 8px;
  margin-top: 4px;
}

.terms label {
  font-weight: normal;
  font-size: 14px;
}

.terms a {
  color: var(--primary-color);
  text-decoration: none;
}

.terms a:hover {
  text-decoration: underline;
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
  text-align: center;
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
