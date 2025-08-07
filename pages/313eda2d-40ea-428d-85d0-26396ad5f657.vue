<template>
  <div class="inset-0 fixed bg-linear-to-r from-cyan-500 to-blue-500" v-show="hasFinishLoading" un-cloak>
    <ul class="absolute inset-0 overflow-hidden">
      <li v-for="i in 10" class="absolute bg-white/20 -bottom-48 animate-[animate_25s_linear_infinite] rounded-full"
        :class="`circle${i}`"></li>
    </ul>
  </div>
  <Transition leave-active-class="animate__animated animate__fadeOut animate__slow">
    <div class="fixed inset-0 bg-radial-[125%_125%_at_50%_10%] from-black from-40% to-fuchsia-700"
      v-show="!show[1] && hasFinishLoading"></div>
  </Transition>
  <TresCanvas window-size v-show="hasFinishLoading">
    <TresPerspectiveCamera :position="[0, 0, 20]"></TresPerspectiveCamera>
    <Suspense>
      <RouterView></RouterView>
    </Suspense>
    <EffectComposer>
      <SMAA></SMAA>
      <Glitch :goWild="true" v-if="!show[0] && hasFinishLoading"></Glitch>
    </EffectComposer>
  </TresCanvas>
  <div class="fixed inset-0 flex flex-col justify-between pb-12 pt-12"
    :class="{ 'portrait:pt-20': tma && isFullscreen() }" un-cloak>
    <Transition enter-active-class="animate__animated animate__fadeInDown animate__fast" enter-from-class="animate-none"
      enter-to-class="animate-none">
      <el-button v-show="show[2] && hasFinishLoading" tag="a" size="large" href="https://bryusova.ru" target="_blank"
        rel="noopener noreferrer" class="mx-auto animate-pulse shadow-xl">
        <svg class="mr-2" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 14 14">
          <path fill="currentColor" fill-rule="evenodd"
            d="M3.658.162a.75.75 0 0 1 .918.53l.449 1.673a.75.75 0 1 1-1.45.389L3.129 1.08a.75.75 0 0 1 .53-.919M.072 3.755a.75.75 0 0 0 .53.918l1.673.448a.75.75 0 1 0 .388-1.448L.99 3.224a.75.75 0 0 0-.918.53m4.4 1.965a1 1 0 0 1 1.264-1.265l7.12 2.375c.92.307.908 1.612-.018 1.902l-2.026.637l2.7 2.7a1 1 0 0 1-1.415 1.414l-2.706-2.707l-.642 2.046c-.29.925-1.596.937-1.903.017zM2.066 8.755a.75.75 0 1 1-1.061-1.06L2.231 6.47a.75.75 0 0 1 1.06 1.06zm6.46-6.591a.75.75 0 0 0-1.06-1.061L6.24 2.328a.75.75 0 0 0 1.061 1.06z"
            clip-rule="evenodd" />
        </svg>ПОМОГУ РАЗОБРАТЬСЯ</el-button>
    </Transition>
    <el-popover placement="bottom" title="МАК-карта дня" width="80%" trigger="click" effect="dark"
      popper-style="word-break: normal;" :content="description">
      <template #reference>
        <div v-if="!(show[0] && show[1])" class="mx-auto w-fit">
          <Transition enter-active-class="animate__animated animate__fadeInDown animate__fast"
            enter-from-class="animate-none" enter-to-class="animate-none"
            leave-active-class="animate__animated animate__fadeOutUp animate__slower" leave-from-class="animate-none"
            leave-to-class="animate-none">
            <el-button v-if="!show[0] && hasFinishLoading" color="DarkMagenta" size="large" dark>
              <svg class="mr-2" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 14 14">
                <path fill="currentColor" fill-rule="evenodd"
                  d="M14 7A7 7 0 1 1 0 7a7 7 0 0 1 14 0M5.75 5.25A1.25 1.25 0 1 1 7 6.5a.75.75 0 0 0-.75.75v.646a.75.75 0 1 0 1.5 0a2.751 2.751 0 1 0-3.5-2.646a.75.75 0 0 0 1.5 0M8 10.5a1 1 0 1 1-2 0a1 1 0 0 1 2 0"
                  clip-rule="evenodd" />
              </svg>ОЧЕНЬ КРАТКАЯ ИНСТРУКЦИЯ</el-button>
          </Transition>
        </div>
      </template>
    </el-popover>
    <div v-if="!(show[0] && show[1])" class="self-end mx-auto w-fit">
      <Transition enter-active-class="animate__animated animate__fadeInUp animate__fast" enter-from-class="animate-none"
        enter-to-class="animate-none" leave-active-class="animate__animated animate__fadeOutDown animate__slower"
        leave-from-class="animate-none" leave-to-class="animate-none">
        <el-button v-if="!show[0] && hasFinishLoading" class="shadow-xl shadow-cyan-500/50 animate-pulse" size="large"
          color="DarkMagenta" dark @click="() => { if (card) show[0] = true }">
          <svg class="mr-2" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 14 14">
            <path fill="currentColor" fill-rule="evenodd"
              d="M4.317 1.001a.625.625 0 0 1 .784.408l.485 1.533c.44-.104.912-.162 1.416-.162s.975.058 1.415.161l.481-1.531a.625.625 0 1 1 1.193.375l-.493 1.567a7 7 0 0 1 2.037 1.472l1.298-1.3a.625.625 0 0 1 .885.883l-1.375 1.377c.291.4.518.778.686 1.088a8 8 0 0 1 .352.748a2 2 0 0 1 .04.12l.01.044a.6.6 0 0 1 0 .239a1 1 0 0 1-.025.093l-.025.072q-.029.073-.075.181a8 8 0 0 1-.277.566a9.4 9.4 0 0 1-1.134 1.654c-1.027 1.197-2.664 2.439-4.993 2.439s-3.966-1.242-4.994-2.439A9.4 9.4 0 0 1 .875 8.935a8 8 0 0 1-.352-.747a2 2 0 0 1-.04-.12l-.01-.045c-.002-.013-.011-.06-.011-.12s.009-.106.011-.118a1 1 0 0 1 .025-.093q.011-.037.025-.072q.027-.074.074-.181c.063-.144.155-.339.278-.567c.17-.317.404-.704.704-1.112L.19 4.414a.625.625 0 1 1 .87-.898l1.329 1.288a7 7 0 0 1 2.016-1.451l-.496-1.567A.625.625 0 0 1 4.317 1M1.976 8.343a7 7 0 0 1-.218-.44a8.2 8.2 0 0 1 1.198-1.87C3.845 4.999 5.165 4.03 7.002 4.03s3.157.97 4.045 2.003a8.2 8.2 0 0 1 1.198 1.87c-.05.111-.121.262-.217.44a8.2 8.2 0 0 1-.98 1.432c-.889 1.034-2.21 2.003-4.046 2.003s-3.157-.97-4.046-2.003a8.2 8.2 0 0 1-.98-1.432m2.76-.47c.01-1.244 1.036-2.24 2.28-2.24s2.256.996 2.248 2.24c-.009 1.242-1.036 2.238-2.28 2.238s-2.256-.996-2.247-2.239"
              clip-rule="evenodd" />
          </svg>НАЖМИ И УЗРИ СВОЮ КАРТУ ДНЯ</el-button>
      </Transition>
    </div>
  </div>
  <Teleport to="body">
    <div v-if="!hasFinishLoading"
      style="position:fixed;display:flex;justify-content:center;align-items:center;inset: 0px;">
      <el-progress type="circle" :percentage="progress"></el-progress>
    </div>
  </Teleport>
