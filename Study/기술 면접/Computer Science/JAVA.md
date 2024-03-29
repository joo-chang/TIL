#CS #면접 
### Java 장단점

- **장점**
    1. 운영체제에 독립적
        - JVM에서 동작하기 때문에 플랫폼에 종속적이지 않다.
    1. 객체지향 언어
        - 캡슐화, 상속, 추상화, 다형성 등을 지원하여 객체지향 프로그래밍이 가능하다.
    2. 동적 로딩을 지원
       - 애플리케이션이 실행될 때 모든 객체가 생성되지 않고, 각 객체가 필요한 시점에 클래스를 동적으로 로딩해서 생성된다.
       - 또한 유지보수 시 해당 클래스만 수정하면 되기 때문에 전체 애플리케이션을 다시 컴파일 할 필요가 없다. 따라서 유지보수가 쉽고 빠르다.
- **단점**
    1. 비교적 느림
       - 한번 컴파일링으로 실행 가능한 기계어가 만들어지지 않고, JVM에 의해 기계어로 번역되고 실행되는 과정을 거치기 때문에 실행 속도가 조금 느리다.
    2. 작성해야 하는 코드가 비교적 길다

<br>

---
### 객체지향 프로그래밍의 특징

**장점**
- 객체지향적 설계로 프로그램을 유연하고 변경이 용이하게 만들 수 있다.
- 각각의 객체들이 독립적인 역할을 하기 때문에 코드변경을 최소화하고 유지보수가 쉽고 빠르다.

**특징**
- 추상화
    - 사물들의 공통적인 특징을 파악해서 하나의 개념(집합)으로 다루는 것
    - 목적과 관련 없는 부분은 제거하여 필요한 부분만 표현하기 위한 개념
- 캡슐화
    - 정보 은닉 : 사용하지 않는 정보를 외부에서 접근하지 못하도록 제한
    - 캡슐화는 높은 응집도, 낮은 결합도로 유연함과 유지보수성 증가할 수 있게 해준다.
- 상속
    - 기존 클래스를 재활용하여 새롭게 클래스를 정의할 수 있게 도와주는 개념
- 다형성
    - 형태가 같은데 다른 기능을 하는 것을 의미
    - 오버라이딩, 오버로딩

<br>

---
### 객체지향 vs 절차지향

**객체지향 프로그래밍**
- 구현해야할 객체들 사이의 상호작용을 프로그래밍하는 방식
- 상속, 다형성, 캡슐화, 추상화를 통해 결합도를 낮추고 응집도를 높일 수 있으며 코드의 재사용성도 높일 수 있다.

**절차지향 프로그래밍**
- 실행하고자 하는 절차를 정하고, 순차적으로 프로그래밍하는 방식으로 속도가 빠르다.
- 엄격하게 순서가 정해져 있어 비효율적이고 유지보수가 어렵다.
- 목적을 달성하기 위한 일의 흐름에 중점을 둔다.

<br>

---
### 객체지향 설계 원칙 (SOLID)

**단일 책임 원칙 (SRP : Single Response Principle)**

- `하나의 클래스는 하나의 책임` 만 가져야 한다.
- 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것.

**개방-폐쇄 원칙 (OCP : Open-Closed Principle)**

- 클래스는 `확장에 열려있으나 변경에는 닫혀있어야 한다.`
- 기능 추가 시 클래스 확장을 통해 쉽게 구현하고, 확장에 따른 클래스 수정은 최소화 하도록 프로그램을 작성해야 하는 설계 기법이다.

**리스코프 치환 원칙 (LSP : Liskov Substitution Principle)**

- 인터페이스 규약에 맞게 구현해야 한다.
- 자동차 인터페이스의 앞으로 가는 기능을 뒤로 가게 구현하면 LSP 위반.

**인터페이스 분리 원칙 (ISP : Interface Segregation Principle)**

