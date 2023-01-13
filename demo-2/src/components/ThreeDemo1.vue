<template>
  <div id="container"></div>
</template>

<script setup>
import { onMounted } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
import { RGBELoader } from 'three/examples/jsm/loaders/RGBELoader';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000,
);
camera.position.set(0, 0.4, 1);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
onMounted(() => {
  document.querySelector('#container').appendChild(renderer.domElement);
});

const light = new THREE.DirectionalLight(0xffffff, 0.4);
scene.add(light);

let mixer;
let donuts;
new GLTFLoader().load('/resources/donuts.glb', (glb) => {
  scene.add(glb.scene);
  donuts = glb.scene;
  mixer = new THREE.AnimationMixer(glb.scene);
  const clips = glb.animations;
  clips.forEach(function (clip) {
    const action = mixer.clipAction(clip);
    action.loop = THREE.LoopOnce;
    action.clampWhenFinished = true; // 停在最后一帧
    action.play();
  });
});

new RGBELoader().load('/resources/sky.hdr', function (texture) {
  scene.background = texture;
  texture.mapping = THREE.EquirectangularReflectionMapping;
  scene.environment = texture;
  renderer.outputEncoding = THREE.sRGBEncoding;
  renderer.render(scene, camera);
});

function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
  if (donuts) {
    donuts.rotation.y += 0.01; // 让 donuts 旋转
  }
  if (mixer) {
    mixer.update(0.02); // 推进混合器时间并更新动画
  }
}
animate();

const controls = new OrbitControls(camera, renderer.domElement);
controls.update();
</script>

<style scoped></style>
