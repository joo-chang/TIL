## 자바 스트림

### 스트림이란?

> 자바 8에서 추가된 스트림(Stream)은 기존 자바 I/O에서 나오는 InputStream, OutputStream과는 다른 것으로 함수형 프로그래밍에서 단계적으로 정의된 계산을 처리하기 위한 인터페이스이다.

스트림은 데이터의 흐름으로 배열 또는 컬렉션 인스턴스에 함수를 조합하여 원하는 결과를 필터링하고 가공된 결과를 쉽게 처리할 수 있다. 자바 입출력에서 말하는 스트림과 연속된 데이터의 흐름이라는 관점에서는 비슷하지만 내용적으로는 다른 것으로 특히 데이터를 다루는 여러 분야에서 유용하게 사용할 수 있으며 코딩 테스트 등에 나오는 문제들을 풀 때도 도움된다.

스트림은 데이터 소스르르 추상화 하고 있으므로 데이터 소스에 상관없이 같은 방식으로 처리할 수 있다는 장점이 있으며 데이터를 다루는데 자주 사용되는 메서드들을 정의해 두고있어 기존의 방식보다 간결하고 유연한 구현이 가능하다.


### 기존의 방식과 스트림 방식 비교
	* 동일한 데이터를 가지는 배열과 리스트를 선언
	* 각각 데이터 정렬을 위한 메서드를 통해 데이터를 정렬
	* 정렬된 값을 확인하기 위해 출력문을 이용해서 출력

```java
String[] strArr = {"data1", "data2", "data3"}
List<String> strList = Arrays.asList(strArr);
```

### 기존 방식

데이터 정렬을 위해 각각 Arrays, Collections 의 sort 메서드를 이용해 정렬한 다음 for문을 이용해 결과를 출력하는 형식이다.

```java
Arrays.sort(strArr);
Collections.sort(strList);

for(String str : strArr){
	System.out.println(str);
}

for(String str : strList){
	System.out.println(str);
}
```

### Stram API 사용

배열 혹은 리스트로 부터 스트림을 생성하고 정렬을 위해 sorted() 메서드를 호출한 다음 출력을 위해 forEach() 메서드 호출

```java
strList.stream().sorted().forEach(System.out::println);
Arrays.stream(strArr).sorted().forEach(System.out::println);
```

여기서 forEach는 `void forEach(Consumer<? super T> action)` 로 정의되어 있으며 Consumer 함수형 인터페이스를 인자로 가진다. 메서드 레퍼런스를 사용하지 않고 람다식으로 표현하면 다음과 같다.

```java
strList.stream().sorted().forEach(x -> System.out.println(x));
```


### 스트림과 컬렉션

> 자바에서 데이터를 다루기 위한 기본은 컬렉션 프레임워크이다. 컬렉션은 자료구조에 대한 구현체. 즉, 데이터 처리를 위해서는 각 요소들을 순회 하면서 처리해야 하고 하나의 단계가 끝나고 다른 단계를 진행하는 절차적인 구조를 가지고 있다.
> 그러나 점점 데이터 양이 많아지고 프로그램에서 데이터를 다루는 방법이 복잡해지면서 병렬처리의 효과적인 구현 방법에 대한 필요성이 증가하고 코드의 간결화와 성능 향상이 요구 되었다.
> 람다와 함께 스트림 API는 바로 이러한 문제점들을 해결하기 위한 방법으로 최신의 프로그램 언어들과 유사한 방법으로 자바에서도 데이터를 핸들링 할 수 있다. 스트림의 주요 특징은 다음과 같다.

* 스트림에서 요소들은 저장되지 않고 하부 컬렉션에 보관 되거나 필요할 때에만 생성해 사용된다.
* 스트림 연산은 원본 데이터를 바꾸지 않고 결과를 저장한 새로운 스트림을 반환한다.
* 스트림은 가능한 지연(Lazy) 처리를 기본으로 한다.
* 스트림은 한 번 사용하고 버려진다. (재활용 불가)
* 로직을 잘못 설계하면 스트림을 반복해서 사용하게 되고 이는 실행 성능에 문제가 될 수 있다.
* 병렬 처리가 컬렉션 내부에서 처리되므로 많은 데이터 처리 시 성능 향상의 효과가 있다.


