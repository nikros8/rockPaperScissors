<script setup lang="ts">
import triangleSvg from "assets/svg/bg-triangle.svg"
import rockSvg from "assets/svg/icon-rock.svg"
import paperSvg from "assets/svg/icon-paper.svg"
import scissorsSvg from "assets/svg/icon-scissors.svg"
</script>
<template>
  <div class="game">
    <img class="triangle-background" :src="triangleSvg" alt="triangle" />
    <div class="outer-circle primary paper">
      <div class="outer-circle secondary paper"></div>
      <div class="inner-circle primary">
        <div class="inner-circle secondary"><img :src="paperSvg" alt="paper" /></div>
      </div>
    </div>
    <div class="outer-circle primary scissors">
      <div class="outer-circle secondary scissors"></div>
      <div class="inner-circle primary">
        <div class="inner-circle secondary scissors"><img :src="scissorsSvg" alt="scissors" /></div>
      </div>
    </div>
    <div class="outer-circle primary rock">
      <div class="outer-circle secondary rock"></div>
      <div class="inner-circle primary">
        <div class="inner-circle secondary"><img :src="rockSvg" alt="rock" /></div>
      </div>
    </div>
  </div>
</template>

<style scope>
.game {
  display: flex;
  position: relative;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 105px;
}

.game .triangle-background {
  position: absolute;
  width: 200px;
  top: 53%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* OUTER CIRCLE */

.game .outer-circle {
  border-radius: 50%;
  height: 130px;
  width: 130px;
}

.game .outer-circle.primary {
  position: relative;
  overflow: hidden;
}
.game .outer-circle.secondary {
  position: relative;
  bottom: 5px;
}

.game .outer-circle.primary.paper {
  background-color: #304c9b;
}
.game .outer-circle.secondary.paper {
  background-color: hsl(230, 89%, 65%);
}
.game .outer-circle.primary.scissors {
  background-color: #c87117;
  margin-left: auto;
}
.game .outer-circle.secondary.scissors {
  background-color: hsl(40, 84%, 53%);
}
.game .outer-circle.primary.rock {
  background-color: #9a1934;
  margin-top: 20px;
}
.game .outer-circle.secondary.rock {
  background-color: hsl(349, 70%, 56%);
}

/* INNER CIRCLE */

.game .inner-circle {
  height: 100px;
  width: 100px;
  border-radius: 50%;
}

.game .inner-circle.primary {
  background-color: #b8bfda;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  overflow: hidden;
}
.game .inner-circle.secondary {
  background-color: #ddd;
  position: relative;
  top: 5px;
}

.game .inner-circle img {
  position: relative;
  top: 48%;
  left: 49%;
  transform: translate(-50%, -50%);
  width: 44px;
}

.game .inner-circle.secondary.scissors img {
  width: 46px;
}
</style>
