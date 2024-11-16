
<template>
  <div class="card">
    <h2>Create a Deck</h2>
    <form @submit.prevent="createDeck">
      <input type="text" v-model="deckName" placeholder="Deck Name" required />
      <input type="text" v-model="hero" placeholder="Hero" required />
      <input type="text" v-model="base" placeholder="Base" required />
      <textarea v-model="cards" placeholder="Add cards (comma-separated, min 50 cards)" required></textarea>
      <button type="submit">Save Deck</button>
    </form>
  </div>
</template>

<script>
import { db, auth } from "../firebase/firebaseConfig";

export default {
  data() {
    return {
      deckName: "",
      hero: "",
      base: "",
      cards: "",
    };
  },
  methods: {
    async createDeck() {
      const user = auth.currentUser;
      if (!user) {
        alert("Please log in to create a deck.");
        this.$router.push("/login");
        return;
      }

      const cardArray = this.cards.split(",").map((card) => card.trim());
      if (cardArray.length < 50) {
        alert("A deck must contain at least 50 cards.");
        return;
      }

      const deck = {
        deckName: this.deckName,
        hero: this.hero,
        base: this.base,
        cards: cardArray,
      };

      await db.ref(`decks/${user.uid}`).push(deck);
      alert("Deck created successfully!");
      this.$router.push("/");
    },
  },
};
</script>