- 인터페이스를 각각 사용에 맞게 끔 잘게 분리해야한다.
- SRP는 클래스의 단일 책임 강조, ISP는 인터페이스의 단일 책임 강조

**의존관계 역전 원칙 (DIP : Dependency Inversion Principle)**

- 추상화에 의존해야지 구체화에 의존하면 안된다.
- 구현 클래스에 의존하지 말고, 인터페이스에 의존해야 한다.

<br>

---
### JVM (Java Virtual Machine)

- JVM 역할은 자바 애플리케이션을 클래스 로더를 통해 읽어 자바 API와 함께 실행하는 것
- Java와 OS 사이에서 중개자 역할을 수행하여 Java가 OS에 구애받지 않고 독립적으로 작동이 가능하다.
- 메모리 관리, Garbage Collection을 수행한다.

**JVM 구성 요소**

- Class Loader : 클래스 파일을 Runtime Data Area의 메서드 영역으로 불러오는 역할
- Execution Engine : 메모리(Runtime Data Area)에 적재된 클래스들을 기계어로 변경해 실행
- Garbage Collector : 메모리 관리 기법 중 하나. 힙 메모리에서 참조되지 않는 개체들 제거
- Runtime Data Area : 런타임 시 실제 데이터가 저장되는 곳. OS로 부터 할당 받은 메모리 영역

**JVM 실행 과정**

1. JVM은 OS로부터 메모리(Runtime Data Area)를 할당 받음
2. 컴파일러(javac)가 소스코드(.java)를 읽어 바이트코드(.class)로 변환
3. Class Loader를 통해 Class 파일을 JVM 내에 Runtime Data Area로 로딩
4. 로딩된 Class 파일을 Execution Engine을 통해 해석 및 실행

**JVM 메모리 구조**

- 메서드(static) 영역
  - 클래스가 사용되면 해당 클래스 파일을 읽어들여 클래스에 대한 정보(바이트 코드)를 메서드 영역에 저장
  - 클래스와 인터페이스, 메서드, 필드, static 변수, final 변수 등이 저장되는 영역
- JVM 스택 영역
  - 스레드마다 존재하여 스레드가 시작할 때마다 할당
  - 지역 변수, 매개 변수, 연산 중 발생하는 임시 데이터 저장
  - 메서드 호출 마다 개별적 스택 생성
- JVM 힙 영역
  - 런타임 시 동적으로 할당하여 사용하는 영역
  - new 연산자로 생성된 객체와 배열 저장
  - 참조가 없는 객체는 GC의 대상
- PC Register
  - JVM은 스택 기반의 가상 머신으로 CPU에 직접 접근하지 않고 Stack에서 주소를 뽑아서 PC Register에 저장된다.
- Native Method Stack
  - Java 이외의 언어에 제공되는 Method 정보가 저장되는 공간
  - Low Level 코드를 실행하는 스택

<br>

### Process/Thread 개념

**프로세스**

- 프로세스는 일반적으로 CPU에 의해 메모리에 올려져 실행중인 프로그램
- 메모리 공간을 포함하여 독립적인 실행 환경을 가지고 있다.
- JVM은 주로 하나의 프로세스로 실행되며, 동시에 여러 작업을 수행하기 위해 멀티 스레드를 지원한다.

**스레드**

- 스레드는 프로세스 안에서 작업을 실행하는 단위
- 자바에서는 JVM에 의해 관리
- 프로세스에는 적어도 한 개 이상의 스레드가 있고 스레드를 추가 생성 시 멀티 스레드 환경이 된다.
- 멀티 스레드들은 프로세스의 리소스를 공유

<br>

---
### Thread-Safe

- 여러 스레드에서 클래스나 클래스의 객체에 동시에 접근하더라도 정확하게 동작하는 것
- 여러 스레드에서 공유되는 클래스에 대한 접근을 관리하는 것

<br>

---
### Syncronized

