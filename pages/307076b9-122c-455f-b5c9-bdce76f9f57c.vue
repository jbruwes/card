<template><!---->
  <Levioso :speed="2" :rotationFactor="2" :floatFactor="2" :range="[-0.2, 0.2]">
    <TresMesh :rotation-y="Math.PI">
        <TresPlaneGeometry :args="[10, 15]"></TresPlaneGeometry>
        <TresShaderMaterial :vertex-shader :fragment-shader :uniforms :transparent="true" :depthWrite="false">
        </TresShaderMaterial>
    </TresMesh></Levioso>
</template>

<script setup lang="js">
import { TextureLoader, Vector2, FileLoader } from "three";
import { useTresContext, useLoader } from "@tresjs/core";
import { computed } from "vue";
import { Levioso } from '@tresjs/cientos';

const vertexShader = await useLoader(FileLoader, "./shaders/plane.vert"),
    fragmentShader = await useLoader(FileLoader, "./shaders/back.frag"),
    cardtemplate = { type: "t", value: new TextureLoader().load("./images/cardtemplateback4.png") },
    backtexture = { type: "t", value: new TextureLoader().load("./images/color3.jpg") },
    noise = { type: "t", value: new TextureLoader().load("./images/noise2.png") },
    skullrender = { type: "t", value: new TextureLoader().load("./images/flower3.png") },
    noiseTex = { type: "t", value: new TextureLoader().load("./images/rgbnoise2.png") },
    color = { type: "t", value: new TextureLoader().load("./images/color11.png") },
    { sizes: { width, height } } = useTresContext(),
    uniforms = computed(() => ({
        cardtemplate, backtexture, noise, skullrender, noiseTex, color,
        resolution: { value: new Vector2(width.value, height.value) },
    }));
</script>