<template>

  <div class="inset-0 fixed bg-linear-to-r from-cyan-500 to-blue-500">
    <ul class="absolute inset-0 overflow-hidden">
      <li v-for="i in 10" class="absolute bg-white/20 -bottom-48 animate-[animate_25s_linear_infinite] rounded-full"
        :class="`circle${i}`"></li>
    </ul>
  </div>

  <div class="fixed inset-0 bg-radial-[125%_125%_at_50%_10%] from-black from-40% to-fuchsia-700" ref="backRef"
    v-if="!show[2]"></div>

  <TresCanvas window-size>
    <TresPerspectiveCamera :position="[0, 0, 20]"></TresPerspectiveCamera>
    <router-view></router-view>
    <EffectComposer>
      <SMAA></SMAA>
      <Glitch :goWild="true" v-if="!show[0]"></Glitch>
    </EffectComposer>
  </TresCanvas>
  <Transition name="custom-classes" enter-active-class="animate__animated animate__tada"
    leave-active-class="animate__animated animate__bounceOutRight">
    <div class="fixed left-0 right-0 bottom-0" v-show="!show[0]">
      <div class="w-fit mx-auto mb-12"><el-button size="large" color="DarkMagenta" round dark
          @click="show[0] = true">НАЖМИ И УЗРИ СВОЮ КАРТУ ДНЯ</el-button></div>
    </div>
  </Transition>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";
import { EffectComposer, Glitch, SMAA } from '@tresjs/post-processing';
import { reactive, provide, useTemplateRef, watch, shallowRef } from "vue";
import gsap from "gsap";
import { retrieveRawInitData } from "@telegram-apps/sdk";
import { isTMA } from "@telegram-apps/bridge";
import { useCookies } from "@vueuse/integrations/useCookies";
import { ElButton, ElNotification } from "element-plus";
import { useSpeechSynthesis } from "@vueuse/core";

const show = reactive(new Array(3).fill(false)),
  back = useTemplateRef("backRef"),
  { get, set } = useCookies(["card"]),
  title = "возвращайся завтра за новой картой",
  { isSupported, speak } = useSpeechSynthesis(title, { lang: 'ru-Ru' }),
  cardNumber = shallowRef();

provide("show", show);
provide("cardNumber", cardNumber);

watch(() => show[2], () => {
  ElNotification.success({ title, showClose: false, });
  if (isSupported) speak();
});

if (await isTMA()) {
  const initDataRaw = retrieveRawInitData();
  fetch("https://localhost:3000", {
    headers: {
      Authorization: `tma ${initDataRaw}`
    },
  });
} else {
  cardNumber.value = parseInt(get("card"));
  if (isNaN(cardNumber.value)) cardNumber.value = Math.floor(Math.random() * 100) + 1;
  else show.fill(true);
}

watch(() => show[0], () => {
  const now = new Date();
  set("card", cardNumber.value, { maxAge: 24 * 60 * 60 - now.getHours() * 60 * 60 - now.getMinutes() * 60 - now.getSeconds() });
  gsap.to(back.value, {
    opacity: 0, duration: 2, delay: 2, onComplete: () => {
      show[2] = true;
    }
  });
});

</script>

<style>
@import "./node_modules/element-plus/dist/index.css";
@import "./node_modules/animate.css/animate.min.css";

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
