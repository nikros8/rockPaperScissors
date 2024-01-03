<scrip setup lang="ts"></scrip>
<template>
  <div class="rules">
    <button>RULES</button>
  </div>
</template>
<style scope>
.rules {
  display: flex;
  justify-content: center;
  position: absolute;
  width: 100%;
  bottom: 55px;
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
</style>
