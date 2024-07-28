## Exception

자바 예외는 크게 3가지로 나눈다

- Checked Exception
- Error
- Unchecked Exception

![[Pasted image 20240726104442.png]]

## Error 란?

에러는 시스템에 비정상적인 상황이 발생했을 때 나타난다.
OutOfMemoryError 나 StackOverflowError 와 같이 복구할 수 없는 것을 말한다.
에러는 개발자가 예측하기 쉽지 않고 처리할 수 있는 방법이 없다.

## Exception 이란?

예외는 프로그램 실행 중 개발자의 실수로 예기치 않는 상황이 발생했을 때 나타난다.

- ArrayIndexOutOfBoundsException, NullPointerException, FileNotFoundException 

예외는 Checked, Unchecked 두 가지로 나눌 수 있다.

위 그림에서 RuntimeException의 하위 클래스들이 Unchecked Exception이다.

### Checked Excetpion

체크 예외는 실행하기 전에 예측 가능한 예외를 말하고, 반드시 예외 처리(try catch or throw)를 해야 한다.

- IOException, ClassNotFoundExcption 등

### Unchecked Exception

 언체크 예외는 RuntimeException의 하위 클래스를 의미한다. 이것은 예외 처리를 강제하지 않는다. 실행 중에 발생할 수 있는 예외를 의미한다.

- ArrayIndexOutOfBoundsException, NullPointerException

### Rollback 여부

|                     | Checked                                                                                     | UnChecked                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 처리 여부               | 반드시 예외 처리                                                                                   | 명시적인 처리를 강제하지 않음                                                                                                                   |
| 확인 시점               | Compile 시점                                                                                  | Runtime 시점                                                                                                                         |
| 예외 발생 시 <br>트랜잭션 처리 | Rollback X                                                                                  | Rollback O                                                                                                                         |
| 대표 예외               | Exception 상속 받은 하위 클래스 중<br>Runtime Exception을 제외한 모든 예외<br>- IOExcetpion<br>- SQLException | Runtime Exception 하위 예외<br>- NullPointerException<br>- IllegalArgumentException<br>- IndexOutOfBoundException<br>- SystemException |

