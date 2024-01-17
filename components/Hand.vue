<script setup lang="ts">
import rockSVG from "/assets/svg/icon-rock.svg"
import paperSVG from "/assets/svg/icon-paper.svg"
import scissorsSVG from "/assets/svg/icon-scissors.svg"
const props = defineProps({
  handType: {
    type: String,
  },
  outerCircleBackgroundPrimary: {
    type: String,
    required: true,
  },
  outerCircleBackgroundSecondary: {
    type: String,
    required: true,
  },
})
const handImages: Record<string, string> = {
  rock: rockSVG,
  paper: paperSVG,
  scissors: scissorsSVG,
}

const colors = reactive({
  primaryColor: {
    backgroundColor: props.outerCircleBackgroundPrimary,
  },
  secondaryColor: {
    backgroundColor: props.outerCircleBackgroundSecondary,
  },
})

const capitalize = (value: string): string => {
  const capitalized = value.charAt(0).toUpperCase() + value.slice(1)
  return capitalized
}

const getIconPath = (name: string | undefined): string => {
  return name !== undefined ? handImages[name.toLowerCase()] : ""
}
</script>
<template>
  <div>
    <div class="outer-circle primary" :style="colors.primaryColor">
      <div class="outer-circle secondary" :style="colors.secondaryColor"></div>
      <div class="inner-circle primary">
        <div class="inner-circle secondary">
          <img v-if="handType" :src="getIconPath(handType)" :alt="`${capitalize(handType)}`" />
        </div>
      </div>
    </div>
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

@media (min-width: 750px) {
  .outer-circle {
    height: 200px;
    width: 200px;
  }
  .inner-circle {
    height: 155px;
    width: 155px;
  }
  .inner-circle img {
    width: 70px;
  }
}
</style>
