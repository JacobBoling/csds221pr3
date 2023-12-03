<template>
  <div id="app">
    <Greeting 
      v-if="showGreeting" 
      :begin="begin"
      :streak="streak"
      :time="timeStr"
      @toggleGreeting="start"
    />
    <Hints 
      v-if="showHints" 
      :num="numHints" 
      @toggleHints="(show, num) => { showHints = show; numHints = num }"
    />

    <div class="panel">
      <div class="col">

        <h1>One Truth, Two Lies</h1>
        <h3 class="timer">{{ timeStr }}</h3>

        <div class="col">
          <div class="row">
            <button class="rune" id="a" v-on:click="selectRune">A</button>
            <button class="rune" id="b" v-on:click="selectRune">B</button>
            <button class="rune" id="c" v-on:click="selectRune">C</button>
            <button class="rune" id="d" v-on:click="selectRune">D</button>
            <button class="rune" id="e" v-on:click="selectRune">E</button>
          </div>
          <div class="row">
            <button class="rune" id="f" v-on:click="selectRune">F</button>
            <button class="rune" id="g" v-on:click="selectRune">G</button>
            <button class="rune" id="h" v-on:click="selectRune">H</button>
            <button class="rune" id="i" v-on:click="selectRune">I</button>
            <button class="rune" id="j" v-on:click="selectRune">J</button>
            <button class="rune" id="k" v-on:click="selectRune">K</button>
          </div>
          <div class="row">
            <button class="rune" id="l" v-on:click="selectRune">L</button>
            <button class="rune" id="m" v-on:click="selectRune">M</button>
            <button class="rune" id="n" v-on:click="selectRune">N</button>
            <button class="rune" id="o" v-on:click="selectRune">O</button>
            <button class="rune" id="p" v-on:click="selectRune">P</button>
          </div>
        </div>

        <button v-on:click="checkAnswer">Enter</button>

        <div class="col">
          <div class="form-check-inline">
            <input type="radio" class="form-check-input" name="selector" value="0" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="1" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="2" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="3" v-on:click="selectTriple"/>
          </div>
          <div class="form-check-inline">
            <input type="radio" class="form-check-input" name="selector" value="4" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="5" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="6" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="7" v-on:click="selectTriple"/>
          </div>
          <div class="form-check-inline">
            <input type="radio" class="form-check-input" name="selector" value="8" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="9" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="10" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="11" v-on:click="selectTriple"/>
          </div>
          <div class="form-check-inline">
            <input type="radio" class="form-check-input" name="selector" value="12" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="13" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="14" v-on:click="selectTriple"/>
            <input type="radio" class="form-check-input" name="selector" value="15" v-on:click="selectTriple"/>
          </div>
        </div>

        <div>
          <button v-on:click="clear">Clear Inputs</button>
          <button v-on:click="getHint">I'm Stuck!</button>
        </div>

        <!--Debugging tool-->
        <div><button v-if="false" v-on:click="test">Test</button></div>

        <h1 v-if="showBan" style="color: red;"><i class="fa fa-fw fa-ban icon"></i></h1>

        <p class="credits">Puzzle/Story Credit: <em>Destiny 2: Season of the Witch</em>, Bungie, 2023.</p>
      </div>
    </div>
  </div>
</template>

<script>
const { ref } = require('vue'); 
const moment = require('moment');
import Greeting from './components/Greeting.vue'
import Hints from './components/Hints.vue'
const showGreeting = ref(true);
const showHints = ref(false);

