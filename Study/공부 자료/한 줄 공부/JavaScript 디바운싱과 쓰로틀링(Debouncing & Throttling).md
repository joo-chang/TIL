> 디바운싱(Debouncing)과 쓰로틀링(Throttling)은 JavaScript에서 이벤트 핸들러의 실행 빈도를 제어하는 중요한 최적화 기법이다. 특히 스크롤, 리사이즈, 키보드 입력 등 빈번하게 발생하는 이벤트를 처리할 때 성능을 크게 향상시킬 수 있다.

## 기본 개념

### 디바운싱(Debouncing)
디바운싱은 연속적으로 발생하는 이벤트를 그룹화하여, 마지막 이벤트 발생 후 일정 시간이 지난 후에 한 번만 처리하는 기법이다. 즉, 이벤트가 발생하고 지정된 시간 내에 다시 같은 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머를 설정한다.

```js
function debounce(func, delay) {
  let timeoutId;
  
  return function(...args) {
    const context = this;
    
    // 이전 타이머 취소
    clearTimeout(timeoutId);
    
    // 새로운 타이머 설정
    timeoutId = setTimeout(() => {
      func.apply(context, args);
    }, delay);
  };
}

// 사용 예시
const handleSearch = debounce(function(event) {
  console.log('검색 요청:', event.target.value);
  // API 호출 등의 작업 수행
}, 500);

// 검색 입력 필드에 이벤트 리스너 등록
document.getElementById('search').addEventListener('input', handleSearch);
```

### 쓰로틀링(Throttling)
쓰로틀링은 이벤트가 발생하는 주기와 상관 없이, 일정 시간 간격으로 이벤트 핸들러를 최대 한 번만 실행하는 기법이다. 이벤트가 아무리 많이 발생해도 지정된 시간 간격으로만 실행을 허용한다.

```js
function throttle(func, limit) {
  let inThrottle = false;
  
  return function(...args) {
    const context = this;
    
    if (!inThrottle) {
      func.apply(context, args);
      inThrottle = true;
      
      setTimeout(() => {
        inThrottle = false;
      }, limit);
    }
  };
}

// 사용 예시
const handleScroll = throttle(function() {
  console.log('스크롤 이벤트 처리');
  // 스크롤 위치 계산 등의 작업 수행
}, 300);

// 스크롤 이벤트 리스너 등록
window.addEventListener('scroll', handleScroll);
```

## 디바운싱과 쓰로틀링을 사용하는 이유
- **성능 최적화**: 불필요한 함수 호출을 줄여 CPU 사용량 감소
- **API 호출 감소**: 서버 요청 횟수를 효과적으로 제한
- **사용자 경험 향상**: 스무스한 UI 인터랙션 제공
- **리소스 절약**: 특히 DOM 조작, 계산 비용이 큰 렌더링 작업 최소화
- **모바일 기기 배터리 절약**: 과도한 이벤트 처리로 인한 배터리 소모 방지

## 활용 예시

### 디바운싱 활용 사례

#### 1. 자동 완성 검색
사용자 입력이 일시적으로 멈출 때까지 기다렸다가 API 요청을 보내는 방식으로 서버 부하를 줄일 수 있다.

```js
// 검색 입력창의 디바운싱 처리
const autoCompleteSearch = debounce(async function(query) {
  try {
    const response = await fetch(`/api/search?q=${query}`);
    const results = await response.json();
    displayResults(results);
  } catch (error) {
    console.error('검색 오류:', error);
  }
}, 300);

document.getElementById('search').addEventListener('input', function(e) {
  autoCompleteSearch(e.target.value);
});
```

#### 2. 폼 유효성 검사
사용자가 입력을 마친 후에 유효성 검사를 수행하여 불필요한 검증 작업을 줄인다.

```js
const validateEmail = debounce(function(email) {
  const isValid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  const errorElement = document.getElementById('email-error');
  
  if (!isValid && email.length > 0) {
    errorElement.textContent = '유효한 이메일 주소를 입력해주세요.';
    errorElement.style.display = 'block';
  } else {
    errorElement.style.display = 'none';
  }
}, 500);

document.getElementById('email').addEventListener('input', function(e) {
  validateEmail(e.target.value);
});
```

### 쓰로틀링 활용 사례

#### 1. 스크롤 이벤트 처리
무한 스크롤 구현 시 스크롤 이벤트를 쓰로틀링하여 성능을 향상시킨다.

