## ArrayList 특징

- 인덱스로 원소를 관리하고 원소를 빠르게 탐색할 수 있다.
- 하지만 중간에 원소를 추가하거나 삭제하게되면 원소의 인덱스가 밀리거나 줄어들게 되어 성능 측면에서 좋지 않다.

## 배열 vs 인덱스

- 배열은 크기가 고정되어있지만, ArrayList는 사이즈가 동적인 배열이다.
- 배열은 Primitive type과 Object 모두 담을 수 있지만, ArrayList는 Object만 담을 수 있다.
- 배열은 제네릭을 사용할 수 없지만, ArrayList는 타입 안정성을 보장해주는 제네릭을 사용할 수 있다.
- 배열은 element들을 할당하기 위해 assignment(할당) 연산자를 사용해야 하고, ArrayList는 add() 메서드를 통해 element를 삽입한다.

---
## 동작

ArrayList의 크기는 지정하지 않으면 기본 사이즈는 10이다.

ArrayList 생성 후 크기가 10을 넘으면 어떻게 될까?

```java
public boolean add(E e) {
        ++this.modCount;
        this.add(e, this.elementData, this.size);
        return true;
}    
    
private void add(E e, Object[] elementData, int s) {
  
        if (s == elementData.length) {
            elementData = this.grow(); 
        }
        elementData[s] = e;
        this.size = s + 1; 
}
```

add 메소드를 보면 세번째 파라미터에서 size를 받고 있다.
`size` 와 `elementData.length`를 비교하여 배열의 사이즈를 조정해야 하는지 체크한다.

만약 사이즈를 추가해야 한다면 grow 메소드를 호출한다.

```java
 private Object[] grow() {
        // 현재 사이즈에서 + 1을 인자로 넘긴다.
        return this.grow(this.size + 1);
    }

    private Object[] grow(int minCapacity) {
        // 기존 elementData를 newCapacity 길이만큼 새로 할당한 배열에 복사한다.
        return this.elementData = Arrays.copyOf(this.elementData, this.newCapacity(minCapacity));
    }

    private int newCapacity(int minCapacity) {
        int oldCapacity = this.elementData.length;     
        // 기존 용량 + 기존 용량/2 (우측 시프트 연산)
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        
        if (newCapacity - minCapacity <= 0) {
            if (this.elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) { // arrayList가 비어있을 때
                return Math.max(10, minCapacity);
            } else if (minCapacity < 0) {
                throw new OutOfMemoryError();
            } else {
                return minCapacity;
            }
        } else {//새로운 용량이 기존 용량의 +1 된 사이즈보다 크다면     
            
            return newCapacity - 2147483639 <= 0 ? newCapacity : hugeCapacity(minCapacity);
        }
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) {
            throw new OutOfMemoryError();
        } else {
        	 // 2147483639 byte를 넘어갈 경우 int 최대치 용량 부여 int 범위 넘어서면 outOfMemory
             return minCapacity > 2147483639 ? 2147483647 : 2147483639;
        }
    }
```

위 코드를 보면 핵심은 `기존 용량  + 기존 용량 / 2` 만큼 배열을 새로 생성한다.
즉, 현재 배열보다 1.5배의 크기로 메모리를 할당 받아 기존 배열을 복사하게 되는 것이다.

---
## 정리

ArrayList는 배열이 꽉차면 현재 크기보다 1.5배 큰 배열에 기존 배열을 복사하기 때문에 동적으로 원소를 추가할 수 있다.
그렇기 때문에 배열의 크기를 특정할 수 있을 때는 ArrayList 보다 배열을 사용하는 것이 좋다.