</template>

<script setup lang="js">
import { TresCanvas } from "@tresjs/core";
import { EffectComposer, Glitch, SMAA } from '@tresjs/post-processing';
import { reactive, provide, watch, shallowRef, inject } from "vue";
import { cloudStorage, init, isFullscreen, mountViewport } from "@telegram-apps/sdk";
import { isTMA } from "@telegram-apps/bridge";
import { ElButton, ElNotification, ElProgress, ElPopover } from "element-plus";
import { useSpeechSynthesis } from "@vueuse/core";
import { useProgress } from "@tresjs/cientos";
import { RouterView } from "vue-router";
import bridge from "@vkontakte/vk-bridge";

const runTimeouted = (promise) => new Promise((resolve) => {
  const timeoutId = setTimeout(() => resolve(false), 100);
  promise
    .then(() => {
      clearTimeout(timeoutId);
      resolve(true);
    })
    .catch(() => {
      clearTimeout(timeoutId);
      resolve(false);
    });
}),
/*  isVKMA = () => new Promise((resolve) => {
    const timeoutId = setTimeout(() => resolve(false), 100);
    bridge.send("VKWebAppInit")
      .then(() => {
        clearTimeout(timeoutId);
        resolve(true);
      })
      .catch(() => {
        clearTimeout(timeoutId);
        resolve(false);
      });
  }),
  mountAvailableViewport = () => new Promise((resolve) => {
    const timeoutId = setTimeout(() => resolve(false), 100);
    mountViewport()
      .then(() => {
        clearTimeout(timeoutId);
        resolve(true);
      })
      .catch(() => {
        clearTimeout(timeoutId);
        resolve(false);
      });
  }),*/
  { id } = defineProps(["id"]),
  pages = inject("pages"),
  { title, description } = pages[id],
  show = reactive(new Array(3).fill(false)),
  message = "возвращайся завтра за новой картой",
  { isSupported, speak } = useSpeechSynthesis(message, { lang: "ru-RU" }),
  cardNumber = shallowRef(),
  card = shallowRef(false),
  now = new Date(),
  [vkma, tma] = await Promise.all([
  //isVKMA(), 
  runTimeouted(bridge.send("VKWebAppInit")),
  isTMA()]),
  { hasFinishLoading, progress } = await useProgress();

