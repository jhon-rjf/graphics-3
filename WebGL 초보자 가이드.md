## WebGL이란 무엇인가?

**WebGL (Web Graphics Library)은 웹 브라우저에서 3D 그래픽을 렌더링**할 수 있게 해주는 JavaScript API입니다.

###  주요 특징
- **GPU 가속**: 그래픽 카드의 성능을 활용해 빠른 렌더링
- **브라우저 호환성**: 대부분의 현대 브라우저에서 지원
- **플러그인 불필요**: Flash나 Unity 같은 별도 플러그인 없이 작동
- **실시간 렌더링**: 게임, 시뮬레이션, 데이터 시각화 등에 활용
---
## Three.js 소개

**Three.js**는 WebGL을 **쉽게 사용할 수 있게 해주는 JavaScript 라이브러리**입니다.
###  Three.js의 장점

- **간단한 문법**: 복잡한 WebGL 코드를 단순화
- **풍부한 기능**: 조명, 재질, 애니메이션 등 내장
- **활발한 커뮤니티**: 많은 예제와 문서
- **크로스 플랫폼**: 데스크톱, 모바일 모두 지원
---

## 개발 환경 설정
### 1️. 필요한 도구

- **텍스트 에디터**: VSCode, Sublime Text 등
- **웹 브라우저**: Chrome, Firefox, Safari 등
- **로컬 서버**: Live Server (VSCode 확장) 또는 Python 서버

### 2️. 파일 구조

```

프로젝트 폴더/

├── index.html (메인 HTML 파일)

├── style.css (스타일 파일, 선택사항)

└── script.js (JavaScript 파일, 선택사항)

```

### 3️. Three.js 설치 방법

#### 방법 1: CDN 사용 (권장)

```html

<script type="importmap">

{

"imports": {

"three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js"

}

}

</script>

```

#### 방법 2: npm 설치

```bash

npm install three

```

  

---
## 코드 구조 이해하기

 예제 코드는 다음과 같은 구조를 가집니다:

```

HTML 기본 구조

├── 스타일 정의 (CSS)

├── Three.js 라이브러리 로드

└── JavaScript 메인 코드

├── 1. 기본 설정 (Scene, Camera, Renderer)

├── 2. 쉐이더 정의 (Vertex, Fragment)

├── 3. 지오메트리 및 재질 생성

├── 4. 조명 설정

└── 5. 애니메이션 루프

```

  

---

  

## 단계별 코드 분석

### 1. HTML 기본 구조

```html

<!DOCTYPE html>

<html lang="en">

<head>

<meta charset="UTF-8">

<title>Three.js 무지개빛 사각형</title>

<!-- 스타일 정의 -->

</head>

<body>

<!-- JavaScript 코드 -->

</body>

</html>

```

**설명**: 기본적인 HTML5 문서 구조입니다.


---
### 2. CSS 스타일링

```css

body {

margin: 0;

overflow: hidden;

}

  

canvas {

position: absolute;

left: 0;

top: 0;

width: 320px;

height: 240px;

}

```

**설명**:
- `margin: 0`: 페이지 여백 제거
- `overflow: hidden`: 스크롤바 숨김
- `canvas`: Three.js가 생성하는 캔버스 요소 스타일링

  

---

  

### 3. Three.js 라이브러리 로드

```html

<script type="importmap">

{

"imports": {

"three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js"

}

}

</script>

```

**설명**: ES6 모듈 시스템을 사용해 Three.js를 로드합니다.

---
###  4. 기본 설정

```javascript

import * as THREE from 'three';

  

// 씬 생성 - 모든 3D 객체들이 들어갈 공간

const scene = new THREE.Scene();

  

// 카메라 생성 - 3D 세계를 보는 시점

const camera = new THREE.PerspectiveCamera(

75, // 시야각 (FOV)

320 / 240, // 종횡비 (가로/세로)

0.1, // 가까운 클리핑 평면

1000 // 먼 클리핑 평면

);

  

// 렌더러 생성 - 실제로 화면에 그려주는 역할

const renderer = new THREE.WebGLRenderer({ antialias: true });

renderer.setSize(320, 240); // 캔버스 크기 설정

renderer.setClearColor('black'); // 배경색 설정

document.body.appendChild(renderer.domElement);

```

**핵심 개념**:
- **Scene**: 3D 세계의 컨테이너
- **Camera**: 3D 세계를 보는 관점
- **Renderer**: 3D 데이터를 2D 화면에 그리는 엔진

---

  ###  5. 쉐이더 프로그래밍

