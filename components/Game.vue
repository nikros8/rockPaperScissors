<script setup lang="ts">
import triangleSvg from "assets/svg/bg-triangle.svg"
import rockSvg from "assets/svg/icon-rock.svg"
import paperSvg from "assets/svg/icon-paper.svg"
import scissorsSvg from "assets/svg/icon-scissors.svg"

const newScore = ref(0)

const props = defineProps(["score", "updateScore"])
const updateScoreAndEmit = (result: string) => {
  switch (result) {
    case "YOU WIN":
      newScore.value += 1
      break
    case "YOU LOSE":
      newScore.value -= 1
      break
  }
  props.updateScore(newScore.value)
}

const isBackgroundVisible = ref(true)

const allHands: Record<
  string,
  { isVisible: boolean; outerCircleColorPrimary: string; outerCircleColorSecondary: string }
> = reactive({
  paper: {
    isVisible: true,
    outerCircleColorPrimary: "#304c9b",
    outerCircleColorSecondary: "hsl(230, 89%, 65%)",
  },
  scissors: {
    isVisible: true,
    outerCircleColorPrimary: "#c87117",
    outerCircleColorSecondary: "hsl(40, 84%, 53%)",
  },
  rock: {
    isVisible: true,
    outerCircleColorPrimary: "#9a1934",
    outerCircleColorSecondary: "hsl(349, 70%, 56%)",
  },
})
const playerPicked = ref<string | undefined>(undefined)
const computerPicked = ref<string | undefined>(undefined)
const showPlayerHand = ref<boolean>(false)
const showOpponentHand = ref<boolean>(false)

const result = ref<string>("")
function handleResult(playerPicked: string, computerPicked: string) {
  if (playerPicked !== computerPicked) {
    switch (playerPicked + computerPicked) {
      case "paperscissors":
        result.value = "YOU LOSE"
        break
      case "scissorspaper":
        result.value = "YOU WIN"
        break
      case "paperrock":
        result.value = "YOU WIN"
        break
      case "rockpaper":
        result.value = "YOU LOSE"
        break
      case "scissorsrock":
        result.value = "YOU LOSE"
        break
      case "rockscissors":
        result.value = "YOU WIN"
        break
    }
  } else {
    result.value = "DRAW"
  }
  updateScoreAndEmit(result.value)
}

function handleGame(choice: string) {
  if (playerPicked.value === undefined) {
    playerPicked.value = choice
    computerPicked.value = Object.keys(allHands)[Math.floor(Math.random() * 3)]
    isBackgroundVisible.value = false
    setTimeout(() => {
      showPlayerHand.value = true
      setTimeout(() => {
        showOpponentHand.value = true
        handleResult(playerPicked.value!, computerPicked.value!)
      }, Math.floor(Math.random() * (2500 - 1500 + 1) + 1500))
    }, 500)
  }
}

function startNewGame() {
  isBackgroundVisible.value = true
  showPlayerHand.value = false
  showOpponentHand.value = false
  playerPicked.value = undefined
  computerPicked.value = undefined
  result.value = ""
}
</script>
<template>
  <div :class="['game', showPlayerHand ? 'is-playing' : 'not-playing']">
    <Transition>
      <img
        v-if="isBackgroundVisible"
        class="triangle-background"
        :src="triangleSvg"
        alt="triangle"
      />
    </Transition>
    <template v-for="(value, name) in allHands">
      <Transition>
        <Hand
          v-if="!playerPicked && !computerPicked"
          :name="name"
          :color-primary="value.outerCircleColorPrimary"
          :color-secondary="value.outerCircleColorSecondary"
          :class="[playerPicked === name ? 'player-picked' : name]"
          @click="handleGame(name)"
        />
      </Transition>
    </template>
    <div class="hands">
      <div v-if="showPlayerHand && playerPicked" class="player-hand">
        <Hand
          :name="playerPicked"
          :color-primary="allHands[playerPicked].outerCircleColorPrimary"
          :color-secondary="allHands[playerPicked].outerCircleColorSecondary"
        />
        <div class="title">YOU PICKED</div>
      </div>
      <div v-if="showOpponentHand && computerPicked" class="computer-hand">
        <Hand
          :name="computerPicked"
          :color-primary="allHands[computerPicked].outerCircleColorPrimary"
          :color-secondary="allHands[computerPicked].outerCircleColorSecondary"
        />
        <div class="title">THE HOUSE PICKED</div>
      </div>
      <div v-if="!showOpponentHand && showPlayerHand" class="computer-hand skeleton">
        <div class="skeleton-circle"></div>
        <div class="title">THE HOUSE PICKED</div>
      </div>
    </div>
    <div v-if="result" class="result">
      <div class="result-title">{{ result }}</div>
      <button @click="startNewGame()">PLAY AGAIN</button>
    </div>
  </div>
</template>

<style scope>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.2s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
.game {
  display: flex;
  position: relative;
  margin-top: 105px;
}

.game.not-playing {
  flex-wrap: wrap;
  justify-content: space-between;
}
.game.is-playing {
  flex-direction: column;
  flex-wrap: unset;
  justify-content: unset;
}

.game .hands {
  width: 100%;
  display: flex;
  justify-content: space-between;
}

.game .player-hand,
.game .computer-hand {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.game .computer-hand.skeleton {
  align-self: flex-end;
}
.game .computer-hand .skeleton-circle {
  background-color: #192944;
  height: 110px;
  width: 110px;
  margin-bottom: 10px;
  border-radius: 50%;
}

.game .rock {
  margin-top: 20px;
  margin-left: auto;
  margin-right: auto;
}

.game .triangle-background {
  position: absolute;
  width: 200px;
  top: 53%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.game .title {
  color: white;
  font-size: 16px;
  margin-top: 25px;
  letter-spacing: 1.3px;
}

.game .result {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  margin-top: 65px;
  width: 100%;
}
.game .result .result-title {
  color: white;
  font-size: 55px;
  font-weight: 700;
}
.game .result button {
  cursor: pointer;
  height: 48px;
  width: 220px;
  border-radius: 8px;
  margin-top: 18px;
}
.game .result button:hover {
  color: #d73562;
}
</style>
