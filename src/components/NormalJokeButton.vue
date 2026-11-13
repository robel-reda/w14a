<template>
  <div class="normal-button">
    <button @click="$emit('normal')">
      😊 Restore Normal Joke
    </button>
  </div>
</template>

<script>
export default {
  name: 'NormalJokeButton',
  emits: ['normal']
};
</script>

<style scoped>
.normal-button button {
  background: #292640;
  color: #c4b5fd;
  border: 1px solid #65538e;
  border-radius: 10px;
  padding: 12px 22px;
  font-weight: 600;
  transition: 0.3s;
}

.normal-button button:hover {
  background: #6045a8;
  color: white;
}
</style>