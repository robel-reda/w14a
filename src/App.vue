<template>
  <div class="app">
    <header class="header">
      <div class="logo">😂 Joke<span>Box</span></div>
      <div class="badge">YOUR DAILY DOSE OF HUMOUR</div>
    </header>

    <main class="container">
      <section class="hero">
        <div class="hero-icon">🎭</div>
        <h1>Life is better<br /><span>with laughter.</span></h1>
        <p>Discover random jokes, change their style, and share a laugh.</p>
      </section>

      <section class="joke-card">
        <div class="card-top">
          <span class="label">TODAY'S RANDOM JOKE</span>
          <span class="live"><span></span> LIVE</span>
        </div>

        <div v-if="loading" class="loading">
          <div class="spinner"></div>
          <p>Finding something funny...</p>
        </div>

        <div v-else-if="error" class="error">
          {{ error }}
        </div>

        <div v-else :key="jokeId" class="joke-content">
          <div class="joke-emoji">🤣😂🤣</div>

          <p v-if="displayJoke.type === 'single'" class="joke-text">
            {{ displayJoke.joke }}
          </p>

          <div v-else-if="displayJoke.type === 'twopart'">
            <p class="joke-text">{{ displayJoke.setup }}</p>
            <div class="divider"></div>
            <p class="joke-answer">{{ displayJoke.delivery }}</p>
          </div>
        </div>

        <button class="new-joke" @click="getNewJoke" :disabled="loading">
          <span>↻</span> {{ loading ? 'Loading...' : 'Get New Joke' }}
        </button>
      </section>

      <section class="style-section">
        <div class="section-heading">
          <h2>Choose Your Style</h2>
          <p>Give your joke a different personality.</p>
        </div>

        <div class="style-buttons">
          <button
            :class="{ active: styleType === 'normal' }"
            @click="changeStyle('normal')"
          >
            😊 <span>Normal</span>
          </button>

          <button
            :class="{ active: styleType === 'loud' }"
            @click="changeStyle('loud')"
          >
            📢 <span>Loud</span>
          </button>

          <button
            :class="{ active: styleType === 'snake' }"
            @click="changeStyle('snake')"
          >
            🐍 <span>Snake</span>
          </button>
        </div>
      </section>

      <section class="extra-section">
        <NormalJokeButton @normal="changeStyle('normal')" />

        <TwoPartJoke
          :joke="originalJoke"
          :styleType="styleType"
        />
      </section>

      <StringJokeSearch @search="searchJoke" />

      <section v-if="searchResult" class="search-result">
        <div class="card-top">
          <span class="label">SEARCH RESULT</span>
        </div>

        <p v-if="searchResult.type === 'single'">
          {{ searchResult.joke }}
        </p>

        <div v-else-if="searchResult.type === 'twopart'">
          <p>{{ searchResult.setup }}</p>
          <p>{{ searchResult.delivery }}</p>
        </div>
      </section>
    </main>

    <footer>
      <h3>JokeBox 😂</h3>
      <p>Built with Vue.js, Axios and JokeAPI.</p>
      <p class="copyright">© 2026 JokeBox. Made for laughter.</p>
    </footer>
  </div>
</template>

<script>
import axios from 'axios';

import NormalJokeButton from './components/NormalJokeButton.vue';
import TwoPartJoke from './components/TwoPartJoke.vue';
import StringJokeSearch from './components/StringJokeSearch.vue';

