레이아웃 스래싱(Layout Thrashing)은 DOM(문서 객체 모델)을 반복적으로 읽고 쓰는 작업이 교차로 발생하면서, 브라우저의 Layout(리플로우)과 Paint(리페인트) 계산이 과도하게 유발되어 웹 렌더링 성능이 급격히 저하되는 현상이다. 애니메이션, 대량의 DOM 조작, 스크롤 연산이 많은 환경에서 자주 발생한다.

## 주요 개념

- **리플로우(Layout)와 리페인트(Paint)**: 리플로우는 요소의 크기·위치 재계산, 리페인트는 스타일·색상 등 시각 정보만 다시 그린다. 리플로우가 더 비용이 크다.
- **스래싱 발생 원인**: DOM의 레이아웃 속성을 읽고(예: offsetHeight), 바로 이어서 속성을 쓰는(스타일 변경 등) 작업이 반복되면, 브라우저가 레이아웃 계산을 강제로 반복해 처리 효율이 저하된다.
- **해결 원칙**: 읽기 작업과 쓰기 작업을 분리하여 일괄 처리하면 불필요한 레이아웃 플러시를 줄이고, 스래싱 현상을 크게 줄일 수 있다.

---

## 실습 예제

### 잘못된 코드 예시

```javascript
const items = document.querySelectorAll('.item');
items.forEach(item => {
  // offsetHeight(읽기) → style.height(쓰기) 반복: 스래싱 유발
  const height = item.offsetHeight;
  item.style.height = (height + 10) + 'px';
});
```
- 각 요소마다 읽기-쓰기-읽기가 반복되면, 브라우저가 매번 즉시 레이아웃 연산을 강제 수행하여 전체 성능이 떨어진다.

### 최적화 예시

```javascript
const items = document.querySelectorAll('.item');
const heights = [];

items.forEach(item => {
  heights.push(item.offsetHeight); // 모든 읽기를 모아서 실행
});

items.forEach((item, idx) => {
  item.style.height = (heights[idx] + 10) + 'px'; // 모든 쓰기를 모아서 실행
});
```
- 읽기-쓰기 작업을 분리 처리하여, 브라우저가 레이아웃을 한 번만 계산하도록 최적화된다.

---

## 정리/요약

레이아웃 스래싱은 지속적인 DOM 읽기/쓰기가 반복되면서 레이아웃 엔진이 비효율적으로 동작해 성능 저하가 발생하는 문제이다. 읽기와 쓰기 연산을 그룹화하거나, `requestAnimationFrame` 등 최적화 API를 활용해 최소한으로 레이아웃 작업이 이루어지도록 코드를 설계해야 한다.

---

## 참고 자료

- [MDN - Layout Thrashing](https://developer.mozilla.org/ko/docs/Web/Performance/How_browsers_work#layout_thrashing)
- [Google Web Fundamentals - Minimizing layout thrashing](https://web.dev/avoid-large-complex-layouts-and-layout-thrashing/)
- [NAVER D2 - 레이아웃 스래싱](https://d2.naver.com/helloworld/59361)