- 여러 개의 스레드가 한 개의 자원을 사용하고자 할 때, 현재 사용중인 스레드를 제외하고 나머지 스레드들은 접근할 수 없게 막는 개념이다.
- Java에서 Syncronized 키워드를 제공해 멀티 스레드 환경에서 스레드 간 동기화를 시켜 데이터의 Thread-Safe를 보장한다.

<br>

---
### 접근 제어자

- 자바 접근제어자는 클래스, 인터페이스, 멤버변수, 메서드 등의 접근을 제어하는 지시어를 말한다.
- 외부 객체의 무분별한 접근으로 부터 내부 데이터를 보호할 수 있다.

- public : 모든 패키지, 모든 클래스 접근 허용
- protected : 같은 패키지, 모든 클래스 접근 허용 (다른 패키지인 경우 자식 클래스 접근 허용)
- default : 같은 패키지 내 클래스 접근 허용
- private : 같은 클래스 내 접근 허용

클래스 내부에 선언된 데이터의 부적절한 사용으로부터 보호하기 위해 접근 제어자를 사용한다.

<br>

---
### final 키워드

- final은 변수나 메서드, 클래스가 변경이 불가능 하도록 만드는 키워드이다.
- 무조건 초기화를 해야하며, 값 수정 불가능
- 상수 개념을 가지게 된다.
- 데이터를 지킬 때 사용

<br>

---
### 인터페이스와 추상클래스 차이

**인터페이스**
> 구현체 없이 메서드에 대한 명세만 가지고 있고 모든 메서드는 추상 메서드이다.

- 인터페이스를 상속받는 자식 클래스에 모든 메서드에 대한 구현을 강제함
- 다중 상속 가능
- implements 키워드 사용

**추상 클래스**
> abstract로 선언되거나 추상 메서드를 하나 이상 포함한 클래스를 말한다.

- 클래스이기 때문에 하나만 상속 가능
- 하위 클래스에게 구현을 강제하는 메서드이다.
- extends 키워드 사용

**차이점**
- 인터페이스는 그 인터페이스를 구현하는 모든 클래스에 대해 특정한 메소드가 반드시 존재하도록 강제함에 있다.
- 추상 클래스는 상속받는 클래스들의 공통적인 로직을 추상화 시키고, 기능 확장을 위해 사용한다.
- 추상 클래스는 다중 상속이 불가능하지만, 인터페이스는 다중 상속이 가능하다.


<br>

---
### Collection

Java Collection에는 List, Map, Set 인터페이스를 기준으로 여러 구현체가 존재한다. 또한, Stack, Queue 인터페이스도 존재한다.

**사용 이유**
다수의 Data를 다루는데 표준화된 클래스들을 제공해주기 때문에 Data Structure(자료구조)를 직접 구현하지 않고 편하게 사용할 수 있다.
또한, 배열과 다르게 공간을 미리 정하지 않아도 되므로, 상황에 따라 객체 수를 동적으로 정할 수 있다.

**List**
List 인터페이스를 직접 `@Override` 를 통해 정의하여 사용할 수 있으며, 대표적으로 `ArrayList` 가 있다.
이는 `Vector` 를 개선한 것이다.

**Map**
대표적으로 `HashMap` 이 있다.
Key-Value 구조이고 Key를 기준으로 중복된 값을 저장하지 않고, 순서 보장하지 않는다.
순서 보장하기 위해 `LinkedHashMap` 을 사용

**Set**
대표적으로 `HashSet` 이 있다.
중복 값을 저장하지 않는다.
Set은 Map의 Key-Value 구조에서 key 대신 value가 들어간 구조
순서를 보장하지 않는다.  `LinkedHashSet` 사용

**Stack, Queue**
Stack은 `Stack<Integer> stack = new Stack<>();`
Queue는 `Queue<Integer> queue = new LinkedList<>();`

<br>

---
### try-with-resources

try-with-resources는 try-catch-finally의 문제점을 보완하기 위해 나온 개념이다.

