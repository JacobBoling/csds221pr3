<script setup>
const { ref } = require('vue');
const emit = defineEmits(['toggleHints']);
const props = defineProps(['num']);
</script>

<template>
  <div class="hints">
    <div class="overlay">
      <div class="panel">

        <h3 v-if="sure">Hints</h3>
          <h3 v-else>Are you sure?</h3>

        <div v-if="sure" v-for="i in number">
          <div class="list">{{ i }}) {{ hints[i - 1] }}</div>
        </div>

        <div class="list" v-if="sure && number >= hints.length"><em>All hints shown</em></div>

        <div class="row">
          <button class="hint-btn" v-if="!sure" v-on:click="giveHint">Yes, Give a Hint</button>
          <button class="hint-btn" v-if="!sure" v-on:click="giveAll">Give ALL Hints</button>
          <button class="hint-btn" v-on:click="close" style="margin-bottom: 0;">Close</button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Hints',

  props: {
    num: Number,
  },

  data () {
    const hints = [
      "Each button reveals one truth, and two lies",
      "Find the true shape of all runes",
      "Every rune has one true shape",
      "A rune cannot be both shapes, nor be lying about both",
      "The set of all true triangles is the key"
    ]
    const number = this.$props.num;
    const sure = number >= hints.length; //If all hints have been seen, show by default

    return {
      sure,
      hints,
      number,
    }
  },

  methods: {
    //Give one more hint
    giveHint () {
      this.number++;
      this.sure = true;
    },

    //Give all hints
    giveAll () {
      this.number = this.hints.length;
      this.sure = true;
    },

    //Close window
    close () {
      this.sure = false;
      this.$emit('toggleHints', false, this.number);
    }
  }
}
</script>

<style scoped>  
.panel {
  padding: 10px;
  width: 400px;
}

.list {
  line-height: 2;
  text-align: left;
  display: table-row;
  padding-bottom: 5px;
}

.hint-btn {
  display: inline;
  width: auto;
  margin: 10px;
}
</style>