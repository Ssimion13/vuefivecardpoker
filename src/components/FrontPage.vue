<template>
  <div class="centered">
    <p><strong> Current Deck ID: {{currentDeck}}</strong></p>
    <div class="currentHandDiv"> 
      <div v-for="(card, index) in currentCards"  >
          <img :key="`$index`" v-model="selected" v-on:click="selectCard(index)" v-if="currentCards.length > 0" v-bind:src="card.cards[0].image"     />
      </div>
    </div>
    <div>
      <button v-on:click="newCard"> Draw Card </button>
      <button v-on:click="clearHand"> Clear Hand </button>
      <button v-on:click="swapCards"> Swap Cards </button>
    </div>
  </div>

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
      errors: [],
      selected: [],
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
      //add a new card
        if(this.currentCards.length >= 5){
          alert("hand is full!");
          return;
        }
      axios.get(`https://deckofcardsapi.com/api/deck/${this.currentDeck}/draw/?count=1`)
        .then(response => {
          this.currentCards.push(response.data);
        })
    },
    selectCard: function(index){
      //add a card to the list of cards
      var currentCardIndex = index.toString();
      //if every value in the array doesn't equal the index, return true
      var duplicationCheck = this.selected.every(x => {
        console.log(x);
        return x !== currentCardIndex;
      })

      //thus, if above is true, we'll push it to the array to avoid duplicates
      if(duplicationCheck){
      this.selected.push(currentCardIndex);
      //if it's already here then we'll remove it
      } else {
        var filteredSelection = this.selected.filter(x => {
          return x !== currentCardIndex;
        });
        this.selected = filteredSelection;
      }
    },
    swapCards: function(){
      var deck = this.currentDeck
      var cardsInHand = this.currentCards;
      this.selected.forEach(function (x) {
        console.log(x);
        axios.get(`https://deckofcardsapi.com/api/deck/${deck}/draw/?count=1`)
          .then(response => {
            cardsInHand.splice(x, 1, response.data);
          })
      })
      if(cardsInHand.length > 0){
        this.currentCards = cardsInHand;
      }
      this.selected = [];
    },
    clearHand: function(){
      //remove all cards from the current hand
      this.currentCards = [];
      this.selected = [];
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
.currentHandDiv {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgb(25,25,25);
  height: 500px;
  width: 80%;
}
.centered {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
</style>

