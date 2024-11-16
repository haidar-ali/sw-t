
<template>
  <div class="card">
    <h2>Lobby</h2>
    <button @click="createGame">Create New Game</button>
    <div v-if="games.length">
      <h3>Available Games</h3>
      <ul>
        <li v-for="game in games" :key="game.id">
          Deck: {{ game.deckName }} | Host: {{ game.host }}
          <button @click="attemptJoinGame(game.id)">Join</button>
        </li>
      </ul>
    </div>
    <p v-else>No games available</p>
  </div>
</template>

<script>
import { db, auth } from "../firebase/firebaseConfig";

export default {
  data() {
    return { games: [] };
  },
  async created() {
    db.ref("games").on("value", (snapshot) => {
      const games = snapshot.val();
      this.games = games ? Object.keys(games).map(id => ({ id, ...games[id] })) : [];
    });
  },
  methods: {
    async createGame() {
      const user = auth.currentUser;
      if (!user) {
        alert("Please log in or sign up to create a game.");
        this.$router.push("/signup");
        return;
      }

      const newGame = {
        host: user.displayName || user.email,
        deckName: "My Awesome Deck", // Example deck name; replace with actual user deck name
        status: "waiting",
        hostId: user.uid,
      };
      const gameRef = db.ref("games").push();
      await gameRef.set(newGame);
      this.$router.push("/game");
    },
    async attemptJoinGame(gameId) {
      const user = auth.currentUser;
      if (!user) {
        alert("You must sign up or log in to join a game.");
        this.$router.push("/signup");
        return;
      }

      // Update Firebase with the joining player ID
      await db.ref(`games/${gameId}`).update({ opponentId: user.uid });
      this.$router.push({ path: "/game", query: { gameId } });
    },
  },
};
</script>
