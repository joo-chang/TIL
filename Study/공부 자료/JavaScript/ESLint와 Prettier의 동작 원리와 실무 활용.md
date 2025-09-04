> ESLint와 Prettier는 자바스크립트 코드의 문법 오류, 스타일 문제, 일관성 부족 등을 예방하기 위한 핵심 도구다.  
> ESLint는 코드의 잠재적 문제를 탐지·자동 수정하며, Prettier는 코드 스타일(포매팅)을 통일적으로 적용한다.  
> 실무에서는 두 도구를 함께 연동하여 대규모 협업, 코드리뷰 효율성 향상, 버그 사전 예방에 적극적으로 활용한다.

## 주요 개념

- **ESLint**  
  자바스크립트(및 TypeScript) 코드에서 문법 오류·베스트프랙티스 위반·잠재적 버그 등을 정적 분석할 수 있다.  
  룰을 커스터마이즈할 수 있으며, 다양한 플러그인을 통해 React, Vue, Node.js 등 환경별 규칙도 적용 가능하다.

- **Prettier**  
  코드의 스타일(들여쓰기, 세미콜론, 따옴표, 줄바꿈 등)을 자동으로 일관되게 변환한다.  
  스타일 관련 논란과 코드리뷰 소모를 줄여주며, 실제 포맷팅에 대한 결정권을 팀에서 하나로 통일할 때 유용하다.

- **ESLint + Prettier 연동**  
  두 도구는 역할이 겹칠 수 있으나, ESLint는 **문법적/로직적 문제**에 중점, Prettier는 **스타일 유지와 통일**에 중점을 둔다.  
  일반적으로 ESLint의 스타일 관련 룰은 꺼 놓고, 포매팅을 Prettier에 위임하는 방식이 권장된다.

## 실습 예제

### 기본 설치 및 설정 예시

```bash
# 프로젝트 루트에서 설치
npm install --save-dev eslint prettier

# React 프로젝트 환경 예시 (필요시)
npm install --save-dev eslint-plugin-react eslint-config-prettier eslint-plugin-prettier
```

**.eslintrc.js (권장 설정 예시)**
```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:prettier/recommended" // Prettier와 충돌하는 ESLint 규칙 off
  ],
  plugins: ["react", "prettier"],
  rules: {
    // 프로젝트 규칙 확장 가능, 예:
    "prettier/prettier": "error",
    "no-unused-vars": "warn",
    "react/react-in-jsx-scope": "off"
  },
};
```

**.prettierrc (권장 설정 예시)**
```json
{
  "singleQuote": true,
  "semi": true,
  "trailingComma": "all",
  "printWidth": 80,
  "tabWidth": 2
}
```

### 실제 사용법

- `npx eslint .` 또는 `npm run lint`로 코드 검사.
- `npx prettier --write .` 또는 IDE(예: VSCode)에서 자동 포맷 적용 가능.
- CI 환경(예: Github Actions)에서 체크 및 자동 고침 가능.

## 고급 확장 예시

### 팀/협업에서의 Prettier + ESLint 최신 실무 트렌드

- **라이브러리·프레임워크 별 프리셋 사용:**  
  Next.js, create-react-app 등 최신 템플릿은 ESLint+Prettier 연동을 기본 탑재하거나, 별도 플러그인 제공.
- **IDE 연동:**  
  VSCode 등 에디터에서 “저장 시 자동 포맷팅” 활성화, 팀 규칙 공유(`.editorconfig` 병행 추천).
- **CI 파이프라인 연동:**  
  pull request 시 eslint/prettier 자동 검사, 이슈 있을 때 머지 차단.

## 정리/요약

ESLint와 Prettier는 자바스크립트/프론트엔드 프로젝트 품질 유지에 있어 사실상 필수 도구다.  
ESLint는 문법과 로직 오류 방지, Prettier는 스타일 통일이라는 역할 분담을 하며,  
둘의 연동 및 최신 트렌드 적용을 통해 협업 효율과 코드 일관성을 극대화할 수 있다.

## 참고 자료

- [공식 ESLint 문서](https://eslint.org/docs/latest/use/)
- [Prettier 공식 문서](https://prettier.io/docs/en/index.html)
- [eslint-config-prettier (둘의 충돌 최소화)](https://github.com/prettier/eslint-config-prettier)
- [Airbnb 스타일 ESLint+Prettier 적용 예시](https://github.com/airbnb/javascript)
