
<template>
  <div>
    <h2>Game Board</h2>
    <div>
      <p>Current Phase: {{ currentPhase }}</p>
      <button @click="nextPhase">Next Phase</button>
    </div>
  </div>
</template>

<script>
import { mapActions, mapState } from "vuex";

export default {
  computed: {
    ...mapState(["gameState", "currentPhase"]),
  },
  methods: {
    ...mapActions(["startGame", "nextPhase", "subscribeToGameState"]),
  },
  mounted() {
    this.startGame();
    this.subscribeToGameState(this.gameState.gameId);
  },
};
</script>
