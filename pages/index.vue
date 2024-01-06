<script setup lang="ts">
const score = ref(0)

const updateScore = (newScore: number) => {
  score.value = newScore
}
</script>
<template>
  <main>
    <div class="header-container">
      <Header :score="score" />
    </div>
  </main>
  <main>
    <div class="game-container">
      <Game :score="score" :updateScore="updateScore" />
    </div>
  </main>
  <Rules />
</template>

<style scoped></style>
