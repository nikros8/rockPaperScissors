<script setup lang="ts">
import rockSvg from "assets/svg/icon-rock.svg"
import paperSvg from "assets/svg/icon-paper.svg"
import scissorsSvg from "assets/svg/icon-scissors.svg"
const props = defineProps({
  name: {
    type: String,
    required: true,
  },
  playerPicked: {
    type: String,
    require: true,
  },
  colorPrimary: {
    type: String,
    required: true,
  },
  colorSecondary: {
    type: String,
    required: true,
  },
})

const colors = reactive({
  primaryColor: {
    backgroundColor: props.colorPrimary,
  },
  secondaryColor: {
    backgroundColor: props.colorSecondary,
  },
})

const didPlayerChoose = (): boolean => {
  if (props.playerPicked === props.name) {
    return true
  } else {
    return false
  }
}

const capitalize = (value: string): string => {
  const capitalized = value.charAt(0).toUpperCase() + value.slice(1)
  return capitalized
}

const getIconPath = (name: string): string => {
  // Create a mapping of names to SVG files
  const iconMap: Record<string, string> = {
    rock: rockSvg,
    paper: paperSvg,
    scissors: scissorsSvg,
  }
  return iconMap[name.toLowerCase()] || ""
}
</script>
<template>
  <div>
    <div class="outer-circle primary" :style="colors.primaryColor">
      <div class="outer-circle secondary" :style="colors.secondaryColor"></div>
      <div class="inner-circle primary">
        <div class="inner-circle secondary">
          <img :src="getIconPath(name)" :alt="`${capitalize(name)}`" />
        </div>
      </div>
    </div>
    <h2 v-if="didPlayerChoose()">YOU PICKED</h2>
  </div>
</template>
<style scoped>
/* OUTER CIRCLE */

.outer-circle {
  cursor: pointer;
  border-radius: 50%;
  height: 130px;
  width: 130px;
}

.outer-circle.primary {
  position: relative;
  overflow: hidden;
}
.outer-circle.secondary {
  position: relative;
  bottom: 5px;
}

/* INNER CIRCLE */
.inner-circle {
  height: 100px;
  width: 100px;
  border-radius: 50%;
}

.inner-circle.primary {
  background-color: #b8bfda;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  overflow: hidden;
}
.inner-circle.secondary {
  background-color: #ddd;
  position: relative;
  top: 5px;
}

.inner-circle img {
  position: relative;
  top: 48%;
  left: 49%;
  transform: translate(-50%, -50%);
  width: 44px;
}

.inner-circle.secondary.scissors img {
  width: 46px;
}
</style>
