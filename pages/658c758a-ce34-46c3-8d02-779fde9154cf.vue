<template>
    <template>
        <TresEffectComposer ref="composer" :args="[renderer]" :set-size="[sizes.width.value, sizes.height.value]">
            <TresRenderPass :args="[scene, camera]" attach="passes-0" />
            <TresUnrealBloomPass :args="[undefined, 0.5, 0.1, 0]" attach="passes-1" />
            <TresOutputPass attach="passes-2" :set-size="[sizes.width.value, sizes.height.value]" />
        </TresEffectComposer>
    </template>
</template>

<script setup>
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { OutputPass } from "three/addons/postprocessing/OutputPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { extend, useLoop, useTres } from "@tresjs/core";
import { shallowRef } from "vue";

extend({ EffectComposer, OutputPass, UnrealBloomPass, RenderPass });
const { renderer, scene, camera, sizes } = useTres();
const composer = shallowRef();

useLoop().render(() => {
    if (composer.value) {
        composer.value.render()
    }
});
</script>