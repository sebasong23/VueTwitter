<template>
  <header class="twitter-header">
    <div class="logo">
      <h1>Vue Twitter</h1>
    </div>
    <nav class="main-nav">
      <ul>
        <li><a href="#" class="active">Home</a></li>
        <li><a href="#">Explore</a></li>
        <li><a href="#">Notifications</a></li>
        <li><a href="#">Messages</a></li>
        <li><a href="#">Profile</a></li>
      </ul>
    </nav>
    <div class="user-actions">
      <button class="tweet-btn">Tweet</button>
    </div>
  </header>
</template>

<script>
export default {
  name: 'TwitterHeader'
}
</script>

<style scoped>
.twitter-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  border-bottom: 1px solid #e1e8ed;
  background-color: #ffffff;
}

.logo h1 {
  color: #1da1f2;
  font-size: 1.5rem;
  margin: 0;
}

.main-nav ul {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
}

.main-nav li {
  margin: 0 15px;
}

.main-nav a {
  text-decoration: none;
  color: #657786;
  font-weight: bold;
  padding: 10px 0;
}

.main-nav a.active {
  color: #1da1f2;
  border-bottom: 2px solid #1da1f2;
}

.main-nav a:hover {
  color: #1da1f2;
}

.tweet-btn {
  background-color: #1da1f2;
  color: white;
  border: none;
  border-radius: 30px;
  padding: 10px 20px;
  font-weight: bold;
  cursor: pointer;
}

.tweet-btn:hover {
  background-color: #0c8bd9;
}
</style>