try() 에 자원 객체를 전달하면, try 블록이 끝나고 자동으로 자원 해제 해주는 기능이다.
따로 finally 구문이나 모든 catch 구문에 종료 처리를 하지 않아도 되는 장점이 있다.

<br>

---
### Generic

제네릭이란, 데이터 타입(자료형)을 일반화 하는 것으로, 데이터 타입을 컴파일 시 미리 지정해 두는 것을 말한다.
보통 클래스나 리스트 타입을 미리 지정할 때 사용한다.

```
<T> = Type
<E> = Element
<K> = Key
<V> = Value
<N> = Number
<R> = Result
```

제네릭을 사용하면 컴파일 시 타입을 명시해주기 때문에 의도하지 않은 형변환을 막을 수 있고, 
타입을 수정해야하는 경우 쉽게 변경할 수 있다.

<br>

### Annotation

어노테이션은 본래 주석이라는 뜻으로, 인터페이스를 기반으로 한 문법이다.
주석처럼 코드에 달아 클래스에 특별한 의미를 부여하거나 기능을 주입할 수 있다.

어노테이션은 크게 세 종류가 존재한다.

**built-in annotation**
JDK에 내장 되어있는 어노테이션
대표적인 예 - `@Override` 

**Meta annotation**
어노테이션에 대한 정보를 나타내기 위한 어노테이션

**Custom Annotation**
직접 만들어서 사용하는 어노테이션

<br>

---
### static

static은 static 변수와 static 메서드, 즉 정적 멤버를 만들 수 있는 키워드이다.

정적 필드와 정적 메서드는 인스턴스(객체)가 아닌 클래스에 고정되어, 클래스 로더가 클래스를 로딩해 메모리 영역에 적재할 때 클래스 별로 관리되어 정적 메모리로 사용할 수 있다.

객체 멤버는 객체를 생성할 때, 힙 영역에 할당된다.

정적 멤버는 클래스가 메모리에 올라갈 때 자동으로 생성되기 때문에 객체 인스턴스를 생성하지 않아도 호출하여 사용할 수 있다.   
따라서 static은 유틸리티 함수를 만들거나, 자주 사용하고 변하지 않는 값, 설정 정보등을 정적 메모리에 올려 객체 생성 비용을 낮출 수 있다.

메모리에 올라가는 것이기 때문에 남용하면 메모리가 낭비될 수 있다.



---
## 불변 객체란

- 불변 객체는 객체 생성 이후 내부의 상태가 변하지 않는 객체를 말한다.
- Java에서 필드가 원시 타입인 경우 final 키워드를 사용해 불변 객체를 만들 수 있다.
- 참조 타입일 경우에는 추가적인 작업이 필요하다.

### 참조 타입일 경우 추가적인 작업?

- 참조 타입은 대표적으로 (1) 객체를 참조할 수 있고, (2) 배열이나 (3) 리스트 등을 참조할 수 있다.

1. 참조 변수가 일반 `객체`인 경우 객체를 사용하는 필드의 참조 변수도 불변 객체로 변경해야 한다.
2. 배열일 경우 배열을 받아 copy 해서 저장하고, getter를 clone으로 반환하도록 하면 된다.
	- 배열을 그대로 참조하거나, 반환할 경우 내부 값을 변경할 수 있다. 때문에 clone을 반환해 외부에서 값을 변경하지 못하게 한다.
3. 리스트인 경우에도 배열과 마찬가지로 새로운 List를 만들어 값을 복사하도록 해야 한다.

### 불변 객체나 final을 사용해야 하는 이유

불변 객체나 final을 사용해 얻는 이점

