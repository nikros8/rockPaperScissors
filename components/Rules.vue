<script setup lang="ts">
import rulesSvg from "assets/svg/image-rules.svg"
const isOpen = ref(false)
</script>
<!-- TODO - Modal on desktop should have background with opacity -->
<template>
  <div class="rules">
    <button @click="isOpen = true">RULES</button>
    <Teleport to="body">
      <div v-if="isOpen" class="modal">
        <h1>RULES</h1>
        <img :src="rulesSvg" alt="Game Rules" />
        <span @click="isOpen = false" class="material-symbols-outlined"> close </span>
      </div>
    </Teleport>
  </div>
</template>
<style scope>
.rules {
  display: flex;
  justify-content: center;
  position: absolute;
  width: 100%;
  bottom: 7.3%;
  left: 50%;
  transform: translateX(-50%);
}

.rules button {
  cursor: pointer;
  background-color: transparent;
  color: white;
  font-size: 14px;
  letter-spacing: 1.2px;
  padding-top: 3px;
  height: 42px;
  max-width: 128px;
  width: 100%;
  border: 2px solid #888fa9;
  border-radius: 8px;
}

.modal {
  background-color: white;
  position: fixed;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 92px;
  padding-right: 35px;
  padding-bottom: 60px;
  padding-left: 35px;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  z-index: 999;
}

.modal h1 {
  font-size: 32px;
}
.modal img {
  height: auto;
  max-width: 305px;
  width: 100%;
  margin-top: 110px;
}
.modal span {
  cursor: pointer;
  color: #d0d0d3;
  font-size: 32px;
  margin-top: auto;
}

@media (min-width: 650px) {
  .modal {
    border-radius: 8px;
    align-items: normal;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    padding-top: 30px;
    padding-right: 30px;
    padding-left: 30px;
    height: 415px;
    width: 400px;
  }
  .modal h1 {
    font-size: 32px;
  }
  .modal img {
    margin-top: 45px;
    align-self: center;
  }
  .modal span {
    position: absolute;
    top: 32px;
    right: 25px;
  }
  @media (min-width: 750px) {
    .rules {
      justify-content: flex-end; /* Align to the right */
      transform: none;
      left: unset;
      right: 2.3%;
      bottom: 4%;
    }
  }
}
</style>