### 스트림 연산 구조

스트림은 `어떻게` 가 아니라 `무엇`을 할 것인지에 목적을 두고 사용해야 하며 
연산의 파이프라인은 스트림생성 -> 중간 연산 -> 최종연산 의 형태를 가지며 `.`을 이용한 메서드 체이닝으로 구현된다.

```
Collections 같은 객체 집합.스트림생성().중간연산().최종연산();
```

* 중간연산 메서드는 리턴 타입이 스트림이므로 계속해서 다른 스트림 메서드를 연결해 사용할 수 있다.
* 최종연산 메서드는 리턴 타입이 스트림이 아닌 것으로 메서드 체이닝을 끝내는 역할을 한다.
* 최종연산이 실행 되어야 중간연산도 처리되기 때문에 중간연산들만으로 구성된 메서드 체인은 실행되지 않는다.


전형적인 스트림 사용 예 - 중복 데이터를 제거하고 데이터를 5개로 제한해서 정렬한 다음 출력하는 코드
```java
Arrays.stream(strArr)
		.distinct()
		.limit(5)
		.sorted()
		.forEach(System.out::print);
```


### 스트림생성

* 컬렉션, 배열, 문자열, 파일 등으로부터 스트림을 생성할 수 있다.

#### Empty Stream

비어있는 스트림을 생성하기 위해서는 `empty()` 메서드를 사용한다.

```java
Stream<String> streamEmpty = Stream.empty();
```


#### Array Stream

배열로부터 스트림을 생성하는 방법은 여러가지가 있다.

```java
Stream<String> arraySteam = Stream.of("a", "b", "c");
String[] arr = new String[]{"a", "b", "c"};
Stream<String> arrayFullStream = Arrays.stream(arr);
Stream<String> arrayPartStream = Arrays.stream(arr, 1, 3);
```


#### Collection Stream

자바 컬렉션 인터페이스를 사용하는 Collection, List, Set은 stream() 메서드와 parallelStream() 메서드를 사용할 수 있다. Map의 경우 Key 혹은 Value 값만 리스트로 추출한 다음 스트림을 만들어 사용할 수 있다.

```java
Collection<String> collection = Arrays.asList("a", "b", "c");
Stream<String> collectionStream = collection.stream();

List<String> names = new ArrayList<>();
names.add("Kang");
names.add("Park");
names.stream().forEach(System.out::println);

```


#### String Stream

문자열을 다루는 클래스인 String, StringBuffer, StringBuilder는 문자열 시퀀스를 반환하는 chars() 메서드를 가지고 있는데 이를 통해 스트림을 생성하게 된다. 각각 문자를 IntStream 으로 변환한 예제이다.

```java
IntStream charsStream = "abc".chars();
String str = "Hello world";
str.chars().filter(....)
```


#### File Stream

파일의 경우 자바 NIO 의 Files클래스를 이용해 문자열 스트림 생성이 가능하다.

```java
Path path = Paths.get("C:/Tmp/testfile.txt");
Stream<String> streamOfStrings = Files.lines(path);
Stream<String> streamWithCharset = Files.lines(path, Charset.forName("UTF-8"));
```


#### 병렬 스트림(Parallel Stream)

병렬 스트림은 내부적으로 fork & join 프레임웍을 이용해 자동적으로 연산을 병렬로 수행한다. 병렬처리를 구현하기 위해 개발자가 신경써야 하는 많은 부분을 해결할 수 있으며 스트림 생성시 parallel() 메서드를 실행 하기만 하면된다. 병렬 스트림 처리에서 병렬처리를 중단하려면 sequential()을 호출하면 된다.

```java
int sum = strStream.parallel()
                   .mapToInt(s -> s.length())
                   .sum();
```


#### 스트림 활용 샘플 코드