1. Thread-Safe 하여 병렬 프로그래밍에 유용하며, 동기화를 고려하지 않아도 된다. (공유 자원이 불변하기 때문에 항상 동일한 값을 반환)
2. 실패 원자적인 메소드를 만들 수 있다. (어떠한 예외가 발생하더라도 메소드 호출 전 상태를 유지할 수 있어 예외 발생 전과 똑같은 상태로 다음 로직 처리 가능)
3. 부수 효과를 피해 오류를 최소화할 수 있다. 
	- 부수 효과 : 변수의 값이 바뀌거나 객체의 필드 값을 설정하거나 예외나 오류가 발생하여 실행이 중단되는 현상
4. 메소드 호출 시 파라미터 값이 변하지 않는다는 것을 보장할 수 있다.
5. 가비지 컬렉션 성능을 높일 수 있다. (가비지 컬렉터가 스캔하는 객체의 수가 줄기 때문)

<br>

---
### Garbage Collection

가비지 컬렉션은 JVM의 메모리 관리 기법 중 하나로 시스템에서 동적으로 할당됐던 메모리 영역 중에서 필요 없어진 메모리 영역을 회수하여 메모리를 관리해주는 기법이다.

**어떻게 사용하지 않는다고 판단하고 삭제가 되는지**
객체는 힙 영역에 저장되고, 스택 영역에 이를 가리키는 주소 값이 저장되는데
힙 영역에서 자신을 가리키는 주소 값이 없으면 참조되고 있지 않다고 판단한다.

- GC가 동작하면 GC Root 부터 객체를 찾고, 그 객체가 참조하는 객체를 찾아 mark한다.
- mark 되지 않은 객체는 접근할 수 없다고 판단하여 제거한다.

**GC 장점**
- 메모리 누수를 막을 수 있다.
- 해제된 메모리에 접근하는 오류와, 해제된 메모리를 한번 더 해제하는 이중 해제를 막을 수 있다.

**GC 단점**
- 메모리 해제 타이밍을 개발자가 정확하게 알기 힘들다.
- 실시간 성이 강조되는 프로그램의 경우 GC에게 메모리를 맡기는 것이 알맞지 않을 수 있다.

<br>

---




## 추상 클래스와 인터페이스

- 추상 클래스는 클래스 내 추상 메소드가 하나 이상 포함되고나 abstract로 정의된 경우를 말한다.
- 인터페이스는 모든 메소드가 추상 메소드로만 이루어져 있는 것을 ㅁ라한다.

- 공통점
	- new 연산자로 인스턴스 생성 불가능
	- 사용하기 위해 하위 클래스를 확장/구현 해야 된다.
- 차이점
	

---
## 싱글톤 패턴

싱글톤 패턴은 단 하나의 인스턴스를 생성해 사용하는 디자인 패턴이다.

- 인스턴스가 1개만 존재해야 한다는 것을 보장하고 싶은 경우와 동일한 인스턴스를 자주 생성해야 하는 경우(메모리 낭비 방지)에 주로 사용한다.

### 예시

싱글톤 패턴 대표적인 예시는  Spring Bean이다.

스프링 빈 등록 방식은 기본적으로 싱글톤 스코프이고, 스프링 컨테이너는 모든 빈들을 싱글톤으로 관리한다.

스프링은 요청할 때마다 새로운 객체를 생성해서 반환하는 기능도 제공한다. (프로토타입 빈, @Scope("prototype"))

---



---
## 자바 메모리 영역

자바 메모리 공간은 크게  Method, Stack, Heap 영역으로 구분되고, 데이터 타입에 따라 할당된다.

- Method 영역
	- 전역변수와 static 변수를 저장하며, Method 영역은 프로그램의 시작부터 종료까지 메모리에 남아있다.
	- JVM이 동작해서 클래스가 로딩될 때 생성된다.
- Stack 영역
	- 지역변수와 매개변수 데이터 값이 저장되는 공간이다.
	- 메소드가 호출될 때 메모리에 할당되고, 종료되면 메모리가 해제된다. LIFO 구조를 갖고 변수에 새로운 데이터가 할당되면 이전 데이터는 지워진다.
	- 컴파일 타임 시 할당
