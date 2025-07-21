<template>
  <Levioso :speed="2" :rotationFactor="2" :floatFactor="2" :range="[-0.2, 0.2]">
    <TresMesh>
      <TresPlaneGeometry :args="[10, 15]"></TresPlaneGeometry>
      <TresShaderMaterial :vertex-shader :fragment-shader :uniforms :transparent="true" :depthWrite="false">
      </TresShaderMaterial>
    </TresMesh>
  </Levioso>
  <TresEffectComposer ref="composerRef" :args="[renderer]" :set-size="[width, height]" :renderToScreen="false">
    <TresRenderPass :args="[sceneRTT, cameraRTT]" attach="passes-0" />
    <TresUnrealBloomPass :args="[undefined, 0.5, 2.29, 0]" attach="passes-1" />
  </TresEffectComposer>
</template>

<script setup lang="js">
import { TextureLoader, Vector2, Scene, ShaderMaterial, Vector3, SphereGeometry, MeshBasicMaterial, Mesh, Object3D, FileLoader, Color, PerspectiveCamera } from "three";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { OutputPass } from 'three/addons/postprocessing/OutputPass.js';
import { OBJLoader } from "three/addons/loaders/OBJLoader.js";
import { mergeVertices } from 'three/addons/utils/BufferGeometryUtils.js';
import { extend, useLoop, useTresContext, useLoader } from "@tresjs/core";
import { useTemplateRef, computed } from "vue";
import { Levioso } from '@tresjs/cientos';

extend({ EffectComposer, OutputPass, UnrealBloomPass, RenderPass });

const composer = useTemplateRef("composerRef"),
  vertexShader = await useLoader(FileLoader, "./shaders/plane.vert"),
  fragmentShader = await useLoader(FileLoader, "./shaders/front.frag"),
  mesh2 = await useLoader(OBJLoader, "./models/skull5.obj"),
  { renderer, sizes: { width, height }, camera } = useTresContext(),
  sceneRTT = new Scene(),
  cameraRTT = new PerspectiveCamera(50, 10 / 15, 30,
    100),
  { onBeforeRender } = useLoop(),
  skullmaterial = new ShaderMaterial({
    uniforms: {
      time: { type: "f", value: 0.0 },
      color1: { value: new Vector3(65, 0, 170) },
      color0: { value: new Vector3(197, 81, 245) },
      resolution: { value: new Vector2(width.value, height.value) }
    },
    fragmentShader: await useLoader(FileLoader, "./shaders/skull.frag"),
    vertexShader: await useLoader(FileLoader, "./shaders/skull.vert")
  }),
  cardtemplate = { type: "t", value: new TextureLoader().load("./images/cardtemplateback4.png") },
  backtexture = { type: "t", value: new TextureLoader().load("./images/color3.jpg") },
  noise = { type: "t", value: new TextureLoader().load("./images/noise2.png") },
  noiseTex = { type: "t", value: new TextureLoader().load("./images/rgbnoise2.png") },
  color = { type: "t", value: new TextureLoader().load("./images/color11.png") },
  spheregeo = new SphereGeometry(1.5, 32, 32),
  basicmat = new MeshBasicMaterial({ color: 0x00ffff }),
  eye = new Mesh(spheregeo, basicmat),
  eye2 = new Mesh(spheregeo, basicmat),
  modelgroup = new Object3D(),
  uniforms = computed(() => ({
    cardtemplate, backtexture, noise, noiseTex, color,
    resolution: { value: new Vector2(width.value, height.value) },
    skullrender: { type: "t", value: composer.value?.readBuffer.texture }
  }));

onBeforeRender(({ clock: { oldTime } }) => {
  //modelgroup.quaternion.copy(front.value.parent.quaternion)
  //  modelgroup.rotation.set(-camera.value.rotation._x + modelgroup.rotation.x, -camera.value.rotation._y + modelgroup.rotation.y, 0);
  modelgroup.rotation.set(-camera.value.rotation._x, -camera.value.rotation._y, 0);
  skullmaterial.uniforms.time.value = oldTime / 4000;
  composer.value.render();
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