<template>
  <div class="profile-page">
    <div class="profile-header">
      <div class="profile-cover">
        <img :src="user.coverImage || 'https://via.placeholder.com/1500x500'" alt="Cover image">
      </div>
      <div class="profile-info">
        <div class="profile-avatar">
          <img :src="user.avatar" :alt="user.name">
        </div>
        <div class="profile-actions">
          <button v-if="isCurrentUser" class="edit-profile-btn" @click="$emit('edit-profile')">Edit profile</button>
          <button v-else class="follow-btn" :class="{ 'following': isFollowing }" @click="toggleFollow">
            {{ isFollowing ? 'Following' : 'Follow' }}
          </button>
        </div>
      </div>
      <div class="profile-details">
        <h1 class="profile-name">{{ user.name }}</h1>
        <div class="profile-username">@{{ user.username }}</div>
        <div class="profile-bio" v-if="user.bio">{{ user.bio }}</div>
        <div class="profile-meta">
          <div v-if="user.location" class="profile-location">
            <span class="meta-icon">📍</span>
            <span>{{ user.location }}</span>
          </div>
          <div v-if="user.website" class="profile-website">
            <span class="meta-icon">🔗</span>
            <a :href="user.website" target="_blank" rel="noopener noreferrer">{{ formatWebsite(user.website) }}</a>
          </div>
          <div v-if="user.joinDate" class="profile-join-date">
            <span class="meta-icon">📅</span>
            <span>Joined {{ formatDate(user.joinDate) }}</span>
          </div>
        </div>
        <div class="profile-stats">
          <div class="stat-item">
            <span class="stat-count">{{ user.following || 0 }}</span>
            <span class="stat-label">Following</span>
          </div>
          <div class="stat-item">
            <span class="stat-count">{{ user.followers || 0 }}</span>
            <span class="stat-label">Followers</span>
          </div>
        </div>
      </div>
    </div>

    <div class="profile-tabs">
      <div
        class="profile-tab"
        :class="{ active: activeTab === 'tweets' }"
        @click="setActiveTab('tweets')"
      >
        Tweets
      </div>
      <div
        class="profile-tab"
        :class="{ active: activeTab === 'replies' }"
        @click="setActiveTab('replies')"
      >
        Tweets & Replies
      </div>
      <div
        class="profile-tab"
        :class="{ active: activeTab === 'media' }"
        @click="setActiveTab('media')"
      >
        Media
      </div>
      <div
        class="profile-tab"
        :class="{ active: activeTab === 'likes' }"
        @click="setActiveTab('likes')"
      >
        Likes
      </div>
    </div>

    <div class="profile-content">
      <div v-if="loading" class="loading-indicator">
        Loading...
      </div>
      <div v-else-if="filteredTweets.length === 0" class="no-tweets">
        <div v-if="activeTab === 'tweets'">
          <h3>No tweets yet</h3>
          <p>When this user posts tweets, they'll show up here.</p>
        </div>
        <div v-else-if="activeTab === 'replies'">
          <h3>No replies yet</h3>
          <p>When this user replies to tweets, they'll show up here.</p>
        </div>
        <div v-else-if="activeTab === 'media'">
          <h3>No media yet</h3>
          <p>When this user posts tweets with media, they'll show up here.</p>
        </div>
        <div v-else-if="activeTab === 'likes'">
          <h3>No likes yet</h3>
          <p>When this user likes tweets, they'll show up here.</p>
        </div>
      </div>
      <div v-else class="tweets-container">
        <Tweet
          v-for="tweet in filteredTweets"
          :key="tweet.id"
          :tweet="tweet"
          @reply="handleReply"
          @share="handleShare"
          @open-media="handleMediaView"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Tweet from './Tweet.vue'

