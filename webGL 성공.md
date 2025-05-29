![](src/Pasted%20image%2020250529161116.png)

```html
<!DOCTYPE html>

  

<html lang="en">

  

<head>

  

<meta charset="UTF-8">

  

<title>Three.js 무지개빛 큐브 - 작은 창</title>

  

<style>

  

body { margin: 0; overflow: hidden; }

  

canvas {

  

position: absolute;

  

left: 0;

  

top: 0;

  

width: 320px;

  

height: 240px;

  

}

  

</style>

  

  

<script type="importmap">

  

{

  

"imports": {

  

"three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js"

  

}

  

}

  

</script>

  

</head>

  

<body>

  

<script type="module">

  

import * as THREE from 'three';

  

  

// 기본 설정

  

const scene = new THREE.Scene();

  

const camera = new THREE.PerspectiveCamera(75, 320 / 240, 0.1, 1000); // 카메라 비율도 320:240 고정

  

const renderer = new THREE.WebGLRenderer({ antialias: true });

  

renderer.setSize(320, 240); // 320x240 고정

  

renderer.setClearColor('black'); // 배경색 변경

  

document.body.appendChild(renderer.domElement);

  

  

// 무지개빛 색상 재질을 위한 버텍스 쉐이더

  

const vertexShader = `

  

varying vec2 vUv;

  

void main() {

  

vUv = uv;

  

gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);

  

}

  

`;

  

  

// 무지개빛 색상을 위한 프래그먼트 쉐이더

  

const fragmentShader = `

  

varying vec2 vUv;

  

void main() {

  

// 간단한 무지개 그라데이션 생성

  

vec3 rainbow;

  

rainbow.r = sin(vUv.x * 3.14159 * 2.0) * 0.5 + 0.5;

  

rainbow.g = sin(vUv.y * 3.14159 * 2.0) * 0.5 + 0.5;

  

rainbow.b = sin((vUv.x + vUv.y) * 3.14159) * 0.5 + 0.5;

  

gl_FragColor = vec4(rainbow, 1.0);

  

}

  

`;

  

  

// 사각형 만들기 (정육면체에서 변경)

  

const geometry = new THREE.PlaneGeometry(3, 3);

  

const material = new THREE.ShaderMaterial({

  

uniforms: {},

  

vertexShader: vertexShader,

  

fragmentShader: fragmentShader

  

});

  

const plane = new THREE.Mesh(geometry, material);

  

scene.add(plane);

  

  

// 사각형을 45도(x, y축) 회전

  

plane.rotation.x = Math.PI / 4;

  

plane.rotation.y = Math.PI / 4;

  

  

// 조명 추가

  

const light = new THREE.DirectionalLight(0xffffff, 1);

  

light.position.set(5, 5, 5);

  

scene.add(light);

  

  

const ambientLight = new THREE.AmbientLight(0x404040);

  

scene.add(ambientLight);

  

  

// 카메라 위치 조정

  

camera.position.set(0, 0, 5);

  

  

// 애니메이션

  

function animate() {

  

requestAnimationFrame(animate);

  

// 사각형 회전

  

plane.rotation.x += 0.01;

  

plane.rotation.y += 0.01;

  

renderer.render(scene, camera);

  

}

  

  

animate();

  

</script>

  

</body>

  

</html>
```