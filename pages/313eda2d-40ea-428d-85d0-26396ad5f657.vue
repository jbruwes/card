<template>
  <div class="inset-0 fixed bg-linear-to-r from-cyan-500 to-blue-500" un-cloak>
    <ul class="absolute inset-0 overflow-hidden">
      <li v-for="i in 10" class="absolute bg-white/20 -bottom-48 animate-[animate_25s_linear_infinite] rounded-full"
        :class="`circle${i}`"></li>
    </ul>
  </div>
  <Transition leave-active-class="animate__animated animate__fadeOut animate__slow">
    <div class="fixed inset-0 bg-radial-[125%_125%_at_50%_10%] from-black from-40% to-fuchsia-700" v-show="!show[1]"
      un-cloak></div>
  </Transition>
  <TresCanvas window-size>
    <TresPerspectiveCamera :position="[0, 0, 20]"></TresPerspectiveCamera>
    <Suspense>
      <RouterView></RouterView>
    </Suspense>
    <EffectComposer>
      <SMAA></SMAA>
      <Glitch :goWild="true" v-if="!show[0] && hasFinishLoading"></Glitch>
    </EffectComposer>
  </TresCanvas>
  <div class="fixed inset-0 flex flex-col justify-between pb-12 pt-20">
    <Transition enter-active-class="animate__animated animate__fadeInDown animate__fast" enter-from-class="animate-none"
      enter-to-class="animate-none">
      <el-button v-show="show[2] && hasFinishLoading" tag="a" size="large" href="https://bryusova.ru" target="_blank"
        rel="noopener noreferrer" round class="mx-auto shadow-xl shadow-white animate-pulse">👩 ПОМОГУ
        РАЗОБРАТЬСЯ</el-button>
    </Transition>
    <el-popover placement="bottom" title="Что такое МАК-карты" width="80%" trigger="click" effect="dark"
      popper-style="word-break: normal;" :content>
      <template #reference>
        <div v-if="!(show[0] && show[1])" class="mx-auto w-fit">
          <Transition enter-active-class="animate__animated animate__fadeInDown animate__fast"
            enter-from-class="animate-none" enter-to-class="animate-none"
            leave-active-class="animate__animated animate__fadeOutUp animate__slower" leave-from-class="animate-none"
            leave-to-class="animate-none">
            <el-button v-if="!show[0] && hasFinishLoading" color="DarkMagenta" dark :icon="QuestionFilled">УЗНАЙ О МАК
              КАРТАХ</el-button>
          </Transition>
        </div>
      </template>
    </el-popover>
    <div v-if="!(show[0] && show[1])" class="self-end mx-auto w-fit">
      <Transition enter-active-class="animate__animated animate__fadeInUp animate__fast" enter-from-class="animate-none"
        enter-to-class="animate-none" leave-active-class="animate__animated animate__fadeOutDown animate__slower"
        leave-from-class="animate-none" leave-to-class="animate-none">
        <el-button v-if="!show[0] && hasFinishLoading" class="shadow-xl shadow-cyan-500/50 animate-pulse" size="large"
          color="DarkMagenta" round dark @click="() => { if (card) show[0] = true }">👁️ НАЖМИ И УЗРИ СВОЮ КАРТУ
          ДНЯ</el-button>
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
import { reactive, provide, watch, shallowRef } from "vue";
import { cloudStorage, init } from "@telegram-apps/sdk";
import { isTMA } from "@telegram-apps/bridge";
import { ElButton, ElNotification, ElProgress, ElPopover } from "element-plus";
import { useSpeechSynthesis } from "@vueuse/core";
import { useProgress } from "@tresjs/cientos";
import { RouterView } from "vue-router";
import { QuestionFilled, PhoneFilled } from "@element-plus/icons-vue"

const show = reactive(new Array(3).fill(false)),
  title = "Карта дня 🙋‍♀️",
  message = "возвращайся завтра за новой картой",
  content = "Метафорические ассоциативные карты (МАК) — профессиональный инструмент психолога, который помогает «разговорить» Ваше подсознание. Потому что именно в подсознании и находятся ответы на все наши вопросы! Через интерпретацию изображений Вы получаете доступ к тому, что остаётся за пределами сознательного контроля.",
  { isSupported, speak } = useSpeechSynthesis(message, { lang: "ru-RU" }),
  cardNumber = shallowRef(),
  card = shallowRef(false),
  now = new Date(),
  tma = await isTMA(),// && cloudStorage.isSupported() && cloudStorage.getItem.isAvailable() && cloudStorage.setItem.isAvailable();
  { hasFinishLoading, progress } = await useProgress();

if (tma) init();

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

if (tma) {
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
  if (tma) {
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