export default {
  name: 'App',

  components: {
    NormalJokeButton,
    TwoPartJoke,
    StringJokeSearch
  },

  data() {
    return {
      originalJoke: {},
      displayJoke: {},
      searchResult: null,
      styleType: 'normal',
      loading: false,
      error: '',
      jokeId: 0
    };
  },

  mounted() {
    this.getNewJoke();
  },

  methods: {
    async getNewJoke() {
      this.loading = true;
      this.error = '';
      this.searchResult = null;

      try {
        const response = await axios.get(
          'https://v2.jokeapi.dev/joke/Any'
        );

        this.originalJoke = response.data;
        this.styleType = 'normal';
        this.jokeId++;

        this.updateDisplay();

      } catch (error) {
        this.error = 'Unable to load a joke. Please try again.';
        console.error(error);

      } finally {
        this.loading = false;
      }
    },

    changeStyle(style) {
      this.styleType = style;
      this.updateDisplay();
    },

    updateDisplay() {
      const joke = { ...this.originalJoke };

      if (joke.type === 'single') {
        joke.joke = this.formatText(joke.joke);
      }

      if (joke.type === 'twopart') {
        joke.setup = this.formatText(joke.setup);
        joke.delivery = this.formatText(joke.delivery);
      }

      this.displayJoke = joke;
    },

    formatText(text) {
      if (this.styleType === 'loud') {
        return text.toUpperCase();
      }

      if (this.styleType === 'snake') {
        return text.replaceAll(' ', '_');
      }

      return text;
    },

    async searchJoke(searchText) {
      this.error = '';
      this.searchResult = null;

      try {
        const response = await axios.get(
          'https://v2.jokeapi.dev/joke/Any',
          {
            params: {
              contains: searchText
            }
          }
        );

        if (response.data.error) {
          this.error = 'No matching joke found. Try another word.';
          return;
        }

        this.searchResult = response.data;

      } catch (error) {
        this.error = 'Search failed. Please try again.';
        console.error(error);
      }
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700;800&family=Outfit:wght@400;500;600;700;800&display=swap');

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #10101c;
  color: #ffffff;
  font-family: 'DM Sans', sans-serif;
}

button {
  font-family: inherit;
  cursor: pointer;
}

.app {
  min-height: 100vh;
  background:
    radial-gradient(circle at 50% 5%, #29204b 0%, transparent 35%),
    #10101c;
}

.header {
  max-width: 1100px;
  margin: auto;
  padding: 25px 25px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-family: 'Outfit', sans-serif;
  font-size: 27px;
  font-weight: 800;
}

.logo span {
  color: #a78bfa;
}

.badge {
  font-size: 10px;
  letter-spacing: 2px;
  color: #a78bfa;
  border: 1px solid #493b70;
  padding: 9px 15px;
  border-radius: 30px;
}

.container {
  max-width: 760px;
  margin: auto;
  padding: 40px 20px 80px;
}

.hero {
  text-align: center;
  margin-bottom: 45px;
}

.hero-icon {
  font-size: 45px;
  margin-bottom: 15px;
}

.hero h1 {
  font-family: 'Outfit', sans-serif;
  font-size: 55px;
  line-height: 1.1;
  margin: 0;
}

.hero h1 span {
  color: #a78bfa;
}

.hero p {
  color: #9996b5;
  font-size: 16px;
  margin-top: 18px;
}

.joke-card {
  background: linear-gradient(145deg, #24223b, #19182c);
  border: 1px solid #41365f;
  border-radius: 22px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 20px 60px rgba(0,0,0,0.3);
}

.card-top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.label {
  font-size: 11px;
  color: #aaa2c8;
  letter-spacing: 2px;
  font-weight: 700;
}

.live {
  font-size: 10px;
  color: #64e6ad;
}

.live span {
  display: inline-block;
  width: 7px;
  height: 7px;
  background: #64e6ad;
  border-radius: 50%;
  margin-right: 5px;
}

.joke-content {
  padding: 35px 10px;
  animation: appear 0.5s ease;
}

@keyframes appear {
  from {
    opacity: 0;
    transform: translateY(12px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.joke-emoji {
  font-size: 42px;
  margin-bottom: 15px;
}

.joke-text {
  font-family: 'Outfit', sans-serif;
  font-size: 23px;
  line-height: 1.6;
  font-weight: 600;
}

.joke-answer {
  font-size: 19px;
  color: #c4b5fd;
  line-height: 1.6;
}

.divider {
  width: 45px;
  height: 3px;
  background: #a78bfa;
  margin: 22px auto;
  border-radius: 5px;
}

.new-joke {
  background: linear-gradient(100deg, #8b5cf6, #6366f1);
  color: white;
  border: none;
  padding: 15px 32px;
  border-radius: 12px;
  font-size: 15px;
  font-weight: 700;
  transition: 0.3s;
}

.new-joke:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 25px #8b5cf655;
}

.new-joke:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.new-joke span {
  font-size: 20px;
  margin-right: 7px;
}

.loading {
  padding: 45px;
  color: #b8aed8;
}

.spinner {
  width: 35px;
  height: 35px;
  border: 3px solid #39334f;
  border-top-color: #a78bfa;
  border-radius: 50%;
  margin: auto;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

.error {
  color: #ff9292;
  padding: 35px;
}

.style-section {
  margin-top: 45px;
  text-align: center;
}

.section-heading h2 {
  font-family: 'Outfit', sans-serif;
  font-size: 26px;
}

.section-heading p {
  color: #9996b5;
  font-size: 14px;
}

.style-buttons {
  display: flex;
  justify-content: center;
  gap: 12px;
  margin-top: 25px;
}

.style-buttons button {
  background: #222139;
  color: #c4bddc;
  border: 1px solid #393451;
  border-radius: 12px;
  padding: 15px 25px;
  font-size: 16px;
  transition: 0.3s;
}

.style-buttons button span {
  margin-left: 5px;
}

.style-buttons button:hover,
.style-buttons button.active {
  background: #6045a8;
  border-color: #a78bfa;
  color: white;
}

.extra-section {
  margin-top: 30px;
}

.extra-section > div:first-child {
  text-align: center;
}

.search-box {
  background: #222139;
  border: 1px solid #393451;
  border-radius: 18px;
  padding: 30px;
  margin-top: 35px;
}

.search-box h3 {
  font-family: 'Outfit', sans-serif;
  font-size: 23px;
}

.search-box p {
  color: #9996b5;
  font-size: 14px;
}

.search-box input {
  width: 65%;
  padding: 14px;
  border: 1px solid #494364;
  border-radius: 10px;
  background: #151426;
  color: white;
  font-size: 15px;
}

.search-box input:focus {
  outline: none;
  border-color: #a78bfa;
}

.search-box button {
  background: #8b5cf6;
  color: white;
  border: none;
  border-radius: 10px;
  padding: 14px 20px;
  font-weight: 700;
  margin-left: 8px;
}

.search-result {
  margin-top: 25px;
  background: #222139;
  border: 1px solid #493b70;
  border-radius: 18px;
  padding: 25px;
}

.search-result p {
  line-height: 1.7;
  font-size: 17px;
}

footer {
  text-align: center;
  border-top: 1px solid #302c45;
  padding: 35px 20px;
  color: #9996b5;
}

footer h3 {
  color: #c4b5fd;
  font-family: 'Outfit', sans-serif;
}

.copyright {
  font-size: 12px;
  margin-top: 20px;
}

@media (max-width: 600px) {
  .hero h1 {
    font-size: 39px;
  }

  .header {
    padding: 20px 15px;
  }

  .badge {
    font-size: 8px;
  }

  .style-buttons {
    flex-wrap: wrap;
  }

  .style-buttons button {
    padding: 13px 15px;
  }

  .search-box input {
    width: 100%;
  }

  .search-box button {
    margin: 12px 0 0;
    width: 100%;
  }

  .joke-card {
    padding: 22px 15px;
  }

  .joke-text {
    font-size: 19px;
  }
}
</style>