#### Vertex Shader (정점 쉐이더)

```glsl

varying vec2 vUv;

void main() {

vUv = uv; // UV 좌표를 프래그먼트 쉐이더로 전달

gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);

}

```

#### Fragment Shader (프래그먼트 쉐이더)

```glsl

varying vec2 vUv;

void main() {

// 무지개 색상 계산

vec3 rainbow;

rainbow.r = sin(vUv.x * 3.14159 * 2.0) * 0.5 + 0.5;

rainbow.g = sin(vUv.y * 3.14159 * 2.0) * 0.5 + 0.5;

rainbow.b = sin((vUv.x + vUv.y) * 3.14159) * 0.5 + 0.5;

gl_FragColor = vec4(rainbow, 1.0);

}

```


**쉐이더 설명**:

- **Vertex Shader**: 각 정점의 위치를 계산

- **Fragment Shader**: 각 픽셀의 색상을 계산

- **UV 좌표**: 텍스처 좌표 (0,0)부터 (1,1)까지

  

---

  

### 6. 지오메트리와 재질

```javascript

// 평면 사각형 생성 (3x3 크기)

const geometry = new THREE.PlaneGeometry(3, 3);

  

// 커스텀 쉐이더 재질 생성

const material = new THREE.ShaderMaterial({

uniforms: {},

vertexShader: vertexShader,

fragmentShader: fragmentShader

});

  

// 메쉬 생성 (지오메트리 + 재질)

const plane = new THREE.Mesh(geometry, material);

scene.add(plane);

```

  

**개념**:
- **Geometry**: 객체의 형태 (정점, 면 정보)
- **Material**: 객체의 표면 속성 (색상, 텍스처 등)
- **Mesh**: Geometry + Material의 조합

  

---

  

### 7. 조명 설정

```javascript

// 방향성 조명 (태양광 같은)

const light = new THREE.DirectionalLight(0xffffff, 1);

light.position.set(5, 5, 5);

scene.add(light);

  

// 환경 조명 (전체적인 밝기)

const ambientLight = new THREE.AmbientLight(0x404040);

scene.add(ambientLight);

```

  

**조명 종류**:

- **DirectionalLight**: 방향성 조명 (태양광)
- **AmbientLight**: 환경 조명 (전체 밝기)
- **PointLight**: 점 조명 (전구)
- **SpotLight**: 스포트라이트
  

---

###  8. 애니메이션

```javascript

function animate() {

requestAnimationFrame(animate); // 브라우저의 최적화된 애니메이션

// 객체 회전

plane.rotation.x += 0.01;

plane.rotation.y += 0.01;

// 화면에 렌더링

renderer.render(scene, camera);

}

  

animate(); // 애니메이션 시작

```
**애니메이션 원리**:

1. `requestAnimationFrame`: 브라우저 최적화 함수 (보통 60FPS)
2. 객체 속성 변경 (위치, 회전, 크기 등)
3. `renderer.render()`: 변경사항을 화면에 그리기
4. 무한 반복

  

---
## 핵심 개념 설명
###  좌표계
Three.js는 **우수 좌표계**를 사용합니다:

- **X축**: 오른쪽이 양수
- **Y축**: 위쪽이 양수
- **Z축**: 화면 밖(카메라 쪽)이 양수

### 수학적 개념

  #### 라디안 (Radian)

```javascript

Math.PI / 4 // 45도

Math.PI / 2 // 90도

Math.PI // 180도

Math.PI * 2 // 360도

```

#### 삼각함수 활용

```javascript

sin(x) // 사인파 (부드러운 진동)

cos(x) // 코사인파 (사인파와 90도 차이)

```

###  색상 표현

#### 16진수

```javascript

0xffffff // 흰색

0x000000 // 검은색

0xff0000 // 빨간색

0x00ff00 // 초록색

0x0000ff // 파란색

```

#### RGB 벡터

```glsl

vec3(1.0, 0.0, 0.0) // 빨간색

vec3(0.0, 1.0, 0.0) // 초록색

vec3(0.0, 0.0, 1.0) // 파란색

```

---
## 실행 및 테스트
###  실행 방법
#### 방법 1: Live Server 사용 (VSCode)

1. VSCode에서 Live Server 확장 설치

2. HTML 파일을 오른쪽 클릭

3. "Open with Live Server" 선택

#### 방법 2: Python 로컬 서버

```bash

# Python 3

python -m http.server 8000

```

브라우저에서 `http://localhost:8000` 접속
#### 방법 3: Node.js 서버
```bash

npx http-server

```

  
