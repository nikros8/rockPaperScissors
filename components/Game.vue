<script setup lang="ts">
import triangleSvg from "assets/svg/bg-triangle.svg"
import rockSvg from "assets/svg/icon-rock.svg"
import paperSvg from "assets/svg/icon-paper.svg"
import scissorsSvg from "assets/svg/icon-scissors.svg"

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

function handleGame(choice: string) {
  if (playerPicked.value === undefined) {
    playerPicked.value = choice
    computerPicked.value = Object.keys(allHands)[Math.floor(Math.random() * 3)]
    isBackgroundVisible.value = false
    for (const key in allHands) {
      allHands[key].isVisible = false
    }
    setTimeout(() => {
      showPlayerHand.value = true
      setTimeout(() => {
        showOpponentHand.value = true
      }, Math.floor(Math.random() * (2500 - 1500 + 1) + 1500))
    }, 500)
  }
}
</script>
<template>
  <div class="game">
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
          v-if="value.isVisible"
          :name="name"
          :color-primary="value.outerCircleColorPrimary"
          :color-secondary="value.outerCircleColorSecondary"
          :class="[playerPicked === name ? 'player-picked' : name]"
          @click="handleGame(name)"
        />
      </Transition>
    </template>
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
  flex-wrap: wrap;
  justify-content: space-between;
  margin-top: 105px;
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
</style>
