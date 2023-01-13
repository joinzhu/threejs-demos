<template>
  <div id="container"></div>
</template>

<script setup>
import { onMounted } from 'vue';
import * as THREE from 'three';
import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader';
import { STLLoader } from 'three/examples/jsm/loaders/STLLoader';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  45,
  window.innerWidth / window.innerHeight,
  1,
  1000,
);
camera.position.z = 400;
camera.up.y = 400;
camera.lookAt(new THREE.Vector3(0, 0, 0));
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setClearColor(0xffffff, 1);
renderer.setSize(window.innerWidth, window.innerHeight);
onMounted(() => {
  document.querySelector('#container').appendChild(renderer.domElement);
});

const light = new THREE.DirectionalLight(0xffffff, 0.4);
scene.add(light);

let geometry = new THREE.SphereGeometry(50);
const material = new THREE.MeshBasicMaterial({ color: 0xffff00 });
const sphere = new THREE.Mesh(geometry, material);
scene.add(sphere);

renderer.render(scene, camera);

// new GLTFLoader().load('/resources/donuts.glb', (glb) => {
//   console.warn(glb.scene);
//   scene.add(glb.scene);
//   renderer.render(scene, camera);
// });

// new STLLoader().load('/resources/megaman.stl', (stl) => {
//   console.warn(stl.scene);
//   scene.add(stl.scene);
//   renderer.render(scene, camera);
// });

// new OBJLoader().load('/resources/rabbit.obj', (obj) => {
//   console.warn(obj.scene);
//   scene.add(obj.scene);
//   renderer.render(scene, camera);
// });
</script>

<style scoped></style>
