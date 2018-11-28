<template>
  <ul>
    <li>
       <p><strong>{{currentDeck}}</strong></p>
    </li>
    <div v-for="card in currentCards" :key="card.id">
        <img v-if="currentCards.length > 0" v-bind:src="card.cards[0].image"    />
    </div>
    <button v-on:click="newCard"> Draw Card </button>
    <button v-on:click="clearHand"> Clear Hand </button>
  </ul>

</template>

<script>
import axios from "axios";

export default {
  name: 'HelloWorld',
  data() {
    return {
      currentDeck: "something",
      currentCards: [],
      posts: [],
      errors: []
    }
  },
  props: {
    msg: String
  },
  created() {
    // 
    axios.get(`https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=20`)
    .then(response => {
      console.log(response);
      this.currentDeck = response.data.deck_id;
    })
    .catch(e => {
      this.errors.push(e)
    })
  },
  methods: {
    newCard: function(){
      console.log(this.currentCards.length);
        if(this.currentCards.length >= 3){
          alert("hand is full!");
          return;
        }
      axios.get(`https://deckofcardsapi.com/api/deck/${this.currentDeck}/draw/?count=1`)
        .then(response => {
          this.currentCards.push(response.data);
        })
    },
    clearHand: function(){
      this.currentCards = [];
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
