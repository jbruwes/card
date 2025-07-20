<template>
  <Levioso :speed="2" :rotationFactor="2" :floatFactor="2" :range="[-0.2, 0.2]">
    <TresMesh ref="frontRef">
      <TresPlaneGeometry :args="[20, 30]"></TresPlaneGeometry>
      <TresShaderMaterial :vertex-shader="vert" :fragment-shader="fragPlane" :uniforms="frontuniforms"
        :transparent="true" :depthWrite="false">
      </TresShaderMaterial>
    </TresMesh>
    <TresMesh :rotation-y="Math.PI">
      <TresPlaneGeometry :args="[20, 30]"></TresPlaneGeometry>
      <TresShaderMaterial :vertex-shader="vert" :fragment-shader="fragPlaneback" :uniforms="backuniforms"
        :transparent="true" :depthWrite="false">
      </TresShaderMaterial>
    </TresMesh>
  </Levioso>
  <TresEffectComposer ref="composerRef" :args="[renderer]" :set-size="[sizes.width.value, sizes.height.value]"
    :renderToScreen="false">
    <TresRenderPass :args="[sceneRTT, cameraRTT]" attach="passes-0" />
    <TresUnrealBloomPass :args="[undefined, 0.5, 2.29, 0]" attach="passes-1" />
  </TresEffectComposer>
  <EC>
    <SMAA></SMAA>
    <Glitch :goWild="true"></Glitch>
  </EC>
</template>

<script setup lang="js">
import { EffectComposer as EC, Glitch, SMAA } from '@tresjs/post-processing'
import { Levioso } from '@tresjs/cientos';
import { TextureLoader, Vector2, PerspectiveCamera, Scene, ShaderMaterial, Vector3, SphereGeometry, MeshBasicMaterial, Mesh, Object3D, FileLoader, Color, Box3 } from "three";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { OutputPass } from 'three/addons/postprocessing/OutputPass.js';
import { OBJLoader } from "three/addons/loaders/OBJLoader.js";
import { mergeVertices } from 'three/addons/utils/BufferGeometryUtils.js';
import { extend, useLoop, useTresContext, useLoader } from "@tresjs/core";
import { useTemplateRef, onMounted, watch } from "vue";
import { useWindowSize } from "@vueuse/core";

extend({ EffectComposer, OutputPass, UnrealBloomPass, RenderPass });

const { width, height } = useWindowSize(),
  composer = useTemplateRef("composerRef"),
  front = useTemplateRef("frontRef"),
  vert = await useLoader(FileLoader, "./shaders/plane.vert"),
  fragPlane = await useLoader(FileLoader, "./shaders/front.frag"),
  fragmentShader = await useLoader(FileLoader, "./shaders/skull.frag"),
  vertexShader = await useLoader(FileLoader, "./shaders/skull.vert"),
  fragPlaneback = await useLoader(FileLoader, "./shaders/back.frag"),
  { renderer, sizes, camera } = useTresContext(),
  sceneRTT = new Scene(),
  { onBeforeRender } = useLoop(),
  cameraRTT = new PerspectiveCamera(50, sizes.width.value / sizes.height.value),
  resolution = { value: new Vector2(sizes.width.value, sizes.height.value) },
  time = { type: "f", value: 0.0 },
  color1 = { value: new Vector3(65, 0, 170) },
  color0 = { value: new Vector3(197, 81, 245) },
  uniforms = { time, color1, color0, resolution },
  skullmaterial = new ShaderMaterial({ uniforms, fragmentShader, vertexShader }),
  cardtemplate = { type: "t", value: new TextureLoader().load("./images/cardtemplateback4.png") },
  backtexture = { type: "t", value: new TextureLoader().load("./images/color3.jpg") },
  noise = { type: "t", value: new TextureLoader().load("./images/noise2.png") },
  noiseTex = { type: "t", value: new TextureLoader().load("./images/rgbnoise2.png") },
  color = { type: "t", value: new TextureLoader().load("./images/color11.png") },
  skullrender = { type: "t", value: new TextureLoader().load("./images/flower3.png") },
  frontuniforms = { cardtemplate, backtexture, noise, resolution, noiseTex, color },
  backuniforms = { cardtemplate, backtexture, noise, skullrender, resolution, noiseTex, color },
  spheregeo = new SphereGeometry(1.5, 32, 32),
  basicmat = new MeshBasicMaterial({ color: 0x00ffff }),
  eye = new Mesh(spheregeo, basicmat),
  eye2 = new Mesh(spheregeo, basicmat),
  modelgroup = new Object3D(),
  mesh2 = await useLoader(OBJLoader, "./models/skull5.obj");

onMounted(() => {
  frontuniforms.skullrender = { type: "t", value: composer.value.readBuffer.texture };
});

onBeforeRender(({ clock: { oldTime } }) => {
  modelgroup.quaternion.copy(front.value.parent.quaternion)
  modelgroup.rotation.set(-camera.value.rotation._x + modelgroup.rotation.x, -camera.value.rotation._y + modelgroup.rotation.y, 0);
  skullmaterial.uniforms.time.value = oldTime / 4000;
  composer.value.render();
});

watch([width, height], ([newWidth, newHeight]) => {
  console.log(cameraRTT);
//  cameraRTT.aspect = sizes.width.value / sizes.height.value;
//  cameraRTT.updateProjectionMatrix();
//  cameraRTT.position.set(0, -3, 20);
});

sceneRTT.background = new Color(0x000000);
cameraRTT.position.set(0, -3, 20);
eye.position.set(-2.2, -2.2, -6.6);
eye2.position.set(2.2, -2.2, -6.6);
modelgroup.add(eye);
modelgroup.add(eye2);
mesh2.position.set(0, 0, -10);
mesh2.rotation.set(Math.PI, 0, Math.PI);
mesh2.children.forEach((val) => {
  val.traverse(function (child) {
    child.geometry = mergeVertices(child.geometry);
    child.material = skullmaterial;
    child.verticesNeedUpdate = true;
    child.normalsNeedUpdate = true;
    child.uvsNeedUpdate = true;
    child.geometry.computeVertexNormals();
  });
});
mesh2.scale.set(8, 8, 8);
modelgroup.add(mesh2);
sceneRTT.add(modelgroup);
</script>