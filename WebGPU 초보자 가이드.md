## WebGPU란 무엇인가

WebGPU는 차세대 웹 그래픽 API로, WebGL을 대체하기 위해 개발되었습니다. GPU의 현대적인 기능을 웹에서 활용할 수 있게 해주며, 더 나은 성능과 새로운 기능들을 제공합니다.

  

### 주요 특징

- **최신 GPU 아키텍처 지원**: Vulkan, Metal, DirectX 12 기반
- **컴퓨트 쉐이더 지원**: 그래픽 외 일반 계산 작업 가능
- **더 나은 성능**: 멀티스레딩과 저수준 제어
- **향상된 디버깅**: 더 자세한 오류 정보
- **현대적 API 설계**: 더 직관적이고 안전한 인터페이스

### 장단점

**WebGPU 장점**

- 더 빠른 렌더링 성능
- 컴퓨트 쉐이더로 GPU 연산 활용
- 현대적이고 안전한 API
- 더 나은 멀티스레딩 지원

  

**WebGPU 단점**

- 제한적인 브라우저 지원 (Chrome 113+, Firefox 실험적)
- 학습 곡선이 가파름
- 아직 표준이 완전히 확정되지 않음

  

## 개발 환경 설정
### 브라우저 지원 확인

WebGPU는 아직 모든 브라우저에서 지원되지 않습니다.

**지원 브라우저**

- Chrome 113+ (기본 활성화)

- Firefox (about:config에서 활성화 필요)

- Safari (실험적 지원)

  

**지원 확인 방법**

```javascript

function isWebGPUSupported() {

return !!navigator.gpu;

}

```

  
### Three.js WebGPU 설정

```html

<script type="importmap">

{

"imports": {

"three": "https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js",

"three/addons/": "https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/"

}

}

</script>

```

  

### 필수 임포트

```javascript

import * as THREE from 'three';

import WebGPURenderer from 'three/addons/renderers/webgpu/WebGPURenderer.js';

```

  

## 코드 구조 분석

### 기본 설정 단계

1. **WebGPU 지원 확인**

```javascript

if (isWebGPUSupported()) {

// WebGPU 코드 실행

} else {

// 대체 메시지 또는 WebGL 폴백

}

```

  

2. **렌더러 생성**

```javascript

const renderer = new WebGPURenderer();

renderer.setSize(window.innerWidth, window.innerHeight);

document.body.appendChild(renderer.domElement);

```

  

3. **씬과 카메라 설정**

```javascript

const scene = new THREE.Scene();

scene.background = new THREE.Color(0x000000);

  

const camera = new THREE.PerspectiveCamera(

70, // 시야각

window.innerWidth / window.innerHeight, // 종횡비

0.1, // near

100 // far

);

```

  

### 텍스처 생성
WebGPU에서는 데이터 텍스처를 직접 생성할 수 있습니다:

  

```javascript

const size = 256;

const data = new Uint8Array(4 * size * size); // RGBA

  

for (let y = 0; y < size; y++) {

for (let x = 0; x < size; x++) {

const i = (y * size + x) * 4;

data[i] = Math.sin((x / size) * Math.PI * 2) * 127 + 128; // R

data[i + 1] = Math.sin((y / size) * Math.PI * 2) * 127 + 128; // G

data[i + 2] = Math.sin(((x + y) / size) * Math.PI) * 127 + 128; // B

data[i + 3] = 255; // A

}

}

  

const texture = new THREE.DataTexture(data, size, size, THREE.RGBAFormat);

texture.needsUpdate = true;

```

  

### 고급 재질 사용
WebGPU는 물리 기반 렌더링(PBR)을 효율적으로 지원합니다:

  

```javascript

const material = new THREE.MeshPhysicalMaterial({

map: rainbowTexture, // 기본 텍스처

roughness: 0.2, // 거칠기 (0: 매끄러움, 1: 거침)

metalness: 0.8, // 금속성 (0: 비금속, 1: 금속)

reflectivity: 1.0, // 반사율

clearcoat: 1.0, // 클리어코트 강도

clearcoatRoughness: 0.2, // 클리어코트 거칠기

side: THREE.DoubleSide // 양면 렌더링

});

```

  

## 핵심 개념 설명
### 1. WebGPU 렌더러
WebGL 렌더러와 API는 동일하지만 내부적으로 WebGPU를 사용합니다:

  

```javascript

// WebGL

const renderer = new THREE.WebGLRenderer();

  

// WebGPU

const renderer = new WebGPURenderer();

```

### 2. 물리 기반 렌더링 (PBR)
WebGPU는 현실적인 조명 계산을 위한 PBR을 지원합니다:

- **Roughness**: 표면의 거칠기 정도
- **Metalness**: 금속성 여부
- **Reflectivity**: 반사 정도
- **Clearcoat**: 표면 코팅 효과


### 3. 컴퓨트 쉐이더

WebGPU만의 특별한 기능으로, 그래픽 외 일반 계산을 GPU에서 수행할 수 있습니다:

  ```javascript

// 아직 Three.js에서 완전히 지원되지 않음

// 순수 WebGPU API 사용 필요

```
  
### 4. 메모리 관리
WebGPU는 더 세밀한 메모리 제어가 가능합니다:

```javascript

// 텍스처 업데이트 시 명시적 지시

texture.needsUpdate = true;

```

  

## 실행 방법
### 로컬 서버 실행
WebGPU는 보안상 로컬 파일에서 직접 실행되지 않습니다.

**방법 1: Python 서버**

```bash

python -m http.server 8000

```

  **방법 2: Node.js 서버**

```bash

npx http-server

```

  **방법 3: VSCode Live Server**

VSCode에서 Live Server 확장을 설치하고 HTML 파일에서 "Open with Live Server" 실행

### 브라우저 설정

**Chrome**: 별도 설정 불필요 (113+ 버전)

**Firefox**:

1. `about:config` 입력
2. `dom.webgpu.enabled`를 `true`로 설정
3. 브라우저 재시작