- Heap 영역
	- new 키워드로 생성되는 객체, 배열 등이 Heap 영역에 저장되며, 가비지 컬렉션에 의해 메모리가 관리되어 진다.
	- 런타임 시 할당

- 컴파일 타임 : 소스코드가 기계어로 변환되어 실행가능한 프로그램이 되는 과정
- 런타임 : 컴파일 타임 이후 프로그램이 실행되는 때

---
## 클래스와 객체

클래스는 객체를 만들어내기 위한 설계도 혹은 틀이라고 할 수 있고, 객체를 생성하는데 사용한다.
객체는 설계도(클래스)를 기반으로 생성되며, 자신의 고유 이름과 상태(필드), 행동(메서드)을 갖는다.

객체에 메모리가 할당되어 실제로 활용되는 실체를 인스턴스라고 부른다.

---
## 생성자

생성자는 클래스와 같은 이름의 메소드로, 객체가 생성될 때 호출되는 메서드이다.

명시적으로 생성자를 만들지 않아도 default로 만들어지며, 생성자는 파라미터를 다르게하여 오버로딩 할 수 있다.

---
## Wrapper Class란

- 기본 자료형에 대한 객체 표현을 Wrapper Classs라고 한다.
- 기본 자료형을 Wrapper Class로 변환하는 것이 Boxing이고 반대는 UnBoxing이다.

---
## Synchronized

- 여러 개의 스레드가 한 개의 자원을 사용하고자 할 때 현재 데이터를 사용하고 있는 스레드를 제외하고 나머지 쓰레드들은 데이터에 접근할 수 없게 막는 개념이다.
- 데이터의 Thread-Safe 를 하기 위해 자바에서 Synchronized 키워드를 제공해 멀티 스레드 환경에서 스레드간 동기화를 시켜 데이터의 Thread-Safe를 보장한다.
- Synchronized는 변수와 메소드에 사용해서 동기화 할 수 있으며, Synchronized 키워드를 남발하면 성능저하를 일으킬 수 있다.

---
## OOP (객체지향 프로그래밍)

Object-Oriented Programming의 약어로 객체지향 프로그래밍을 의미한다.

데이터를 객체로 취급하여 프로그램에 반영한 것이며, 순차적으로 프로그램이 동작하는 기존의 것들과는 다르게 객체와 객체의 상호작용을 통해 프로그램이 동작하는 것을 말한다.

### 특징

- 코드 재사용성이 높다.
- 코드의 변경이 용이하다.
- 직관적인 코드분석이 가능하다.
- 개발속도 향상
- 상속을 통한 장점 극대화

### 캡슐화

캡슐처럼 묶어 내부의 구조를 감추는 것을 말한다. 외부에서는 내부의 구조를 알지 못하며, 객체가 노출하여 제공하는 필드와 메소드만 이용할 수 있다. 

캡슐화를 하는 주된 이유는 공개하고 싶지 않은 내용의 보안성과 외부의 잘못된 사용으로 인해 객체가 손상되지 않도록 하기 위함이다.  

자바에서는 접근제한자를 통해 캡슐화된 멤버의 사용범위를 제한한다.

### 상속

하위 객체는 상위 객체를 재사용하여 쉽고 빠르게 설계할 수 있어, 반복된 코드의 중복을 줄여준다.

### 다형성

다형성은 하나의 타입에 여러 객체를 대입하여 다양한 기능을 이용할 수 있도록 한다.

부모 타입에는 모든 자식 객체가 대입될 수 있으며, 인터페이스 타입에는 모든 구현 객체가 대입될 수 있다.

---

<br>

### 참고

https://github.com/JaeYeopHan/Interview_Question_for_Beginner/blob/main/Java/README.md
https://gyoogle.dev/blog/computer-language/Java/%EC%BB%B4%ED%8C%8C%EC%9D%BC%20%EA%B3%BC%EC%A0%95.html
https://thalals.tistory.com/315
