<template>

  <div class="inset-0 fixed bg-linear-to-r from-cyan-500 to-blue-500" v-show="show">
    <ul class="absolute inset-0 overflow-hidden">
      <li v-for="i in 10" class="absolute bg-white/20 -bottom-48 animate-[animate_25s_linear_infinite] rounded-full"
        :class="`circle${i}`"></li>
    </ul>
  </div>

  <div class="fixed inset-0 bg-radial-[125%_125%_at_50%_10%] from-black from-40% to-fuchsia-700" ref="backRef"
    v-if="hide"></div>

  <TresCanvas window-size>
    <TresPerspectiveCamera :position="[0, 0, 20]"></TresPerspectiveCamera>
    <router-view></router-view>
    <EffectComposer>
      <SMAA></SMAA>
      <Glitch :goWild="true" v-if="!show"></Glitch>
    </EffectComposer>
  </TresCanvas>

  <div class="fixed left-0 right-0 bottom-0" v-if="!show">
    <div class="w-fit mx-auto mb-12"><button @click="show = true"
        className="cursor-pointer py-3 rounded-full border-2 bg-black px-6 text-xs hover:bg-white hover:text-black font-medium text-white">НАЖМИ
        И УЗРИ СВОЮ КАРТУ ДНЯ</button>
    </div>
  </div>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";
import { EffectComposer, Glitch, SMAA } from '@tresjs/post-processing';
import { shallowRef, provide, useTemplateRef, watch } from "vue";
import gsap from "gsap";
import { retrieveRawInitData } from "@telegram-apps/sdk";

const show = shallowRef(false),
  hide = shallowRef(true),
  back = useTemplateRef("backRef");

provide("show", show);

watch(show, () => {
  gsap.to(back.value, {
    opacity: 0, duration: 2, delay: 2, onComplete: () => {
      hide.value = false;
    }
  });
});


try {
  const initDataRaw = retrieveRawInitData();
  fetch("https://localhost:3000", {
    method: "GET",
    headers: {
      Authorization: `tma ${initDataRaw}`
    },
  });
} catch(err) {
  console.log(err);
 };


</script>

<style>
.circle1 {
  width: 80px;
  height: 80px;
  left: 25%;
  animation-delay: 0s;
}

.circle2 {
  width: 20px;
  height: 20px;
  left: 10%;
  animation-delay: 2s;
  animation-duration: 12s;
}

.circle3 {
  width: 20px;
  height: 20px;
  left: 70%;
  animation-delay: 4s;
}

.circle4 {
  width: 60px;
  height: 60px;
  left: 40%;
  animation-delay: 0s;
  animation-duration: 18s;
}

.circle5 {
  width: 20px;
  height: 20px;
  left: 65%;
  animation-delay: 0s;
}

.circle6 {
  width: 110px;
  height: 110px;
  left: 75%;
  animation-delay: 3s;
}

.circle7 {
  width: 150px;
  height: 150px;
  left: 35%;
  animation-delay: 7s;
}

.circle8 {
  width: 25px;
  height: 25px;
  left: 50%;
  animation-delay: 15s;
  animation-duration: 45s;
}

.circle9 {
  width: 15px;
  height: 15px;
  left: 20%;
  animation-delay: 2s;
  animation-duration: 35s;
}

.circle10 {
  width: 150px;
  height: 150px;
  left: 85%;
  animation-delay: 0s;
  animation-duration: 11s;
}

@keyframes animate {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
  }

  100% {
    transform: translateY(-1000px) rotate(720deg);
    opacity: 0;
  }
}
</style>
