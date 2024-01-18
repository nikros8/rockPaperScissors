<script setup lang="ts">
import type { PropType } from "vue"
import triangleSvg from "/assets/svg/bg-triangle.svg"

const props = defineProps({
  updateScore: {
    type: Function as PropType<(score: number) => void>,
    required: true,
  },
})

enum GameState {
  start,
  end,
}

enum GameResult {
  win,
  lose,
  draw,
}

enum HandType {
  rock,
  paper,
  scissors,
}

const score = ref(0)
const resultLabel = ref("")
const isBackgroundVisible = ref(true)

let currentGameState: GameState
let lastGameResult: GameResult

const updateScoreAndEmit = (result: GameResult) => {
  switch (result) {
    case GameResult.win:
      resultLabel.value = "YOU WIN"
      score.value += 1
      break
    case GameResult.lose:
      resultLabel.value = "YOU LOSE"
      score.value -= 1
      break
    case GameResult.draw:
      resultLabel.value = "DRAW"
      break
  }
  currentGameState = GameState.end
  props.updateScore(score.value)
}

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

function handleResult(playerPicked: string, computerPicked: string): GameResult {
  const playerHand = getHandTypeFromString(playerPicked)
  const computerHand = getHandTypeFromString(computerPicked)
  switch (true) {
    case playerHand === HandType.paper && computerHand === HandType.rock:
    case playerHand === HandType.rock && computerHand === HandType.scissors:
    case playerHand === HandType.scissors && computerHand === HandType.paper:
      return GameResult.win
    case playerHand === HandType.paper && computerHand === HandType.scissors:
    case playerHand === HandType.rock && computerHand === HandType.paper:
    case playerHand === HandType.scissors && computerHand === HandType.rock:
      return GameResult.lose
    case playerHand === computerHand:
      return GameResult.draw
    default:
      return GameResult.draw // Add a default case to cover all possible code paths
  }
}
const resizeContainer = ref(false)

function prepareGame(hand: string) {
  isBackgroundVisible.value = false
  playerPicked.value = hand.toString()
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
  currentGameState = GameState.start
  if (playerPicked.value === undefined) {
    prepareGame(hand)
    playGameStepByStep().then(() => {
      lastGameResult = handleResult(playerPicked.value!, computerPicked.value!)
      updateScoreAndEmit(lastGameResult)
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
  resultLabel.value = ""
}

function getHandTypeFromString(typeString: string): HandType {
  switch (typeString) {
    case "rock":
      return HandType.rock
    case "paper":
      return HandType.paper
    case "scissors":
      return HandType.scissors
    default:
      return HandType.rock // or handle the invalid case according to your needs
  }
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
      <template v-for="(hand, key) in allHands">
        <Transition>
          <Hand
            v-if="!playerPicked && !computerPicked"
            :handType="key"
            :outerCircleBackgroundPrimary="hand.backgroundPrimary"
            :outerCircleBackgroundSecondary="hand.backgroundSecondary"
            :class="[playerPicked === key ? 'player-picked' : key]"
            @click="handleGame(key)"
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
      <div v-if="resultLabel" class="result">
        <div class="result-title">{{ resultLabel }}</div>
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