```js
const infiniteScroll = throttle(function() {
  const {scrollTop, scrollHeight, clientHeight} = document.documentElement;
  
  // 페이지 하단에 도달했는지 확인
  if (scrollTop + clientHeight >= scrollHeight - 5) {
    loadMoreContent();
  }
}, 200);

function loadMoreContent() {
  console.log('추가 콘텐츠 로딩...');
  // 데이터 로딩 및 렌더링 로직
}

window.addEventListener('scroll', infiniteScroll);
```

#### 2. 윈도우 리사이즈 이벤트
반응형 레이아웃 조정 시 리사이즈 이벤트를 쓰로틀링하여 빈번한 계산을 방지한다.

```js
const handleResize = throttle(function() {
  console.log('윈도우 크기 조정 처리');
  
  // 화면 너비에 따라 레이아웃 조정
  const width = window.innerWidth;
  if (width < 768) {
    switchToMobileLayout();
  } else if (width < 1200) {
    switchToTabletLayout();
  } else {
    switchToDesktopLayout();
  }
}, 250);

window.addEventListener('resize', handleResize);
```

## 디바운싱과 쓰로틀링의 차이점

| 특성 | 디바운싱 | 쓰로틀링 |
|------|---------|---------|
| 구현 원리 | 마지막 이벤트 발생 후 일정 시간이 지나면 실행 | 일정 시간 간격으로 최대 한 번만 실행 |
| 이벤트 처리 시점 | 연속된 이벤트의 끝에서 실행 | 연속된 이벤트 중 일정 간격으로 실행 |
| 적합한 상황 | 연속된 이벤트의 '최종' 상태만 중요할 때 | 일정 주기로 '중간' 상태를 처리해야 할 때 |
| 예시 | 검색 입력, 폼 유효성 검사 | 스크롤 이벤트, 게임 입력 처리 |

## 고급 구현 예시

### 옵션이 포함된 디바운스 함수

```js
function debounce(func, wait, options = {}) {
  let timeout;
  
  return function(...args) {
    const context = this;
    const later = function() {
      timeout = null;
      if (options.trailing !== false) {
        func.apply(context, args);
      }
    };
    
    const callNow = options.leading && !timeout;
    clearTimeout(timeout);
    timeout = setTimeout(later, wait);
    
    if (callNow) {
      func.apply(context, args);
    }
  };
}

// leading edge 옵션을 사용한 예시 (처음 이벤트 발생 시 즉시 실행)
const immediateDebounce = debounce(
  () => console.log('실행!'), 
  300, 
  { leading: true }
);
```

### 취소 가능한 쓰로틀 함수

```js
function throttle(func, wait, options = {}) {
  let timeout, context, args;
  let previous = 0;
  
  const later = function() {
    previous = options.leading === false ? 0 : Date.now();
    timeout = null;
    func.apply(context, args);
    context = args = null;
  };
  
  const throttled = function(..._args) {
    const now = Date.now();
    if (!previous && options.leading === false) previous = now;
    
    const remaining = wait - (now - previous);
    context = this;
    args = _args;
    
    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      previous = now;
      func.apply(context, args);
      context = args = null;
    } else if (!timeout && options.trailing !== false) {
      timeout = setTimeout(later, remaining);
    }
  };
  
  throttled.cancel = function() {
    clearTimeout(timeout);
    previous = 0;
    timeout = context = args = null;
  };
  
  return throttled;
}

// 사용 예시
const scrollHandler = throttle(() => {
  console.log('스크롤 처리 중...');
}, 200);

window.addEventListener('scroll', scrollHandler);

// 필요시 취소 가능
// scrollHandler.cancel();
```

## 결론
디바운싱과 쓰로틀링은 성능 최적화를 위한 필수적인 JavaScript 기법이다.

1. 디바운싱은 연속된 이벤트의 마지막 이벤트만 처리하여 검색, 입력 검증과 같은 시나리오에 적합하다.
2. 쓰로틀링은 일정 시간 간격으로 이벤트를 처리하여 스크롤, 리사이즈와 같은 연속적인 이벤트에 적합하다.
3. 두 기법을 적절히 활용하면 사용자 경험을 해치지 않으면서 성능을 크게 향상시킬 수 있다.
4. 상황에 맞게 선택하여 사용하는 것이 중요하며, 때로는 두 기법을 함께 사용하는 것도 고려할 수 있다.

최신 프론트엔드 프레임워크나 라이브러리를 사용하더라도 이러한 기본적인 최적화 기법을 이해하고 적용하는 것은 웹 애플리케이션의 성능과 사용자 경험 향상에 큰 도움이 된다.