```java
// Map에서 value 만 형변환 스트림.
double[] dresult = rr.values().stream().mapToDouble(i->i).toArray();
```

#### Stream.builder()

빌더를 사용하면 스트림에 직접적으로 원하는 값을 넣을 수 있다. 마지막에 `build()` 메소드로 스트림을 리턴한다.

```java
Stream<String> builderStream = 
  Stream.<String>builder()
    .add("Eric").add("Elena").add("Java")
    .build(); // [Eric, Elena, Java]
```
``
#### Stream.generate()

genernate 메소드를 이용하면 `Supplier<T>` 에 해당하는 람다로 값을 넣을 수 있다. `Supplier<T>`는 인자는 없고 리턴값만 있는 함수형 인터페이스이다. 람다에서 리턴하는 값이 들어간다.

```java
public static<T> Stream<T> generate(Supplier<T> s) {}
```

이때 생성되는 스트림은 크기가 정해져있지 않고 무한하기 때문에 특정 사이즈로 최대 크기를 제한해야 한다.

```java
Stream<String> generatedStream = Stream.genrate(() -> "gen").limit(5);
//[el, el, el, el, el]
//5개의 "gen"이 들어간 스트림이 생성된다.
```


#### Stream.iterate()

iterate 메소드를 이용하면 초기값과 해당 값을 다루는 람다를 이용해서 스트림에 들어갈 요소를 만든다. 예제에서 30이 초기값이고 값이 2씩 증가하는 값들이 들어간다. 즉, 요소가 다음 요소의 인풋으로 들어간다. 이 방법도 스트림 사이즈 제한이 필요하다.

```java
Stream<Integer> interatedStream =
	Stream.interate(30, n -> n+2).limit(5); // [30, 32, 34, 36, 38]
```


### 중간 연산 메서드

대표적인 유형과 메서드
* 스트림 필터링 : filter(), distinct()
* 스트림 변환 : map(), flatMap()
* 스트림 제한  : limit(), skip()
* 스트림 정렬 : sorted()
* 스트림 연산 결과 확인 : peek()
* 타입 변환 : asDoubleStream(), asLongStream(), boxed()

아래 예제들은 모두 다음과 같은 리스트 데이터를 사용한다고 가정한다.

```java
List<Integer> intList = Arrays.asList(1, 2, 3);
List<String> strList = Arrays.asList("Park", "Kim", "Lee", "Kang");
```

#### filter(), distinct()

* 스트림 요소를 필터링 하기 위한 메서드

> filter()는 스트림 요소 마다 비교문을 만족하는 요소로 구성된 스트림을 반환한다. 즉, 특정 조건에 맞는 값만
추리기 위한 용도로 사용한다. distinct()는 요소들의 중복을 제거하고 스트림을 반환한다.

```java
intList.stream().filter(x -> x<=2).forEach(System.out::println); // 1, 2
Arrays.asList(1, 2, 3, 2, 5).stream().distinct().forEach(System.out::println); // 1, 2, 3, 5
```

#### map()

* 스트림의 각 요소마다 수행할 연산을 구현할 때 사용한다.

```java
intList.stream().map(x -> x*x).forEach(System.out::println); // 1, 4, 9
intList.stream().filter(x-> x<2).map(x -> x*x).forEach(System.out:println); // 1, 4
```


#### flatMap()

* 기존 요소를 새로운 요소로 대체한 스트림을 생성한다.

```java
Arrays.asList(intList.Arrays.asList(2, 5)).stream()
        .flatMap(i -> i.stream())
        .forEach(System.out::println);

strList.stream()
        .flatMap(message -> Arrays.stream(message.split("an")));
```


#### limit()

> 스트림의 시작 요소로부터 인자로 전달된 인덱스 까지의 요소를 추출해 새로운 스트림을 생성한다.

```java
intList.stream().limit(2).forEach(System.out::println); // 1, 2
```


#### skip()

> 스트림의 시작 요소로부터 인자로 전달된 인덱스 까지의 요소를 추출해 새로운 스트림을 생성한다.

```java
intList.stream().skip(2).forEach(System.out::println);
```


