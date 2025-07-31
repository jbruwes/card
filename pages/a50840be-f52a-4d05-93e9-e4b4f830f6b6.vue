<template>
  <Suspense>
    <TresGroup ref="cardRef">
      <Levioso :speed="5">
        <TresMesh v-if="!show[1]">
          <TresPlaneGeometry :args="[7, 10]"></TresPlaneGeometry>
          <TresShaderMaterial :vertex-shader :fragment-shader="fragmentShaderFront" :uniforms="uniformsFront"
            :transparent="true" :depthWrite="false">
          </TresShaderMaterial>
        </TresMesh>
        <Image :texture="texture" :radius="0.5" :transparent="true" :scale="[7, 10]" v-else></Image>
        <TresMesh :rotation-y="Math.PI">
          <TresPlaneGeometry :args="[7, 10]"></TresPlaneGeometry>
          <TresShaderMaterial :vertex-shader :fragment-shader="fragmentShaderBack" :uniforms="uniformsBack"
            :transparent="true" :depthWrite="false">
          </TresShaderMaterial>
        </TresMesh>
      </Levioso>
    </TresGroup>
  </Suspense>
  <TresEffectComposer ref="composerRef" :args="[renderer]" :set-size="[width, height]" :renderToScreen="false">
    <TresRenderPass :args="[sceneRTT, cameraRTT]" attach="passes-0"></TresRenderPass>
    <TresUnrealBloomPass :args="[undefined, 0.5, 2.29, 0]" attach="passes-1"></TresUnrealBloomPass>
  </TresEffectComposer>
</template>

<script setup lang="js">
import { TextureLoader, Vector2, Scene, ShaderMaterial, Vector3, SphereGeometry, MeshBasicMaterial, Mesh, Object3D, FileLoader, Color, PerspectiveCamera } from "three";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { OutputPass } from "three/addons/postprocessing/OutputPass.js";
import { OBJLoader } from "three/addons/loaders/OBJLoader.js";
import { mergeVertices } from 'three/addons/utils/BufferGeometryUtils.js';
import { extend, useLoop, useTresContext, useLoader } from "@tresjs/core";
import { useTemplateRef, computed, inject, watch, shallowRef } from "vue";
import { Levioso, Image } from "@tresjs/cientos";
import gsap from "gsap";

extend({ EffectComposer, UnrealBloomPass, RenderPass, OutputPass });

const cardNumber = inject("cardNumber"),
  texture = shallowRef(new TextureLoader().load(`./images/deck1/Star${cardNumber.value}.jpg`)),
  show = inject("show"),
  composer = useTemplateRef("composerRef"),
  card = useTemplateRef("cardRef"),
  injectedCard = inject("card"),
  { renderer, sizes: { width, height } } = useTresContext(),
  sceneRTT = new Scene(),
  cameraRTT = new PerspectiveCamera(50, 10 / 15, 30,
    100),
  { onBeforeRender } = useLoop(),
  spheregeo = new SphereGeometry(1.5, 32, 32),
  basicmat = new MeshBasicMaterial({ color: 0x00ffff }),
  eye = new Mesh(spheregeo, basicmat),
  eye2 = new Mesh(spheregeo, basicmat),
  modelgroup = new Object3D(),
  cardtemplate = { type: "t", value: new TextureLoader().load("./images/cardtemplateback4.png") },
  backtexture = { type: "t", value: new TextureLoader().load("./images/color3.jpg") },
  noise = { type: "t", value: new TextureLoader().load("./images/noise2.png") },
  noiseTex = { type: "t", value: new TextureLoader().load("./images/rgbnoise2.png") },
  color = { type: "t", value: new TextureLoader().load("./images/color11.png") },
  skullrender = { type: "t", value: new TextureLoader().load("./images/flower3.png") },
  uniformsFront = computed(() => ({
    cardtemplate, backtexture, noise, noiseTex, color,
    resolution: { value: new Vector2(width.value, height.value) },
    skullrender: { type: "t", value: composer.value?.readBuffer.texture }
  })),
  uniformsBack = computed(() => ({
    cardtemplate, backtexture, noise, skullrender, noiseTex, color,
    resolution: { value: new Vector2(width.value, height.value) },
  })),
  vertexShader = await useLoader(FileLoader, "./shaders/plane.vert"),
  fragmentShaderFront = await useLoader(FileLoader, "./shaders/front.frag"),
  fragmentShaderBack = await useLoader(FileLoader, "./shaders/back.frag"),
  mesh2 = await useLoader(OBJLoader, "./models/skull5.obj"),
  skullmaterial = new ShaderMaterial({
    uniforms: {
      time: { type: "f", value: 0.0 },
      color1: { value: new Vector3(65, 0, 170) },
      color0: { value: new Vector3(197, 81, 245) },
      resolution: { value: new Vector2(width.value, height.value) }
    },
    fragmentShader: await useLoader(FileLoader, "./shaders/skull.frag"),
    vertexShader: await useLoader(FileLoader, "./shaders/skull.vert")
  });


watch(() => show[0], () => {
  gsap.timeline().to(card.value.rotation, {
    duration: 2, y: Math.PI, onComplete: () => {
      show[1] = true;
    }
  }).to(card.value.rotation, {
    duration: 2, y: 2 * Math.PI, onComplete: () => {
      show[2] = true;
    }
  });
});

watch(card, () => {
  injectedCard.value = true;
}, { once: true });

onBeforeRender(({ clock: { oldTime } }) => {
  if (!show[1]) {
    skullmaterial.uniforms.time.value = oldTime / 4000;
    composer.value.render();
  }
});

cameraRTT.position.z = 35;
sceneRTT.background = new Color(0x000000);
eye.position.set(-2.2, 0.8, -6.6);
eye2.position.set(2.2, 0.8, -6.6);
modelgroup.add(eye);
modelgroup.add(eye2);
mesh2.position.set(0, 3, -10);
mesh2.rotation.set(Math.PI, 0, Math.PI);
mesh2.children.forEach((val) => {
  val.traverse(function (child) {
    child.geometry = mergeVertices(child.geometry);
    child.geometry.computeVertexNormals();
    child.material = skullmaterial;
  });
});
mesh2.scale.set(8, 8, 8);
modelgroup.add(mesh2);
sceneRTT.add(modelgroup);
</script>