if (tma) {
  init();
  if (mountViewport.isAvailable())  runTimeouted(mountViewport());
  //mountAvailableViewport();
}

let cardDate;

provide("show", show);
provide("card", card);

watch([() => show[2], hasFinishLoading], ([value1, value2]) => {
  if (value1 && value2) {
    ElNotification({ title, message, showClose: false, position: "bottom-left", duration: 0 });
    if (isSupported) speak();
  }
});

/*
const initDataRaw = retrieveRawInitData();
fetch("https://localhost:3000", {
  headers: {
    Authorization: `tma ${initDataRaw}`
  },
});
*/

if (vkma) {
  const { keys } = await bridge.send("VKWebAppStorageGet", { keys: ["card-number", "card-date"] });
  cardNumber.value = parseInt(keys[0].value);
  cardDate = new Date(keys[1].value);
} else if (tma) {
  cardNumber.value = parseInt(await cloudStorage.getItem("card-number"));
  cardDate = new Date(await cloudStorage.getItem("card-date"));
} else {
  cardNumber.value = parseInt(localStorage.getItem("card-number"));
  cardDate = new Date(localStorage.getItem("card-date"));
}
if (
  isNaN(cardNumber.value) ||
  isNaN(cardDate.valueOf()) ||
  !(
    now.getFullYear() === cardDate.getFullYear() &&
    now.getMonth() === cardDate.getMonth() &&
    now.getDate() === cardDate.getDate()
  )
) cardNumber.value = Math.floor(Math.random() * 100) + 1;
else show.fill(true);

provide("cardNumber", cardNumber);

watch(() => show[0], () => {
  if (vkma) {
    bridge.send("VKWebAppStorageSet", { key: "card-number", value: cardNumber.value.toString() });
    bridge.send("VKWebAppStorageSet", { key: "card-date", value: new Date().toISOString() });
  } else if (tma) {
    cloudStorage.setItem("card-number", cardNumber.value);
    cloudStorage.setItem("card-date", new Date().toISOString());
  } else {
    localStorage.setItem("card-number", cardNumber.value);
    localStorage.setItem("card-date", new Date().toISOString());
  }
});

</script>

<style>
@import "./node_modules/element-plus/dist/index.css";
@import "./node_modules/animate.css/animate.min.css";

.el-progress--circle .el-progress__text,
.el-progress--dashboard .el-progress__text {
  left: 0;
  margin: 0;
  position: absolute;
  text-align: center;
  top: 50%;
  transform: translateY(-50%);
  width: 100%;
}

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
