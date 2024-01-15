<template>
  <div ref="containerRef" class=""></div>
</template>

<script setup lang="js">
  import * as THREE from 'three';
import Stats from 'three/addons/libs/stats.module.js';
import { ImprovedNoise } from 'three/addons/math/ImprovedNoise.js';
import { onMounted, ref } from 'vue';

let container, stats;
let camera, scene, renderer;
let mesh, texture;

const worldWidth = 256, worldDepth = 256;
const clock = new THREE.Clock();

const containerRef = ref(null);

onMounted(() => {
  container = containerRef.value;

  camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 1, 10000);
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x000); // Dark blue color

  scene.fog = new THREE.FogExp2(0xf6f6f6, 0.001);

  const data = generateHeight(worldWidth, worldDepth);

  camera.position.set(100, 1600, -900);
  camera.lookAt(-400, 810, -800);

  const geometry = new THREE.PlaneGeometry(7500, 7500, worldWidth - 1, worldDepth - 1);
  geometry.rotateX(-Math.PI / 2);

  const vertices = geometry.attributes.position.array;

  for (let i = 0, j = 0, l = vertices.length; i < l; i++, j += 3) {
    vertices[j + 1] = data[i] * 10;
  }

  texture = new THREE.CanvasTexture(generateTexture(data, worldWidth, worldDepth));
  texture.wrapS = THREE.ClampToEdgeWrapping;
  texture.wrapT = THREE.ClampToEdgeWrapping;
  texture.colorSpace = THREE.SRGBColorSpace;

  mesh = new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({ map: texture }));
  scene.add(mesh);

  renderer = new THREE.WebGLRenderer();
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.setSize(window.innerWidth, window.innerHeight);
  container.appendChild(renderer.domElement);

  stats = new Stats();
  stats.showPanel(0); // 0: fps, 1: ms, 2: mb, 3+: custom
  container.appendChild(stats.dom);

  window.addEventListener('resize', onWindowResize);

  animate();
});

function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}

function generateHeight(width, height) {
  let seed = Math.PI / 4;
  window.Math.random = function () {
    const x = Math.sin(seed++) * 10000;
    return x - Math.floor(x);
  };

  const size = width * height,
    data = new Uint8Array(size);
  const perlin = new ImprovedNoise(),
    z = Math.random() * 100;
  let quality = 1;

  for (let j = 0; j < 4; j++) {
    for (let i = 0; i < size; i++) {
      const x = i % width,
        y = ~~(i / width);
      data[i] += Math.abs(perlin.noise(x / quality, y / quality, z) * quality * 1.75);
    }
    quality *= 5;
  }

  return data;
}

function generateTexture(data, width, height) {
  let context, image, imageData, shade;
  const vector3 = new THREE.Vector3(0, 0, 0);
  const sun = new THREE.Vector3(1, 1, 1);
  sun.normalize();

  const canvas = document.createElement('canvas');
  canvas.width = width;
  canvas.height = height;
  context = canvas.getContext('2d');
  context.fillStyle = '#000033';
  context.fillRect(0, 0, width, height);
  image = context.getImageData(0, 0, canvas.width, canvas.height);
  imageData = image.data;

  for (let i = 0, j = 0, l = imageData.length; i < l; i += 4, j++) {
    vector3.x = data[j - 2] - data[j + 2];
    vector3.y = 2;
    vector3.z = data[j - width * 2] - data[j + width * 2];
    vector3.normalize();
    shade = vector3.dot(sun);
    imageData[i] = 0;
    imageData[i + 1] = 25;
    imageData[i + 2] = 51;
  }

  context.putImageData(image, 0, 0);

  const canvasScaled = document.createElement('canvas');
  canvasScaled.width = width * 4;
  canvasScaled.height = height * 4;

  context = canvasScaled.getContext('2d');
  context.scale(4, 4);
  context.drawImage(canvas, 0, 0);

  image = context.getImageData(0, 0, canvasScaled.width, canvasScaled.height);
  imageData = image.data;

  for (let i = 0, l = imageData.length; i < l; i += 4) {
    const v = ~~(Math.random() * 5);
    imageData[i] += v;
    imageData[i + 1] += v;
    imageData[i + 2] += v;
  }

  context.putImageData(image, 0, 0);

  return canvasScaled;
}

function animate() {
    requestAnimationFrame(animate);

// Rotate the mesh
mesh.rotation.y -= 0.003;

// Update camera position for automatic rotation
const rotationSpeed = 0.01;
const deltaTime = clock.getDelta();
camera.rotation.y += rotationSpeed * deltaTime;

// Look at the scene's center
camera.lookAt(scene.position);

renderer.render(scene, camera);
stats.update();
}
</script>

<style>
/* Add any necessary styles here */
</style>
