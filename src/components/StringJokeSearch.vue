<template>
  <div class="search-box">
    <h3>🔎 Find a Joke</h3>

    <p>Enter a word and discover a joke containing it.</p>

    <div class="search-form">
      <input
        v-model="searchText"
        type="text"
        placeholder="Example: computer, dog, cat..."
        @keyup.enter="submitSearch"
      />

      <button @click="submitSearch">
        Search
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'StringJokeSearch',

  emits: ['search'],

  data() {
    return {
      searchText: ''
    };
  },

  methods: {
    submitSearch() {
      const text = this.searchText.trim();

      if (!text) {
        alert('Please enter a word first.');
        return;
      }

      this.$emit('search', text);
    }
  }
};
</script>

<style scoped>
.search-box {
  text-align: center;
}

.search-form {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

.search-form input {
  flex: 1;
  min-width: 0;
}

.search-form button {
  cursor: pointer;
}

@media (max-width: 500px) {
  .search-form {
    flex-direction: column;
  }
}
</style>