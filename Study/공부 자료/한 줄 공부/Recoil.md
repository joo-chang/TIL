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

## 사용법

### 1. Recoil 설치

```
npm install recoil
```

### 2. RecoilRoot 세팅

App.js 또는 index.js 최상단에 RecoilRoot를 설정한다.

```js
import React from 'react';  
import {  
	RecoilRoot,  
} from 'recoil';  
  
function App() {  
	return (  
		<RecoilRoot>  
			<CharacterCounter />  
		</RecoilRoot>  
	);  
}
```

### 3. atom 설정

아래와 같이 원하는 값들을 설정한다. 
이때 key 값은 고유해야 한다.

```js
// atom.js

import { atom } from "recoil";
import { recoilPersist } from "recoil-persist";

const { persistAtom } = recoilPersist();

export const userState = atom({
	key: "user",
	default: {
		userId: 0,
		email: '',
		nickname: '',
		profileUrl: '',
		role: ''
	},
	// 새로고침해도 값 유지
	effects_UNSTABLE: [persistAtom],
})
```

`useSetRecoilState()` 를 이용하여 해당 atom에 값을 세팅한다.

```js
const Login = () => {
	const setUser = useSetRecoilState(userState);

	const onhandlePost = async data => {

		const postData = { email, password }
		await axios
			.post("/login", postData)
			.then(function (response) {
				// 유저 정보 세팅
				setUser(response.data.data);
			})
	}
}
```

이후 `useRecoilState(userState)` 를 이용해 저장된 값을 불러와서 사용할 수 있다.

```js
const [user, setUser] = useRecoilState(userState);

return (
	<div>
		{user.email} {user.nickname}
	</div>
)
```

