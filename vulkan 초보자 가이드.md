# Windows에서 Visual Studio로 Vulkan 무지개 삼각형 그리기

  
## 1. 개발 환경 설정

### Vulkan SDK 설치

1. https://vulkan.lunarg.com/ 접속
2. 최신 버전 다운로드 (Windows 64-bit)
3. 설치 진행 시 환경변수 자동 설정 확인
4. 설치 완료 후 `VULKAN_SDK` 환경변수 확인

### 환경변수 확인 방법

```cmd

echo %VULKAN_SDK%

```

예상 출력: `C:\VulkanSDK\1.4.309.0`

### Visual Studio 2022 설치

- Community 버전 이상 권장
- C++ 데스크톱 개발 워크로드 설치
- Windows 10/11 SDK 포함

## 2. 프로젝트 생성 및 설정
### 프로젝트 생성

1. Visual Studio 2022 실행
2. "새 프로젝트 만들기" 선택
3. "콘솔 앱" 템플릿 선택
4. 프로젝트 이름: `VulkanTriangle`
5. 위치 선택 후 "만들기"

### NuGet으로 GLFW 설치

1. 도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔
2. 다음 명령어 실행:

```powershell

Install-Package glfw

```

  

### 프로젝트 속성 설정

프로젝트 우클릭 > 속성에서 다음 설정:

  

**C/C++ > 일반**
- 추가 포함 디렉터리: `$(VULKAN_SDK)\Include`  

**링커 > 일반**
- 추가 라이브러리 디렉터리: `$(VULKAN_SDK)\Lib`

**링커 > 입력**
- 추가 종속성에 추가: `vulkan-1.lib`

**일반 설정**

- 플랫폼 도구 집합: Visual Studio 2022 (v143)

- C++ 언어 표준: ISO C++17 표준 (/std:c++17)

  
**콘솔 창 제거 (선택사항)**

- 링커 > 시스템 > 하위 시스템: Windows (/SUBSYSTEM:WINDOWS)
- 링커 > 고급 > 진입점: mainCRTStartup


## 3. 쉐이더 파일 작성

  ### 폴더 구조 생성
프로젝트 폴더에 `shaders` 폴더 생성

  ### Vertex Shader 작성 (shader.vert)

```glsl

#version 450

  

vec2 positions[4] = vec2[](

vec2(-0.5, -0.5),

vec2(0.5, -0.5),

vec2(-0.5, 0.5),

vec2(0.5, 0.5)

);

  

vec3 colors[4] = vec3[](

vec3(1.0, 0.0, 0.0),

vec3(0.0, 1.0, 0.0),

vec3(0.0, 0.0, 1.0),

vec3(1.0, 1.0, 0.0)

);

  

layout(location = 0) out vec3 fragColor;

  

void main() {

gl_Position = vec4(positions[gl_VertexIndex], 0.0, 1.0);

fragColor = colors[gl_VertexIndex];

}

```

  

### Fragment Shader 작성 (shader.frag)
```glsl

#version 450

  

layout(location = 0) in vec3 fragColor;

layout(location = 0) out vec4 outColor;

  

void main() {

outColor = vec4(fragColor, 1.0);

}

```

### 쉐이더 컴파일
명령 프롬프트에서 shaders 폴더로 이동 후 실행:
```cmd

cd shaders

C:/VulkanSDK/1.4.309.0/Bin/glslc.exe shader.vert -o vert.spv

C:/VulkanSDK/1.4.309.0/Bin/glslc.exe shader.frag -o frag.spv

```

  

### 배치 파일 생성 (compile_shaders.bat)

```batch

@echo off

cd shaders

%VULKAN_SDK%\Bin\glslc.exe shader.vert -o vert.spv

%VULKAN_SDK%\Bin\glslc.exe shader.frag -o frag.spv

echo Shaders compiled successfully!

pause

```

## 4. 코드 구조 이해
### 주요 클래스: HelloTriangleApplication

```cpp

class HelloTriangleApplication {

public:

void run() {

initWindow(); // GLFW 창 초기화

initVulkan(); // Vulkan 초기화

mainLoop(); // 렌더링 루프

cleanup(); // 리소스 정리

}

};

```

  
