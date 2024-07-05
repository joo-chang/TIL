## 멀티 모듈

모놀리식 아키텍쳐 프로젝트를 MSA 아키텍쳐로 전환하는 과정에서 단일 모듈로 구성되어 있는 프로젝트를 멀티 모듈로 구성하는 과정이 필요하다.

멀티 모듈이란 패키지의 한 단계 위의 집합체로, 관련된 패키지와 리소스들을 재사용할 수 있는 그룹이다.

쉽게 말해 모듈은 패키지의 집합을 의미한다.


### 멀티 모듈 사용 이유

멀티 모듈을 사용하는 이유는 크게 2가지가 있다.

- 멀티 프로젝트의 공통된 소스 코드를 묶기 위해 멀티 모듈로 구성
- 단일 프로젝트에서 소스 코드를 어떠한 단위로 분리하기 위해 멀티 모듈로 구성

#### 멀티 프로젝트의 공통된 소스 코드를 묶기 위해 멀티 모듈로 구성

![[Pasted image 20240612114150.png]]

A, B, C 프로젝트가 모두 멤버 관련 소스 코드를 사용한다고 할 때, 멤버 코드에 변경이 생기면 어떻게 될까?
각각 프로젝트의 멤버 코드에 수정해야 한다.
이런 번거로움을 단일 프로젝트의 멀티 모듈로 구성하면 해결할 수 있다.

![[Pasted image 20240612114617.png]]

단일 프로젝트로 Common 프로젝트를 구성하고 A, B, C에 모두 들어 있던 공통 멤버 코드를 개별 모듈로 분리하여 구성하면 위와 같은 문제를 해결할 수 있다.


#### 단일 프로젝트에서 소스 코드를 어떠한 단위로 분리하기 위해 멀티 모듈로 구성

MSA를 위해 멀티 모듈을 사용하는 경우 이 이유 때문에 멀티 모듈을 사용한다.

공통 코드를 추출하는 것이 목적이 아니라 어떠한 단위로 소스 코드를 분리하기 위해 사용한다.


![[Pasted image 20240612122725.png]]

단위 개념은 크게 2가지가 있다.

- 계층별
- 도메인별

MSA 전환 시 도메인별로 서비스가 나뉘기 때문에 도메인별로 모듈을 분리해서 프로젝트를 구성하면 된다.

---

## Root 모듈 구성

- 기존 소스 코드 삭제(src 폴더 삭제)
- build.gradle에서 하위 모듈 설정
- settings.gradle에서 하위 모듈 추가

![[Pasted image 20240612123914.png]]

### settings.gradle

```java
rootProject.name = 'shboard'
include 'shboard-common'
include 'member-service'
include 'board-service'
include 'discovery-server'
```

settings.gradle 에서는 하위 모듈들을 include로 선언해줘서 Root 모듈 설정에서 사용할 수 있도록 지정한다.
### build.gradle

```java
project(':board-service') {

	dependencies {
	
		implementation project(':shboard-common')
		
		implementation project(':member-service')
		
		implementation(testFixtures(project(":shboard-common")))
		
	}

}

project(':member-service') {

	dependencies {
	
		implementation project(':shboard-common')
		
		implementation(testFixtures(project(":shboard-common")))
		
	}

}
```