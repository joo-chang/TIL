Rollup.js는 JavaScript 모듈 번들러로, ES 모듈을 기반으로 최적화된 번들을 생성한다.
Webpack과 비슷하지만 Tree Shaking 기능이 강력하여 불필요한 코드를 제거하고, 번들 크기를 최소화하는 데 유리하다.
<br>

## 기본 개념

### 모듈 번들링

- 여러 개의 JavaScript 모듈(파일)을 하나의 파일이나 여러 개의 파일로 합치는 작업이다. 이 과정을 통해 애플리케이션 로딩 속도를 개선하고, 네트워크 요청 수를 줄일 수 있다.

### 트리 쉐이킹

- 사용되지 않는 코드를 제거하고 번들의 크기를 줄이는 최적화 기법이다. ES 모듈의 정적 분석 덕분에 Rollup은 실제로 사용된 코드만 번들에 포함시킬 수 있다.
<br>

## 특징

### 트리 쉐이킹

- 사용되지 않는 코드를 제거하여 최적화된 번들 생성
- Webpack보다 더 효과적으로 코드 최적화 가능

### ES 모듈 (ESM) 기반

- 최신 JavaScript 표준 지원
- import/export 사용 시 성능 최적화

### 간단하고 직관적인 설정

- rollup.config.js 파일을 통해 설정
- 필요에 따라 플러그인 확장 가능

### 플러그인 지원

- Babel, TypeScript, CommonJS 변환 등을 지원하는 다양한 플러그인 제공

### 다양한 출력 포맷 지원

- ES Module(esm), CommonJS(cjs), IIFE, UMD 등 다양한 형식으로 번들 생성 가능
<br>

## 결론

Rollup은 코드를 최적화하고 번들링(여러 개의 모듈을 하나 또는 여러 개 파일로 합치는 작업)하는 도구이다.
Vite는 Rollup 기반으로 되어있는 빌드 도구이자 개발 서버이다.

