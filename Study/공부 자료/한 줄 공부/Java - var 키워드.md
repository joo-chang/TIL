
java의 var 키워드는 java 10 부터 도입된 키워드 이다. 

해당 키워드는, **지역 변수의 타입 추론**을 위한 키워드 이다. **변수 선언시, 타입을 생략 가능**하게 한다.

var 키워드의 경우 Compile 시점에 타입을 추론하게 된다. 즉 Compile 시점에 타입이 이미 결정된다. 

이때 컴파일러가 타입을 추론할 수 없을 경우, Compile Error 가 발생하게 된다. 

즉 이걸 반대로 생각한다면, RunTime 시점에 타입에 대한 연산이 일어나지 않는다는 말이다. 

---
## 장단점

### 장점

- **가독성 향상**: 코드가 간결해지고, 중복된 타입 선언을 제거하여 가독성이 향상된다.
- **유지 보수성 증가**: 코드의 변경이 있을때, 변수의 타입을 모두 변경하지 않아도 되어, 유지보수성이 증가한다.
- **컴파일 시점에 검증**: 컴파일 시점에 에러를 잡아낼 수 있어, 실수를 줄일수있다.
- **코드 간소화**: 타입을 모두 적지 않기 때문에 코드가 간소화됨.

### 단점

- **잘못된 타입 추론**: 개발자가 의도한 타입대로 추론 하지 않을 수 있으며, 크리티컬한 에러 발생으로 이어질 수 있다.
- **코드 분석의 어려움**: 코드의 간소화로 가독성은 향상될 수 있으나, 분석에는 번거로움이 발생 할 수 있다.

```java
// 변경 전
List<Map<String,Object>> list = service.getAll();

// 변경 후
var list = service.getAll();
```

## 사용 방법

var 키워드는 `지역 변수` 로만 사용 가능하다.

- 반드시 초기화가 필요하다.
- null 불가
- 배열도 타입 추론 불가
- 람다식 타입 추론 불가

```java
@GetMapping("/{id}")  
public ApiResponse<List<ArticleDTO>> getArticles(@PathVariable Long id) {  
	// 변경 전
	List<ArticleDTO> response = articleService.getArticleDetails(id);
	// 변경 후
    var response = articleService.getArticleDetails(id);  
    return ApiResponse.success(HttpStatus.OK.name(), response);  
}
```