#### sorted()

> 스트림 요소를 정렬하는 메서드로 기본적으로 오름차순으로 정렬한다.

```java
IntStream.of(14, 11, 20, 39, 23)
        .sorted()
        .boxed()
        .collect(Collectors.toList());
// [11, 14, 20, 23, 39]
```

인자를 넘기는 경우와 비교해보자.
스트링 리스트에서 알파벳 순으로 정렬한 코드와 Comparator를 넘겨서 역순으로 정렬한 코드이다.

```java
List<String> lang =
    Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift");

lang.stream()
        .sorted()
        .collect(Collectors.toList());
// [Go, Groovy, Java, Python, Scala, Swift]
        
lang.stream()
        .sorted(Comparator.reverseOrder())
        .collect(Collectors.toList());
// [Swift, Scala, Python, Java, Groovy, Go]
```

```java
Arrays.asList(1,4,3,2).stream().sorted().forEach(System.out::println); // 1,2,3,4
Arrays.asList(1,4,3,2).stream().sorted((a,b) -> b.compareTo(a)).forEach(System.out::println); // 4,3,2,1
Arrays.asList(1,4,3,2).stream().sorted( (a,b) -> -a.compareTo(b)).forEach(System.out::println); // 4,3,2,1
```

#### peak()

결과 스트림의 요소를 사용해 추가로 동작을 수행한다.

> 원본 스트림을 이용하는 것이 아니므로 스트림 연산 과정에서 중간 중간 결과를 확인할 떄 사용할 수 있다.
최종 연산인 forEach() 처럼 반복해서 요소를 처리하는 메서드이며 
중간 연산이므로 최종연산 메서드가 실행되지 않으면 지연되기 때문에 반드시 최종연산 메서드가 호출되어야 동작한다.

> 앞의 filter() 예제를 보면 최종 연산으로 forEach()를 이용해 출력하는데 
만일 최종 연산이 forEach()가 sum() 이나 다른 최종 연산이라면 값을 출력해볼 방법이 없다. 이 경우 peak() 사용한다.

```java
int sum = intList.stream().filter(x -> x<=2)
        .peak(System.out::println)
        .mapToInt(Integer::intValue).sum();
System.out.println("sum: " + sum);
```


---
### 최종 연산 메서드

대부분의 최종연산은 결과값만 리턴되므로 별도의 출력문을 연결해 사용하기 어렵다.
대표적인 유형과 메서드는 다음과 같다.


* 요소의 출력 : forEach()
* 요소의 소모 : reduce()
* 요소의 검색 : findFirst(), findAny()
* 요소의 검사 : anyMatch(), allMatch(), noneMatch()
* 요소의 통계 : count(), min(), max()
* 요소의 연산 : sum(), average()
* 요소의 수집 : collect()

#### forEach()

> 스트림 요소들을 순환하면서 반복해서 처리해야 하는 경우 사용한다.

```java
intList.stream().forEach(System.out::println); //1, 2, 3
intList.stream().forEach(x -> System.out.printf("%d : %d\n", x, x*x)); // 1 : 1, 2 : 4, 3 : 9
```


#### reduce()

> map과 비슷하게 동작하지만 개별연산이 아니라 누적연산이 이루어진다는 차이가 있다.

두개의 인자 n, n+1을 가지며 연산결과는 n이 되고 다시 다음 요소와 연산하게 된다.
즉, 1, 2 번째 요소를 연산하고, 그 결과와 3번째 요소를 연산하는 방식이다.

```java
int sum = intList.stream().reduce((a, b) -> a + b).get();
System.out.println("sum : " + sum); // 1, 2 = 3    3, 3 = 6
```


#### findFirst(), findAny()

> 두 메서드는 스트림에서 지정한 첫번째 요소를 찾는 메서드이다.

보통 filter()와 함께 사용되고 findAny()는 parallelStream()에서 병렬 처리 시 가장 먼저 발견된 요소를 찾는 메서드로
결과는 스트림 원소의 정렬 순서와 상관 없다.