export default {
  name: 'ProfilePage',
  components: {
    Tweet
  },
  props: {
    username: {
      type: String,
      required: true
    },
    currentUser: {
      type: Object,
      default: null
    }
  },
  data() {
    return {
      user: null,
      tweets: [],
      activeTab: 'tweets',
      loading: true,
      isFollowing: false
    }
  },
  computed: {
    isCurrentUser() {
      return this.currentUser && this.user && this.currentUser.username === this.user.username;
    },
    filteredTweets() {
      if (!this.tweets.length) return [];

      switch (this.activeTab) {
        case 'tweets':
          return this.tweets.filter(tweet => !tweet.isReply);
        case 'replies':
          return this.tweets.filter(tweet => tweet.isReply);
        case 'media':
          return this.tweets.filter(tweet => tweet.media && tweet.media.length > 0);
        case 'likes':
          return this.tweets.filter(tweet => tweet.isLiked);
        default:
          return this.tweets;
      }
    }
  },
  methods: {
    setActiveTab(tab) {
      this.activeTab = tab;
    },
    toggleFollow() {
      this.isFollowing = !this.isFollowing;

      // In a real app, we would make an API call to follow/unfollow the user
      if (this.isFollowing) {
        this.user.followers++;
      } else {
        this.user.followers--;
      }
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      const options = { month: 'long', year: 'numeric' };
      return date.toLocaleDateString(undefined, options);
    },
    formatWebsite(url) {
      // Remove http:// or https:// and trailing slash for display
      return url.replace(/^https?:\/\//, '').replace(/\/$/, '');
    },
    handleReply(tweet) {
      // In a real app, this would open a reply dialog
      console.log('Reply to tweet:', tweet.id);
    },
    handleShare(tweet) {
      // In a real app, this would open a share dialog
      console.log('Share tweet:', tweet.id);
    },
    handleMediaView(data) {
      // In a real app, this would open a media viewer
      console.log('View media:', data.tweet.id, 'index:', data.index);
    },
    fetchUserProfile() {
      this.loading = true;

      // In a real app, we would make an API call to fetch the user profile
      // For now, we'll simulate an API call with setTimeout
      setTimeout(() => {
        // Mock user data
        this.user = {
          id: 1,
          name: 'John Doe',
          username: this.username,
          bio: 'Frontend developer passionate about Vue.js and web technologies. Building the future of the web one component at a time.',
          avatar: 'https://via.placeholder.com/200',
          coverImage: 'https://via.placeholder.com/1500x500',
          location: 'San Francisco, CA',
          website: 'https://johndoe.dev',
          joinDate: '2020-01-15',
          following: 245,
          followers: 187
        };

        // Mock tweets data
        this.tweets = [
          {
            id: 1,
            name: this.user.name,
            username: this.user.username,
            avatar: this.user.avatar,
            text: 'Just launched my new Vue.js project! Check it out and let me know what you think. #vuejs #webdev',
            time: '2h',
            replies: 5,
            retweets: 10,
            likes: 25,
            isReply: false,
            isLiked: false,
            media: [
              'https://via.placeholder.com/600/92c952'
            ]
          },
          {
            id: 2,
            name: this.user.name,
            username: this.user.username,
            avatar: this.user.avatar,
            text: 'Working on a new feature for my Vue Twitter clone. Stay tuned for updates!',
            time: '1d',
            replies: 2,
            retweets: 5,
            likes: 15,
            isReply: false,
            isLiked: true
          },
          {
            id: 3,
            name: this.user.name,
            username: this.user.username,
            avatar: this.user.avatar,
            text: '@vuejs The new Composition API is amazing! It makes complex component logic so much cleaner.',
            time: '2d',
            replies: 1,
            retweets: 3,
            likes: 8,
            isReply: true,
            isLiked: false
          },
          {
            id: 4,
            name: this.user.name,
            username: this.user.username,
            avatar: this.user.avatar,
            text: 'Here are some screenshots of my latest project. Built with Vue.js and Tailwind CSS.',
            time: '3d',
            replies: 7,
            retweets: 12,
            likes: 35,
            isReply: false,
            isLiked: true,
            media: [
              'https://via.placeholder.com/600/771796',
              'https://via.placeholder.com/600/24f355',
              'https://via.placeholder.com/600/d32776'
            ]
          }
        ];

        this.loading = false;
      }, 1000);
    }
  },
  mounted() {
    this.fetchUserProfile();
  },
  watch: {
    username(newUsername) {
      if (newUsername) {
        this.fetchUserProfile();
      }
    }
  }
}
</script>

<style scoped>
.profile-page {
  background-color: var(--bg-primary);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--border-color);
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.profile-header {
  position: relative;
}

.profile-cover {
  height: 200px;
  overflow: hidden;
}

.profile-cover img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.profile-info {
  display: flex;
  justify-content: space-between;
  padding: 0 20px;
  margin-top: -40px;
}

.profile-avatar {
  width: 134px;
  height: 134px;
  border-radius: 50%;
  border: 4px solid var(--bg-primary);
  overflow: hidden;
  background-color: var(--bg-primary);
  transition: border-color 0.3s ease;
}

.profile-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.profile-actions {
  margin-top: 20px;
}

.edit-profile-btn, .follow-btn {
  padding: 8px 16px;
  border-radius: 30px;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
}

.edit-profile-btn {
  background-color: transparent;
  color: var(--text-primary);
  border: 1px solid var(--border-color);
}

.edit-profile-btn:hover {
  background-color: var(--hover-color);
}

.follow-btn {
  background-color: var(--text-primary);
  color: var(--bg-primary);
  border: none;
}

.follow-btn:hover {
  opacity: 0.9;
}

.follow-btn.following {
  background-color: transparent;
  color: var(--text-primary);
  border: 1px solid var(--border-color);
}

.profile-details {
  padding: 20px;
}

.profile-name {
  font-size: 20px;
  font-weight: bold;
  margin: 0;
  color: var(--text-primary);
}

.profile-username {
  font-size: 15px;
  color: var(--text-secondary);
  margin-bottom: 12px;
}

.profile-bio {
  margin-bottom: 12px;
  line-height: 1.4;
  color: var(--text-primary);
}

.profile-meta {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 12px;
  color: var(--text-secondary);
}

.profile-location, .profile-website, .profile-join-date {
  display: flex;
  align-items: center;
}

.meta-icon {
  margin-right: 4px;
}

.profile-website a {
  color: var(--primary-color);
  text-decoration: none;
}

.profile-website a:hover {
  text-decoration: underline;
}

.profile-stats {
  display: flex;
  gap: 20px;
}

.stat-item {
  display: flex;
  align-items: center;
}

.stat-count {
  font-weight: bold;
  color: var(--text-primary);
  margin-right: 4px;
}

.stat-label {
  color: var(--text-secondary);
}

.profile-tabs {
  display: flex;
  border-bottom: 1px solid var(--border-color);
  transition: border-color 0.3s ease;
}

.profile-tab {
  flex: 1;
  text-align: center;
  padding: 15px 0;
  font-weight: bold;
  color: var(--text-secondary);
  cursor: pointer;
  transition: color 0.3s ease, background-color 0.3s ease;
  position: relative;
}

.profile-tab:hover {
  background-color: var(--hover-color);
  color: var(--primary-color);
}

.profile-tab.active {
  color: var(--primary-color);
}

.profile-tab.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 4px;
  background-color: var(--primary-color);
  border-radius: 4px 4px 0 0;
}

