<script setup lang="ts">
const props = defineProps({
  score: {
    type: Number,
    required: true,
  },
})
</script>
<template>
  <div class="header">
    <div class="game-title">
      ROCK <br />
      PAPER<br />
      SCISSORS
    </div>
    <div class="score">
      <div class="score-title-container">
        <div class="score-title">SCORE</div>
      </div>
      <div class="score-value">{{ score }}</div>
    </div>
  </div>
</template>

<style scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  outline: 2px solid hsl(217, 16%, 45%);
  border-radius: 5px;
  height: 96px;
  width: 100%;
  margin-top: 32px;
  padding: 12px 12px 12px 23px;
  box-sizing: border-box;
}
.header .game-title {
  color: white;
  font-size: 21px;
  font-weight: 700;
  line-height: 16px;
}

.header .score {
  overflow: hidden;
  background-color: #f7f6f6;
  border-radius: 5px;
  height: 100%;
  width: 80px;
}
.header .score .score-title-container {
  display: flex;
  justify-content: center;
  align-items: flex-end;
  background-color: white;
  height: 22px;
  width: 100%;
}
.header .score .score-title-container .score-title {
  font-size: 10px;
  letter-spacing: 1.4px;
  color: hsl(229, 64%, 46%);
}
.header .score .score-value {
  text-align: center;
  font-size: 40px;
  font-weight: 700;
  line-height: 1;
}
</style>
