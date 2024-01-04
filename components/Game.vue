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
const computerPicked = ref<string | null>(null)

function handleGame(choice: string) {
  if (playerPicked.value === undefined) {
    playerPicked.value = choice
    computerPicked.value = Object.keys(allHands)[Math.floor(Math.random() * 3)]
    isBackgroundVisible.value = false
    for (const key in allHands) {
      if (key !== playerPicked.value) {
        allHands[key].isVisible = false
      }
    }
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
          :playerPicked="playerPicked"
          :color-primary="value.outerCircleColorPrimary"
          :color-secondary="value.outerCircleColorSecondary"
          :class="[playerPicked === name ? 'player-picked' : name]"
          @click="handleGame(name)"
        />
      </Transition>
    </template>
    <!-- <Hand
      v-if="computerPicked"
      :name="computerPicked"
      :color-primary="allHands[computerPicked].outerCircleColorPrimary"
      :color-secondary="allHands[computerPicked].outerCircleColorSecondary"
      class="computer-picked"
    /> -->
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
</style>
