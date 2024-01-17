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
    <Game :updateScore="updateScore" />
  </main>
  <Rules />
</template>

<style scoped></style>
