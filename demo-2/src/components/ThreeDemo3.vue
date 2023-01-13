<template>
  <div id="container"></div>
  <div class="controls-box">
    <section>
      <el-row v-for="(v, i) in properties" :key="i">
        <div>
          <el-col :span="8">
            <span class="vertice-span">{{ v.name }}</span>
          </el-col>
          <el-col :span="13">
            <el-slider
              v-model="v.value"
              :min="v.min"
              :max="v.max"
              :step="v.step"
              :format-tooltip="formatTooltip"
            ></el-slider>
          </el-col>
          <el-col :span="3">
            <span class="vertice-span">{{ v.value }}</span>
          </el-col>
        </div>
      </el-row>
    </section>
  </div>
</template>

<script setup>
import { onMounted, reactive } from 'vue'
import * as THREE from 'three'
import * as SceneUtils from 'three/examples/jsm/utils/SceneUtils'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

const properties = reactive({
  radius: { name: 'radius', value: 8, min: 0, max: 40, step: 1 },
  widthSegments: { name: 'widthSeg', value: 10, min: 0, mix: 20, step: 1 },
  heightSegments: { name: 'heightSeg', value: 10, min: 0, max: 20, step: 1 },
  phiStart: { name: 'phiStart', value: 0, min: 0, max: Math.PI * 2 },
  phiLength: { name: 'phiLength', value: Math.PI * 2, min: 0, max: Math.PI * 2, step: 0.1 },
  thetaStart: { name: 'thetaStart', value: 0, min: 0, max: Math.PI * 2, step: 0.1 },
  thetaLength: { name: 'thetaLength', value: Math.PI, min: 0, max: Math.PI * 2, step: 0.1 }
})

const formatTooltip = val => val

onMounted(() => {
  init()
})

const init = () => {
  createScene()
  createMesh()
  createLight()
  createCamera()
  createRender()
  createControls()
  render()
}

let scene, camera, renderer, controls, mesh

const createScene = () => {
  scene = new THREE.Scene()
}

const createMesh = () => {
  let keys = Object.keys(properties)
  let config = keys.map(k => {
    return properties[k].value
  })
  const geometry = new THREE.SphereGeometry(...config)
  const meshMaterial = new THREE.MeshNormalMaterial({ side: THREE.DoubleSide })
  const wireFrameMat = new THREE.MeshBasicMaterial({ wireframe: true })
  mesh = SceneUtils.createMultiMaterialObject(geometry, [meshMaterial, wireFrameMat])
  scene.add(mesh)
}

const createLight = () => {
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.1)
  scene.add(ambientLight)
  const spotLight = new THREE.SpotLight(0xffffff)
  spotLight.position.set(-40, 60, -10)
  spotLight.castShadow = true
  scene.add(spotLight)
}

const createCamera = () => {
  const element = document.getElementById('container')
  const width = element.clientWidth
  const height = element.clientWidth
  const k = width / height
  camera = new THREE.PerspectiveCamera(35, k, 0.1, 1000)
  camera.position.set(-80, 60, 40)
  camera.lookAt(new THREE.Vector3(10, 0, 0))
  scene.add(camera)
}

const createRender = () => {
  const element = document.getElementById('container')
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(element.clientWidth, element.clientHeight)
  renderer.shadowMap.enabled = true
  renderer.shadowMap.type = THREE.PCFShadowMap
  renderer.setClearColor(0x3f3f3f, 1)
  element.appendChild(renderer.domElement)
}

const updateFun = () => {
  const tempRotationY = mesh.rotation.y
  scene.remove(mesh)
  createMesh()
  mesh.rotation.y += tempRotationY + 0.01
}

const render = () => {
  updateFun()
  renderer.render(scene, camera)
  requestAnimationFrame(render)
}

const createControls = () => {
  controls = new OrbitControls(camera, renderer.domElement)
}
</script>

<style scoped>
#container {
  position: absolute;
  width: 1000px;
  height: 1000px;
  top: 0;
  left: 0;
}

.controls-box {
  position: absolute;
  right: 5px;
  top: 5px;
  width: 300px;
  padding: 10px;
  background-color: #fff;
  border: 1px solid #c3c3c3;
}
.vertice-span {
  line-height: 38px;
  padding: 0 2px 0 10px;
}
</style>
