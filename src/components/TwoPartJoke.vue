<template>
  <div v-if="joke.type === 'twopart'" class="two-part">
    <h3>🎭 Two-Part Joke</h3>

    <div class="part">
      <span>SETUP</span>
      <p>{{ formattedSetup }}</p>
    </div>

    <div class="part">
      <span>PUNCHLINE</span>
      <p>{{ formattedDelivery }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TwoPartJoke',

  props: {
    joke: {
      type: Object,
      required: true
    },

    styleType: {
      type: String,
      required: true
    }
  },

  computed: {
    formattedSetup() {
      return this.formatText(this.joke.setup);
    },

    formattedDelivery() {
      return this.formatText(this.joke.delivery);
    }
  },

  methods: {
    formatText(text) {
      if (!text) return '';

      if (this.styleType === 'loud') {
        return text.toUpperCase();
      }

      if (this.styleType === 'snake') {
        return text.replaceAll(' ', '_');
      }

      return text;
    }
  }
};
</script>

<style scoped>
.two-part {
  background: #222139;
  border: 1px solid #493b70;
  border-radius: 16px;
  padding: 25px;
  margin-top: 25px;
}

.two-part h3 {
  color: #c4b5fd;
  font-size: 20px;
  margin-top: 0;
}

.part {
  margin-top: 20px;
}

.part span {
  color: #a78bfa;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 2px;
}

.part p {
  color: #eeeafc;
  line-height: 1.6;
  font-size: 17px;
}
</style>