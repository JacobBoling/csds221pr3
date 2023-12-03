<script setup>
const { ref } = require('vue');
const emit = defineEmits(['toggleGreeting']);
const props = defineProps(['begin', 'streak', 'time']);
</script>

<template>
  <div class="greeting">
    <div class="overlay">
      <div class="panel">
        <div v-if="begin">
          <h3>Welcome to the Imbaru Engine</h3>
          <p>
            After a perilous adventure through a deep dungeon, an adventurer stands in front of a great vault. Behind it lies a treasure beyond value, locked with a puzzle designed by the God of Cunning herself. Legend says only the most ingenious can gain access to the riches within.
          </p>
          <p>
            There are sixteen runes and sixteen buttons. Input the correct set of runes, and the prize is yours. The selected button does not impact the answer. On the face of the vault, the God of Cunning has graciously provided a single clue:
          </p>
        </div>
        <div v-else>
          <h3>Congratulations!</h3>
          <p>
            You have outsmarted the God of Cunning's machinations and proven yourself worthy of the grand prize. Beyond the door lies a large room full of gold, jewls, and precious artifacts. It would take a hundred lifetimes to spend it all if you tried.
          </p>
          <div class="row">
            <h5>Time: {{ time }}</h5>
            <h5>Streak: {{ streak }}</h5>
          </div>
          <p>
            But what is that on the opposing wall? Another set of runes! While these riches are immense, the possibility of gaining even more is dreadfully enticing. Do you wish to double your winnings with another puzzle?
          </p>
        </div>
        <h3>"One Truth, Two Lies"</h3>
        <button v-on:click="close(false)">Begin Curated Puzzle</button>
        <button v-on:click="close(true)">Begin Shuffled Puzzle</button>
        <p v-if="begin"><em>The curated puzzle is recommended for newcomers</em></p>
          <button v-else v-on:click="quit()">I am satisfied with my plunder</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Greeting',

  props: {
    begin: Boolean,
    streak: Number,
    time: String,
  },

  methods: {
    close (shuffle) {
      this.$emit('toggleGreeting', false, shuffle);
    },

    quit () {
      location.reload();
    },
  }
}
</script>

<style scoped>
</style>
