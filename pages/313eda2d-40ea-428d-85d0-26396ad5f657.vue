<template>
  <TresCanvas window-size clear-color="black" >
      <router-view></router-view>
  </TresCanvas>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";
</script>