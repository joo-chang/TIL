> Recoil은 React 전용 상태 관리 라이브러리이다. Recoil을 사용해 전역 상태 관리 시 코드가 간결해진다.

## Recoil 특징

### atom

- Recoild 에서 상태를 정의하는 개념 (Redux의 store 와 유사)
- 컴포넌트가 구독할 수 있는 state 이고 atom을 생성하기 위해 고유 key와 default 값이 필요함
- atom 값이 변경되면 atom을 구독하고 있는 모든 컴포넌트들이 모두 리랜더링 됨

### selector

- 파생된 상태(derived state) 의 데이터를 의미
- read-only 객체. 순수 함수
- atom이나 다른 selector를 통해 입력받을 수 있다.
- 상위 atom이나 다른 selector가 업데이트 되면 하위 selector 도 재실행 된다.
- 기본적이고 최소 상태들은 atom에 저장해두고, selector에 명시한 함수를 통해 상태 값을 변경하면 된다.


