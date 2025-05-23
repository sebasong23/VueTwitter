<template>
  <div class="profile-editor-overlay" @click="$emit('close')">
    <div class="profile-editor-container" @click.stop>
      <div class="editor-header">
        <div class="header-actions">
          <button class="close-btn" @click="$emit('close')">✕</button>
          <h2>Edit profile</h2>
          <button class="save-btn" @click="saveProfile" :disabled="isSaving">
            {{ isSaving ? 'Saving...' : 'Save' }}
          </button>
        </div>
      </div>
      
      <div class="editor-content">
        <div class="cover-photo-container">
          <img :src="formData.coverImage || 'https://via.placeholder.com/1500x500'" alt="Cover photo" class="cover-photo">
          <div class="cover-photo-overlay">
            <div class="photo-actions">
              <button class="photo-btn" @click="triggerCoverUpload">
                <span class="photo-icon">📷</span>
              </button>
              <button class="photo-btn" @click="removeCoverPhoto" v-if="formData.coverImage">
                <span class="photo-icon">🗑️</span>
              </button>
            </div>
          </div>
          <input 
            type="file" 
            ref="coverInput" 
            class="hidden-input" 
            accept="image/*" 
            @change="handleCoverUpload"
          >
        </div>
        
        <div class="avatar-container">
          <img :src="formData.avatar" alt="Profile avatar" class="avatar-img">
          <div class="avatar-overlay">
            <button class="photo-btn" @click="triggerAvatarUpload">
              <span class="photo-icon">📷</span>
            </button>
          </div>
          <input 
            type="file" 
            ref="avatarInput" 
            class="hidden-input" 
            accept="image/*" 
            @change="handleAvatarUpload"
          >
        </div>
        
        <div class="form-fields">
          <div class="form-group">
            <label for="name">Name</label>
            <input 
              type="text" 
              id="name" 
              v-model="formData.name" 
              maxlength="50"
              :class="{ 'error': errors.name }"
            >
            <div class="input-counter">{{ formData.name.length }}/50</div>
            <div class="error-message" v-if="errors.name">{{ errors.name }}</div>
          </div>
          
          <div class="form-group">
            <label for="bio">Bio</label>
            <textarea 
              id="bio" 
              v-model="formData.bio" 
              maxlength="160"
              :class="{ 'error': errors.bio }"
            ></textarea>
            <div class="input-counter">{{ formData.bio.length }}/160</div>
            <div class="error-message" v-if="errors.bio">{{ errors.bio }}</div>
          </div>
          
          <div class="form-group">
            <label for="location">Location</label>
            <input 
              type="text" 
              id="location" 
              v-model="formData.location" 
              maxlength="30"
              :class="{ 'error': errors.location }"
            >
            <div class="input-counter">{{ formData.location.length }}/30</div>
            <div class="error-message" v-if="errors.location">{{ errors.location }}</div>
          </div>
          
          <div class="form-group">
            <label for="website">Website</label>
            <input 
              type="text" 
              id="website" 
              v-model="formData.website" 
              maxlength="100"
              :class="{ 'error': errors.website }"
            >
            <div class="error-message" v-if="errors.website">{{ errors.website }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProfileEditor',
  props: {
    user: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      formData: {
        name: this.user.name || '',
        bio: this.user.bio || '',
        location: this.user.location || '',
        website: this.user.website || '',
        avatar: this.user.avatar || 'https://via.placeholder.com/200',
        coverImage: this.user.coverImage || ''
      },
      errors: {
        name: '',
        bio: '',
        location: '',
        website: ''
      },
      isSaving: false
    }
  },
  methods: {
    validateForm() {
      let isValid = true;
      this.errors = {
        name: '',
        bio: '',
        location: '',
        website: ''
      };
      
      if (!this.formData.name.trim()) {
        this.errors.name = 'Name is required';
        isValid = false;
      }
      
      if (this.formData.website && !this.isValidUrl(this.formData.website)) {
        this.errors.website = 'Please enter a valid URL';
        isValid = false;
      }
      
      return isValid;
    },
    isValidUrl(string) {
      try {
        new URL(string);
        return true;
      } catch (_) {
        // If the string doesn't start with http:// or https://, try adding it
        if (!string.startsWith('http://') && !string.startsWith('https://')) {
          try {
            new URL('https://' + string);
            // If valid with https://, update the formData
            this.formData.website = 'https://' + string;
            return true;
          } catch (_) {
            return false;
          }
        }
        return false;
      }
    },
    saveProfile() {
      if (!this.validateForm()) return;
      
      this.isSaving = true;
      
      // In a real app, we would make an API call to update the profile
      setTimeout(() => {
        // Emit the updated profile data to the parent component
        this.$emit('save', { ...this.formData });
        this.isSaving = false;
        this.$emit('close');
      }, 1000);
    },
    triggerCoverUpload() {
      this.$refs.coverInput.click();
    },
    triggerAvatarUpload() {
      this.$refs.avatarInput.click();
    },
    handleCoverUpload(event) {
      const file = event.target.files[0];
      if (file) {
        // In a real app, we would upload the file to a server
        // For now, we'll just create a local URL
        const reader = new FileReader();
        reader.onload = (e) => {
          this.formData.coverImage = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    handleAvatarUpload(event) {
      const file = event.target.files[0];
      if (file) {
        // In a real app, we would upload the file to a server
        // For now, we'll just create a local URL
        const reader = new FileReader();
        reader.onload = (e) => {
          this.formData.avatar = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
    removeCoverPhoto() {
      this.formData.coverImage = '';
    }
  }
}
</script>

<style scoped>
.profile-editor-overlay {
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

.profile-editor-container {
  width: 100%;
  max-width: 600px;
  max-height: 90vh;
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease;
}

.editor-header {
  padding: 10px 15px;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.header-actions {
  display: flex;
  align-items: center;
  justify-content: space-between;
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

.header-actions h2 {
  margin: 0;
  font-size: 18px;
  color: var(--text-primary);
}

.save-btn {
  background-color: var(--text-primary);
  color: var(--bg-primary);
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  font-weight: bold;
  cursor: pointer;
  transition: opacity 0.3s ease;
}

.save-btn:hover {
  opacity: 0.9;
}

.save-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.editor-content {
  overflow-y: auto;
  flex: 1;
}

.cover-photo-container {
  position: relative;
  height: 200px;
  overflow: hidden;
}

.cover-photo {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.cover-photo-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.cover-photo-container:hover .cover-photo-overlay {
  opacity: 1;
}

.photo-actions {
  display: flex;
  gap: 10px;
}

.photo-btn {
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.photo-btn:hover {
  background-color: rgba(0, 0, 0, 0.8);
}

.avatar-container {
  position: relative;
  width: 120px;
  height: 120px;
  border-radius: 50%;
  margin: -60px 0 0 20px;
  border: 4px solid var(--bg-primary);
  overflow: hidden;
  background-color: var(--bg-primary);
  transition: border-color 0.3s ease;
}

.avatar-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.avatar-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.avatar-container:hover .avatar-overlay {
  opacity: 1;
}

.hidden-input {
  display: none;
}

.form-fields {
  padding: 20px;
}

.form-group {
  margin-bottom: 20px;
  position: relative;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: var(--text-primary);
  font-weight: bold;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 12px;
  border: 1px solid var(--border-color);
  border-radius: 5px;
  font-size: 16px;
  background-color: var(--bg-primary);
  color: var(--text-primary);
  transition: border-color 0.3s ease, background-color 0.3s ease, color 0.3s ease;
}

.form-group textarea {
  height: 100px;
  resize: none;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--primary-color);
}

.form-group input.error,
.form-group textarea.error {
  border-color: #e0245e;
}

.input-counter {
  position: absolute;
  right: 10px;
  bottom: 10px;
  font-size: 12px;
  color: var(--text-secondary);
}

.error-message {
  color: #e0245e;
  font-size: 14px;
  margin-top: 5px;
}

@media (max-width: 768px) {
  .profile-editor-container {
    max-width: 100%;
    height: 100%;
    max-height: 100%;
    border-radius: 0;
  }
  
  .cover-photo-container {
    height: 150px;
  }
  
  .avatar-container {
    width: 100px;
    height: 100px;
    margin: -50px 0 0 15px;
  }
  
  .form-fields {
    padding: 15px;
  }
}

@media (max-width: 480px) {
  .editor-header {
    padding: 8px 12px;
  }
  
  .header-actions h2 {
    font-size: 16px;
  }
  
  .save-btn {
    padding: 6px 12px;
    font-size: 14px;
  }
  
  .cover-photo-container {
    height: 120px;
  }
  
  .avatar-container {
    width: 80px;
    height: 80px;
    margin: -40px 0 0 12px;
  }
  
  .form-fields {
    padding: 12px;
  }
  
  .form-group input,
  .form-group textarea {
    padding: 10px;
    font-size: 14px;
  }
}
</style>
