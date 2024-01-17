<script setup lang="ts">
import type { PropType } from "vue"
import triangleSvg from "/assets/svg/bg-triangle.svg"

const props = defineProps({
  updateScore: {
    type: Function as PropType<(score: number) => void>,
    required: true,
  },
})

const score = ref(0)

const updateScoreAndEmit = (result: string) => {
  switch (result) {
    case "YOU WIN":
      score.value += 1
      break
    case "YOU LOSE":
      score.value -= 1
      break
  }
  props.updateScore(score.value)
}

const isBackgroundVisible = ref(true)

const allHands: Record<
  string,
  { isVisible: boolean; backgroundPrimary: string; backgroundSecondary: string }
> = reactive({
  paper: {
    isVisible: true,
    backgroundPrimary: "#304c9b",
    backgroundSecondary: "hsl(230, 89%, 65%)",
  },
  scissors: {
    isVisible: true,
    backgroundPrimary: "#c87117",
    backgroundSecondary: "hsl(40, 84%, 53%)",
  },
  rock: {
    isVisible: true,
    backgroundPrimary: "#9a1934",
    backgroundSecondary: "hsl(349, 70%, 56%)",
  },
})
const playerPicked = ref<string | undefined>(undefined)
const computerPicked = ref<string | undefined>(undefined)
const showPlayerHand = ref<boolean>(false)
const showOpponentHand = ref<boolean>(false)

const result = ref("")
function handleResult(playerPicked: string, computerPicked: string) {
  switch (playerPicked + computerPicked) {
    case "paperrock":
    case "rockscissors":
    case "scissorspaper":
      result.value = "YOU WIN"
      break
    case "paperscissors":
    case "rockpaper":
    case "scissorsrock":
      result.value = "YOU LOSE"
      break
    case "paperpaper":
    case "rockrock":
    case "scissorsscissors":
      result.value = "DRAW"
      break
  }

  updateScoreAndEmit(result.value)
}
const resizeContainer = ref(false)

function prepareGame(hand: string) {
  isBackgroundVisible.value = false
  playerPicked.value = hand
  computerPicked.value = Object.keys(allHands)[Math.floor(Math.random() * 3)]
}
const delay = (ms: number): Promise<void> => new Promise((resolve) => setTimeout(resolve, ms))

async function playGameStepByStep() {
  await delay(500)
  showPlayerHand.value = true
  await delay(Math.floor(Math.random() * (2500 - 1500 + 1) + 1500))
  showOpponentHand.value = true
  await delay(500)
  resizeContainer.value = true
  await delay(1000)
}

function handleGame(hand: string) {
  if (playerPicked.value === undefined) {
    prepareGame(hand)
    playGameStepByStep().then(() => {
      handleResult(playerPicked.value!, computerPicked.value!)
    })
  }
}

function startNewGame() {
  isBackgroundVisible.value = true
  showPlayerHand.value = false
  showOpponentHand.value = false
  playerPicked.value = undefined
  computerPicked.value = undefined
  resizeContainer.value = false
  result.value = ""
}
</script>
<template>
  <div :class="['game-container', resizeContainer ? 'resize-container' : '']">
    <div :class="['game', showPlayerHand ? 'is-playing' : 'not-playing']">
      <!-- Triangle Background with transition vanish when player made a choice -->
      <Transition>
        <img
          v-if="isBackgroundVisible"
          class="triangle-background"
          :src="triangleSvg"
          alt="triangle"
        />
      </Transition>
      <!-- Before game start show all hands what you can pick from.
          If player pick a hand, game will start and this will be hidden  -->
      <template v-for="(hand, name) in allHands">
        <Transition>
          <Hand
            v-if="!playerPicked && !computerPicked"
            :handType="name"
            :outerCircleBackgroundPrimary="hand.backgroundPrimary"
            :outerCircleBackgroundSecondary="hand.backgroundSecondary"
            :class="[playerPicked === name ? 'player-picked' : name]"
            @click="handleGame(name)"
          />
        </Transition>
      </template>

      <!-- After game start -->
      <div class="hands">
        <div v-if="showPlayerHand && playerPicked" class="player-hand">
          <Hand
            class="hand"
            :handType="playerPicked"
            :outerCircleBackgroundPrimary="allHands[playerPicked].backgroundPrimary"
            :outerCircleBackgroundSecondary="allHands[playerPicked].backgroundSecondary"
          />
          <div class="title">YOU PICKED</div>
        </div>
        <div v-if="!showOpponentHand && showPlayerHand" class="computer-hand skeleton">
          <div class="skeleton-circle-container">
            <div class="skeleton-circle"></div>
          </div>
          <div class="title">THE HOUSE PICKED</div>
        </div>
        <div v-if="showOpponentHand && computerPicked" class="computer-hand">
          <Hand
            class="hand"
            :handType="computerPicked"
            :outerCircleBackgroundPrimary="allHands[computerPicked].backgroundPrimary"
            :outerCircleBackgroundSecondary="allHands[computerPicked].backgroundSecondary"
          />
          <div class="title">THE HOUSE PICKED</div>
        </div>
      </div>
      <!-- Result label (win, loose, draw) with play again button -->
      <div v-if="result" class="result">
        <div class="result-title">{{ result }}</div>
        <button @click="startNewGame()">PLAY AGAIN</button>
      </div>
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

@media (min-width: 750px) {
  .game-container.resize-container {
    max-width: 700px;
  }
  .game {
    margin-top: 63px;
  }
  .game .title {
    margin-top: 0;
    margin-bottom: 50px;
  }
  .game.game.is-playing {
    margin-top: 75px;
  }
  .game .triangle-background {
    width: 300px;
  }
  .game .computer-hand .skeleton-circle-container {
    height: 200px;
    width: 200px;
  }
  .game .computer-hand .skeleton-circle {
    height: 180px;
    width: 180px;
  }
  .game .player-hand,
  .game .computer-hand {
    flex-direction: column-reverse;
  }
  .game .result {
    position: absolute;
    margin-top: 0;
    top: 50%;
    transform: translate(0, -50%);
  }
}

@media (max-height: 740px) {
  .game {
    margin-top: 50px;
  }
  .game .result {
    margin-top: 40px;
  }
}
</style>
