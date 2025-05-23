<template>
  <div class="notification-badge" v-if="count > 0">
    {{ formattedCount }}
  </div>
</template>

<script>
export default {
  name: 'NotificationBadge',
  props: {
    count: {
      type: Number,
      default: 0
    }
  },
  computed: {
    formattedCount() {
      return this.count > 99 ? '99+' : this.count;
    }
  }
}
</script>

<style scoped>
.notification-badge {
  background-color: var(--primary-color);
  color: white;
  border-radius: 50%;
  min-width: 18px;
  height: 18px;
  font-size: 12px;
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 4px;
  position: absolute;
  top: -5px;
  right: -5px;
}
</style>
