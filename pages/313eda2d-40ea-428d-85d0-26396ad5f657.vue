<template>
  <TresCanvas window-size shadows alpha clear-color="black" >
      <router-view></router-view>
  </TresCanvas>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";

</script>