```java
strList.stream().filter(s -> s.startsWith("K")).findFirst().ifPresent(System.out::println); // Kim
strList.parallelStream().filter(s -> s.startsWith("K")).findAny().ifPresent(System.out::println); // Kim or Kang
```


#### anyMatch(), allMatch(), noneMatch()

> 스트림 요소 중 특정 조건을 만족하는 요소를 검사하는 메서드

원소 중 일부, 전체 혹은 일치하는 것이 없는 경우를 검사하고 boolean 값을 리턴한다.
noneMatch()의 경우 일치하는 것이 하나도 없을 때 true

```java
boolean result1 = strList.stream().anyMatch(s -> s.startsWith("K")); // true
boolean result2 = strList.stream().allMatch(s -> s.startsWith("H")); // false
boolean result3 = strLoist.stream().noneMathc(s -> s.startsWith("T")); // true
```


#### count(), min(), max()

> min(), max()의 경우 Comparator 를 인자로 요구하고 있으므로 기본 Comparator 들을 사용하거나
직접 람다 표현식으로 구현해야 한다.

```java
intList.stream().count(); // 3
intList.stream().filter(n -> n != 2).count();  // 2
intList.stream().min(Integer::compare).ifPresent(System.out::println);
intList.stream().max(Integer::compareUnsigned).ifPresent(System.out::println);
```

#### sum(), average()

> 스트림 원소들의 합계를 구하거나 평균을 구하는 메서드이다.

reduce() 와 map() 을 이용해도 구현이 가능하다. 이경우 리턴값이 옵셔널이기 때문에 ifPresent()를 이용해 값을 출력할 수 있다.

```java
intList.stream().mapToInt(Integer::intValue).sum();  //6
intList.stream().reduce((a, b) -> a + b).ifPresent(System.out::println);  // 6

intList.stream().mapToInt(Integer::intvalue).average();  // 2
intList.stream().reduce((a, b) -> a + b).map(n -> n/intList.size()).ifPresent(System.out::println);  // 2
```


#### collect()

> 스트림의 결과를 모으기 위한 메서드로 Collectors 객체에 구현된 방법에 따라 처리하는 메서드이다.

최종 처리 후 데이터를 변환하는 경우가 많기 때문에 잘 알아 두어야 한다. 용도별로 사용할 수 있는 Collectors의 메서드는 다음과 같다.

* 스트림을 배열이나 컬렉션으로 변환 : toArray(), toCollection(), toList(), toSet(), toMap()
* 요소의 통계와 연산 메서드와 같은 동작을 수행 : counting(), maxBy(), minBy(), summingInt(), averagingInt()
* 요소의 소모와 같은 동작을 수행 : reducing(), joining()
* 요소의 그룹화와 분할 : groupingBy(), partitioningBy()

```java
strList.stream().map(String::toUpperCase).collect(Collectors.joining("/")); 
strList.stream().collect(Collectors.toMap(k -> k, v -> v.length()));  // {kim=3, park=4, kang=4}

intList.stream().collect(Collectors.counting());
intList.stream().collect(Collectors.maxBy(Integer::compare));
intList.stream().collect(Collectors.reducing((a, b) -> a + b));  // 6
intList.stream().collect(Collectors.summarizingInt(x -> x));  //IntSummaryStatistics{count=3, sum=6, min=1, average=2.000000, max=3}
```

* toMap() 을 사용해서 문자열 스트림의 값을 키로 하고 문자열의 길이를 값으로 하는 맵으로 변환한다.
* counting, maxBy, reducing 은 각각 count(), max(), reduce() 메서드와 동일한 결과이다.
* summarizingInt 는 IntSummaryStatistics 를 리턴하며 count, sum, min, average, max 값을 참조할 수 있다.
* groupingBy는 특정 조건에 따라 데이터를 구분해서 저장한다.
* partitioningBy는 특정 조건으로 데이터를 두그룹으로 나누어 저장한다.

---
### 참고

https://dinfree.com/lecture/language/112_java_10.html

https://futurecreator.github.io/2018/08/26/java-8-streams/

---

