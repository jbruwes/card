<template>
  <TresCanvas window-size clear-color="black">
    <TresPerspectiveCamera :position="[0, 0, 20]"></TresPerspectiveCamera>
    <OrbitControls></OrbitControls>
    <router-view></router-view>
    <EffectComposer>
      <SMAA></SMAA>
      <Glitch :goWild="true"></Glitch>
    </EffectComposer>
  </TresCanvas>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";
import { OrbitControls } from '@tresjs/cientos';
import { EffectComposer, Glitch, SMAA } from '@tresjs/post-processing';
</script>