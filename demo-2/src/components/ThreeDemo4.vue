<template>
  <div id="container"></div>
</template>

<script setup>
import { onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js'

const tipsList = [
  {
    position: { x: -200, y: -4, z: -147 },
    content: {
      title: '进入厨房',
      text: '',
      image: 1,
      showTip: false,
      showTitle: true
    }
  },
  {
    position: { x: -100, y: 0, z: -231 },
    content: {
      title: '信息点2',
      text: '77989',
      showTip: true,
      showTitle: false
    }
  },
  {
    position: { x: 150, y: -50, z: -198 },
    content: {
      title: '信息点3',
      text: 'qwdcz',
      showTip: true,
      showTitle: false
    }
  },
  {
    position: { x: 210, y: 11, z: -140 },
    content: {
      title: '信息点4',
      text: '大豆食心虫侦察十大大苏打大大大大大大大',
      showTip: true,
      showTitle: false
    }
  },
  {
    position: { x: 208, y: -12, z: 140 },
    content: {
      title: '信息点5',
      text: 'eq',
      showTip: true,
      showTitle: false
    }
  },
  {
    position: { x: 86, y: -9, z: 236 },
    content: {
      title: '进入房间',
      text: '',
      showTip: false,
      showTitle: true
    }
  }
]

onMounted(() => {
  domContainer = document.getElementById('container')
  initScene()
  initAxesHelper()
  initCamera()
  initControls()
  initContent()
  initRenderer()
  render()
})

const width = window.innerWidth
const height = window.innerHeight

let scene, camera, renderer, sphere, tipsSpriteList, controls, domContainer, timer

const initScene = () => {
  scene = new THREE.Scene()
  scene.background = new THREE.Color(0xffffff)
}

const initAxesHelper = () => {
  const axesHelper = new THREE.AxesHelper(50)
  scene.add(axesHelper)
}

const initCamera = () => {
  camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000)
  camera.position.set(50, 0, 40)
}

const initControls = () => {
  controls = new OrbitControls(camera, domContainer)
  controls.minDistance = 1
  controls.maxDistance = 100
  controls.enablePan = false
}

const initContent = () => {
  let sphereGeometry = new THREE.SphereGeometry(16, 50, 50)
  sphereGeometry.scale(16, 16, -16)
  let texture = new THREE.TextureLoader().load('/resources/livingRoom.jpg')
  let sphereMaterial = new THREE.MeshBasicMaterial({ map: texture })
  sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)
  scene.add(sphere)
  addTipsSprite()
}

const addTipsSprite = () => {
  let tipTexture = new THREE.TextureLoader().load('/resources/tip.png')
  let material = new THREE.SpriteMaterial({ map: tipTexture })
  tipsSpriteList = []
  tipsList.forEach(item => {
    let sprite = new THREE.Sprite(material)
    sprite.scale.set(10, 10, 10)
    sprite.position.set(item.position.x, item.position.y, item.position.z)
    sprite.content = item.content
    tipsSpriteList.push(sprite)
    scene.add(sprite)
  })
}

const initRenderer = () => {
  renderer = new THREE.WebGLRenderer()
  renderer.setSize(width, height)
  renderer.setClearColor(0x101010, 1)
  domContainer.appendChild(renderer.domElement)
}

const render = () => {
  controls.update()
  renderer.render(scene, camera)
  renderer.sortObjects = false
  timer = requestAnimationFrame(render)
}
</script>

<style scoped>
#container {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
</style>