.profile-content {
  min-height: 300px;
}

.loading-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px;
  color: var(--text-secondary);
}

.no-tweets {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 300px;
  text-align: center;
  padding: 0 20px;
}

.no-tweets h3 {
  color: var(--text-primary);
  margin-bottom: 8px;
}

.no-tweets p {
  color: var(--text-secondary);
}

@media (max-width: 768px) {
  .profile-page {
    border-radius: 0;
    border-left: none;
    border-right: none;
  }

  .profile-cover {
    height: 150px;
  }

  .profile-info {
    padding: 0 15px;
  }

  .profile-avatar {
    width: 100px;
    height: 100px;
  }

  .profile-details {
    padding: 15px;
  }

  .profile-meta {
    flex-direction: column;
    gap: 8px;
  }

  .profile-tab {
    font-size: 14px;
    padding: 12px 0;
  }
}

@media (max-width: 480px) {
  .profile-cover {
    height: 120px;
  }

  .profile-avatar {
    width: 80px;
    height: 80px;
  }

  .profile-actions {
    margin-top: 10px;
  }

  .edit-profile-btn, .follow-btn {
    padding: 6px 12px;
    font-size: 14px;
  }

  .profile-details {
    padding: 10px;
  }

  .profile-name {
    font-size: 18px;
  }

  .profile-username {
    font-size: 14px;
  }

  .profile-bio {
    font-size: 14px;
  }

  .profile-tab {
    font-size: 12px;
    padding: 10px 0;
  }
}
</style>
