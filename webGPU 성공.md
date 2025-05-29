![](src/Pasted%20image%2020250529154736.png)
  ```html
  <!DOCTYPE html>

  

<html lang="en">

  

<head>

  

<meta charset="utf-8" />

  

<title>Three.js WebGPU Test - 무지개빛 큐브</title>

  

<style>

  

body { margin: 0; overflow: hidden; }

  

</style>

  

  

<!-- Import map for CDN -->

  

<script type="importmap">

  

{

  

"imports": {

  

"three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js",

  

"three/addons/": "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/"

  

}

  

}

  

</script>

  

</head>

  

  

<body>

  

<script type="module">

  

import * as THREE from 'three';

  

import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

  

import WebGPURenderer from 'three/addons/renderers/webgpu/WebGPURenderer.js'; // ✅ default import

  

  

// WebGPU 지원 확인

  

function isWebGPUSupported() {

  

return !!navigator.gpu;

  

}

  

  

if (isWebGPUSupported()) {

  

const renderer = new WebGPURenderer();

  

renderer.setSize(window.innerWidth, window.innerHeight);

  

document.body.appendChild(renderer.domElement);

  

  

const scene = new THREE.Scene();

  

scene.background = new THREE.Color(0x000000); // 배경을 검정색으로 변경

  

  

const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.1, 100);

  

camera.position.z = 3;

  

  

const controls = new OrbitControls(camera, renderer.domElement);

  

controls.enableDamping = true;

  

  

// 무지개 색상 텍스처 생성

  

const size = 256;

  

const data = new Uint8Array(4 * size * size);

  

for (let y = 0; y < size; y++) {

  

for (let x = 0; x < size; x++) {

  

const i = (y * size + x) * 4;

  

// 무지개 패턴 만들기

  

data[i] = Math.sin((x / size) * Math.PI * 2) * 127 + 128; // R

  

data[i + 1] = Math.sin((y / size) * Math.PI * 2) * 127 + 128; // G

  

data[i + 2] = Math.sin(((x + y) / size) * Math.PI) * 127 + 128; // B

  

data[i + 3] = 255; // A

  

}

  

}

  

const rainbowTexture = new THREE.DataTexture(data, size, size, THREE.RGBAFormat);

  

rainbowTexture.needsUpdate = true;

  

  

// 사각형 생성

  

const geometry = new THREE.PlaneGeometry(2, 2, 10, 10); // 세분화된 사각형 생성

  

const material = new THREE.MeshPhysicalMaterial({

  

map: rainbowTexture,

  

roughness: 0.2,

  

metalness: 0.8,

  

reflectivity: 1.0,

  

clearcoat: 1.0,

  

clearcoatRoughness: 0.2,

  

side: THREE.DoubleSide // 양면 렌더링

  

});

  

const plane = new THREE.Mesh(geometry, material);

  

scene.add(plane);

  

  

// 주변광 추가

  

const ambientLight = new THREE.AmbientLight(0xaaaaaa); // 밝기를 높이기 위해 주변광의 강도를 증가

  

scene.add(ambientLight);

  

  

// 조명

  

const light = new THREE.DirectionalLight(0xffffff, 1);

  

light.position.set(5, 5, 5);

  

scene.add(light);

  

  

// 반대쪽에도 조명 추가하여 더 화려한 효과 - 더 밝게

  

const light2 = new THREE.DirectionalLight(0xffffff, 1.5); // 조명 강도 증가

  

light2.position.set(-5, -5, -5);

  

scene.add(light2);

  

  

// 윈도우 크기 변경 처리

  

window.addEventListener('resize', () => {

  

camera.aspect = window.innerWidth / window.innerHeight;

  

camera.updateProjectionMatrix();

  

renderer.setSize(window.innerWidth, window.innerHeight);

  

});

  

  

function animate() {

  

requestAnimationFrame(animate);

  

plane.rotation.x += 0.005;

  

plane.rotation.y += 0.01;

  

controls.update();

  

renderer.render(scene, camera);

  

}

  

  

animate();

  

} else {

  

document.body.innerHTML = `<h2 style="color:red;">WebGPU not supported in this browser.</h2>`;

  

}

  

</script>

  

</body>

  

</html>
```