export default {
  name: 'App',
  components: {
    Greeting,
    Hints,
  },
  data () {
    //True shapes of runes
    //true = triangle, false = circle
    const shapes = {};
    const curatedShapes = { //Default puzzle
      a: true,  b: false, c: true,  d: false, 
      e: false, f: true,  g: true,  h: false, 
      i: false, j: false, k: true,  l: false,
      m: false, n: true,  o: true,  p: false
    };
    
    //Honesty of the runes displayed by each button
    //true = truth, false = lie
    const triples = [];
    const curatedTriples = [ //Default puzzle
      { a: false, l: false, n: true  },
      { e: true,  m: false, p: false },
      { b: true,  k: false, o: false },
      { c: false, g: true,  m: false },
      { a: false, f: true,  n: false },
      { i: false, l: true,  o: false },
      { e: false, j: false, p: true  },
      { c: false, d: false, g: true  },
      { i: true,  l: false, n: false },
      { c: false, d: true,  g: false },
      { i: false, k: false, o: false },
      { b: false, h: false, j: false },
      { b: false, h: true,  k: false },
      { c: false, d: false, g: true  },
      { g: true,  m: false, p: false },
      { e: false, h: false, j: true  },
    ];

    //Runes currently selected
    const selected = {
      a: false, b: false, c: false, d: false, 
      e: false, f: false, g: false, h: false, 
      i: false, j: false, k: false, l: false,
      m: false, n: false, o: false, p: false
    };
    
    const showBan = false; //For displaying the "incorrect" symbol
    const numHints = 0; //Number of hints given
    const begin = true; //For changing the mode of the greeting message
    const streak = 0; //Number of puzles solved
    const seconds = 0; //Time on current puzzle
    const timeStr = ""; //String for timer display
    const countTime = false; //Set if the timer is ticking

    return {
      showGreeting,
      showHints,
      shapes,
      curatedShapes,
      triples,
      curatedTriples,
      selected,
      showBan,
      numHints,
      begin,
      streak,
      seconds,
      timeStr,
      countTime,
    }
  },

  methods: {
    //Greeting onclick/close
    start (show, shuffle) {
      //Assign curated puzzle to establish object structures, will be overwritten if shuffled
      this.shapes = Object.assign({}, this.curatedShapes);
      this.triples = this.curatedTriples.slice();
      //Shuffle if desired
      if (shuffle) {
        this.shuffleShapes();
        this.shuffleTriples();
      }
      //Start game
      this.clear();
      this.timer(true);
      showGreeting.value = show;
    },

    //Creates a boolean array of new shapes.
    shuffleShapes () {
      Object.keys(this.shapes).forEach(rune => 
        this.shapes[rune] = Math.random() < 0.5
      )
      //Run it again if there are no triangles (1 in 32768 chance)
      if (!this.allTrue(this.shapes)) { this.shuffleShapes() }
    },

    //Assigns runes to triples
    shuffleTriples () {
      //Iterate through triples
      for (var i = 0; i < 16; i++) {
        let triple = {};
        let runes = Object.keys(this.shapes);
        let count = 16;
        //Assign three runes to each triple, without repeating
        for (var j = 0; j < 3; j++) {
          let index = Math.trunc(Math.random() * count);
          triple[runes[index]] = false;
          runes.splice(index, 1);
          count--;
        }
        //Assign one of the three runes to be a truth, other two are lies
        triple[Object.keys(triple)[Math.trunc(Math.random() * 3)]] = true;
        //Add to object
        this.triples[i] = triple;
      }
      //Shuffle again if a rune never appears (1 in 25 chance)
      if (!this.verifyTriples()) { this.shuffleTriples(); }
    },

    //Makes sure every rune is present
    verifyTriples () {
      let present = {
        a: false, b: false, c: false, d: false, 
        e: false, f: false, g: false, h: false, 
        i: false, j: false, k: false, l: false,
        m: false, n: false, o: false, p: false
      };
      //Find which runes are present
      this.triples.forEach(triple => {
        Object.keys(triple).forEach(rune => {
          present[rune] = true;
        })
      });
      //Make sure all runes are present
      return this.allTrue(present);
    },

    //Checks if all elements in an object are truthy
    allTrue (obj) {
      Object.keys(obj).forEach(key => {
        if (!obj[key]) { return false; }
      });
      return true;
    },

    //Rune onclick
    selectRune (event) {
      let rune = event.currentTarget.id;
      this.selected[rune] = !this.selected[rune];
      event.target.classList.toggle('selected');
    },

    //Radio onclick
    selectTriple (event) {
      let triple = this.triples[event.currentTarget.value];
      //Set shapes for each rune
      Object.keys(this.shapes).forEach(rune =>  {
        let symbClasses = document.getElementById(rune).classList
        //Remove any existing shapes
        symbClasses.remove('triangle', 'circle')
        //Only add shape if it has one in this triple
        if (triple[rune] != undefined) {
          //If the rune is a truthful triangle or a lying circle, display a triangle
          if (triple[rune] == this.shapes[rune]) {
            symbClasses.add('triangle');
          } else symbClasses.add('circle'); //Otherwise circle
        }
      });
    },

    //Enter onclick
    checkAnswer () {
      let right = true;
      //Iterate through answers and selections
      Object.keys(this.shapes).forEach(rune =>  {
        //Each triangle must be selected, each circle must be unselected
        if (this.shapes[rune] != this.selected[rune]) {
          right = false;
        }
      });
      //Display congratulations if right
      if (right) {
        this.countTime = false;
        this.streak++;
        this.begin = false;
        showGreeting.value = true;
      } else {
        //Show red ban symbol briefly if wrong
        this.showBan = true;
        setTimeout(() => this.showBan = false, 1000);
      }
    },

    //Clear onclick
    clear () {
      //Clear runes
      Object.keys(this.selected).forEach(rune =>  {
        this.selected[rune] = false;
        document.getElementById(rune).classList.remove('selected', 'triangle', 'circle')
      });
      //Clear radio
      var radios = document.getElementsByName("selector");
      for(var i = 0; i < radios.length; i++) {
        radios[i].checked = false;
      }
    },

    //Hint onclick
    getHint () {
      showHints.value = true;
    },

    //Timer
    timer (start) {
      if (start) {
        this.seconds = 0;
        this.countTime = true;
      }
      if (this.countTime) {
        //Set timer to the HH:MM:SS portion of the string
        this.timeStr = new Date(this.seconds * 1000).toISOString().slice(11, 19);
        this.seconds++;
        setTimeout(() => this.timer(false), 1000);
      }
    },

    //Tester
    test () {
      console.log(JSON.stringify(this.triples));
    }
  }
}
</script>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.panel {
  width: auto;
  height: 100vh;
  margin: 5px;
  padding: 15px;
  background-color: tan;
  border-style: solid;
  border-color: sienna;
  border-width: 5px;
  border-radius: 10px;
}

.overlay {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
}
  
.overlay > .panel {
  position: relative;
  width: 500px;
  height: auto;
  z-index: 9999;
  margin: 50px auto;
  padding: 20px 30px;
}

.row {
  display: block;
}

.col {
  display: inline;
}

.timer {
  position: absolute;
  top: 15px;
  right: 20px;
}

button {
  background-color: burlywood;
  border-style: solid;
  border-color: sienna;
  border-radius: 5px;
  text-align: center;
  padding: 10px;
  margin: 10px;
  display: inline;
}

.rune {
  float: center;
  height: 30px;
  width: 30px;
  margin: 0px;
  padding: 0px;
}

.selected {
  border-style: solid;
  border-color: red;
}

.triangle {
  background-color: forestgreen;
}

.circle {
  background-color: royalblue;
}

.form-check-inline {
  display: block;
  margin-left: 15px;
}

.form-check-input {
  margin: 4px;
}

.credits {
  font-size: 10px;
  position: absolute;
  bottom: 0;
  right: 20px;
}
</style>
