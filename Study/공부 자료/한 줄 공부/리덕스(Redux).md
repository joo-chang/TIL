> 리덕스는 리액트 생태계에서 사용률이 가장 높은 상태 관리 라이브러리이다.
> 리덕스를 사용하면 컴포턴트들의 상태 관련 로직들을 다른 파일들로 분리시켜서 효율적으로 관리할 수 있고 글로벌 상태 관리를 할 수 있고 상태 관리 로직도 분리할 수 있다.

## 사용 이유

### state 종속성 탈피

`useState`  를 사용할 경우 컴포넌트 내부에 state 를 만들고, 함수로 state를 바꾼다. 그렇기에 state는 컴포넌트에 종속된다.
redux는 컴포넌트에 종속되지 않고, 상태 관리를 컴포넌트 바깥에서 한다.
루트 레벨에서 `store` 라는 곳에 state를 저장하고, 모든 컴포넌트는 store에 구독하면서 state와 바꾸는 함수를 전달 받게 된다. 함수를 바꿔 state가 바뀌면 해당 state를 바라보고 있는 컴포넌트는 모두 리렌더링 된다.

### props -> props -> props 지옥 탈출

state를 자식의 자식의 자식까지 내려가서 사용하면 실수하기 쉽다. 하지만 redux의 store는 프로젝트 루트레벨에 위치하고, 해당 store를 구독하는 컴포넌트는 모두 state와 state를 바꾸는 함수를 받을 수 있다.

위치가 어디든 한번에 상태를 받을 수 있다.

## 기본 원리

redux는 기본적으로 `flux 패턴` 을 따른다.

```
Action -> Dispatch -> Store -> View
```

Redux 데이터 흐름은 동일하게 단방향으로 View 에서 Dispatch(store에서 주는 state를 바꾸는 함수) 를 통해 action (디스패치 함수 이름) 이 발동되고 reducer에 정의된 로직에 따라 store의 state가 변화하고 그 state를 쓰는 view가 변하는 흐름을 따른다.

<br>

## 키워드

### 액션 (Action)

상태의 변화가 필요할 때, 액션이라는 것을 발생 시킨다. 이는 하나의 객체로 표현된다.

```json
{
	type : "TOGGLE_VALUE"
}
```

액션 객체는 `type` 필드를 필수적으로 가지고 있어야 하고 외의 값들은 자유롭게 넣어 사용 가능하다.

```json
{
  type: "ADD_TODO",
  data: {
    id: 0,
    text: "리덕스 배우기"
  }
}
```

<br>

### 액션 생성 함수 (Action Creator)

액션을 만드는 함수이다. 파라미터를 받아와서 액션 객체 형태로 만들어준다.

```js
export function addTodo(data) {
  return {
    type: "ADD_TODO",
    data
  };
}

// 화살표 함수로도 만들 수 있습니다.
export const changeInput = text => ({ 
  type: "CHANGE_INPUT",
  text
});
```

액션 생성함수를 만들어 사용하는 이유는 컴포넌트에서 쉽게 액션을 발생시키기 위함이다.
그래서 `export` 키워드를 붙여 다른 파일에서 불러와 사용 가능하도록 한다.

리덕스를 사용할 때 액션 생성 함수 사용은 필수적이지 않다. 액션을 발생 시킬 때마다 직접 액션 객체를 작성할 수 있다.

<br>

### 리듀서 (Reducer)

리듀서는 변화를 일으키는 함수이다. 2 가지 파라미터를 받아온다.

```js
function reducer(state, action){
	// 상태 업데이트 로직
	return alteredState;
}
```

리듀서는 현재 상태와 전달 받은 액션을 참고하여 새로운 상태를 만들어 반환한다.

<br>

### Store

리덕스에서는 한 애플리케이션당 하나의 스토어를 만들게 된다. 스토어 안에는 현재의 앱 상태, 리듀서, 몇가지 내장 함수가 있다.

<br>

---
## React 에서 Redux 사용

### 패키지 설치

```
npm install redux react-redux redux-devtools-extension redux-logger
```

### reducer 정의

reducer는 store에 들어갈 state와 state를 바꿀 함수를 정의하는 곳이다.
기본적으로 순수함수로 코딩하고, 불변성을 지켜야 한다.

#### 불변성을 지켜야하는 이유

redux는 참조값이 변경되면 state가 바뀌었다고 인식하여 해당 state를 사용하는 컴포넌트에게 리렌더링을 요청하기 때문에 직접적으로 state를 변경하면 참조값이 변하지 않아 redux는 값이 변경되었다고 인식하지 않고 리렌더링 되지 않는다.

### 1. rootReducer 정의

```js
// reducers/index.js

/** root reducer */
import { combineReducers } from "redux";
import counter from "./counter";

// 여러 reducer를 사용하는 경우 reducer를 하나로 묶어주는 메소드
// store에 저장되는 리듀서는 오직 1개이다.
const rootReducer = combineReducers({
  counter
});

export default rootReducer;
```

### 2. 세부 reducer 정의

```js
// reducers/counter.js

// reducer가 많아지면 action상수가 중복될 수 있으니
// 액션이름 앞에 파일 이름을 넣는다.
export const INCRESE = "COUNT/INCRESE";

export const increseCount = count => ({ type: INCRESE, count });

const initalState = {
  count: 0
};

const counter = (state = initalState, action) => {
  switch (action.type) {
    case INCRESE:
      return {
        ...state,
        count: action.count
      };
    //default 필수
    default:
      return state;
  }
};
```

### 3. app에 store 넣고 reducer 반영

```js
// index.js
import React from "react";
import ReactDOM from "react-dom";
import { createStore, applyMiddleware, compose } from "redux";
import { Provider } from "react-redux";
import logger from "redux-logger";
import { composeWithDevTools } from "redux-devtools-extension";

import App from "./App";
import rootReducer from "./reducers";

// 배포 레벨에서는 리덕스 발동시 찍히는 logger를 사용 안하는 코드
const enhancer =
  process.env.NODE_ENV === "production"
    ? compose(applyMiddleware())
    : composeWithDevTools(applyMiddleware(logger));

// 위에서 만든 reducer를 스토어 만들때 넣는다
const store = createStore(rootReducer, enhancer);

ReactDOM.render(
  // 만든 store를 앱 상위에 넣는다
  <Provider store={store}>
    <App />
  </Provider>
  document.getElementById('root'),
);
```

### 4. 컴포넌트에서 redux 사용

```js
import { useSelector, useDispatch } from "react-redux";
import { increseCount } from "reducers/count";

// dispatch를 사용하기 위한 준비
const dispatch = useDispatch();

// store에 접근하여 state 가져오기
const { count } = useSelector(state => state.counter);

const increse = () => {
  // store에 있는 state 바꾸는 함수 실행
  dispatch(increseCount());
};

const Counter = () => {
  return (
    <div>
      {count}
      <button onClick={increse}>증가</button>
    </div>
  );
};

export default Counter;
```

위처럼 store에서 `useDispatch`, `useSelector` 로 state 와 함수를 가져와서 필요 시 호출하면 된다.

---

Redux의 함수는 무조건 동기 처리된다.
이를 비동기 처리하기 위해 나온 것이 **redux-saga** 이다. 


