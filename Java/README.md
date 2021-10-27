# Java 관련 지식
- [Call by value, Call by reference](#call-by-value,-call-by-reference)
- [Garbage Collection](#garbage-collection)

## Call by value, Call by reference
- Call by Value (값에 의한 호출)  
함수 호출 시 인자로 전달되는 변수의 **값**을 복사하여 함수의 인자로 전달  
복사된 인자는 **지역변수(Local value)** 의 특성을 가짐  
인자의 값이 변경되어도 실제 변수의 값은 변함 없음

- Call by Reference (참조에 의한 호출)  
함수 호출 시 인자로 전달되는 변수의 **주소**를 전달  
즉, 함수 안에서 인자의 값이 변경되면 전달된 변수의 값도 변경됨  

#### Java의 경우 함수에 전달되는 인자의 데이터 타입에 따라서 함수 호출 방식이 달라짐
- 원시 자료형 (Primitive type) : Call-by-value로 동작 (int, short, long 등)
- 참조 자료형 (Reference type) : Call-by-reference로 동작 (Array, Class Instance 등)  
-> 더 자세한 자료 https://gwi02379.tistory.com/13


## Garbage Collection

### 가비지 컬렉션이란?
Garbage : 유효하지 않은 메모리  
JVM의 가비지 컬렉터는 메모리 누수를 방지하기 위해 불필요한 메모리를 알아서 정리해줌 

### JVM Heap의 2가지 전제
- 대부분의 객체는 금방 접근 불가능한 상태(unreachable)가 된다  
- 오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재함  

즉, 객체는 대부분 일회성이며, 메모리에 오래 남아있는 경우는 드물다  
객체의 생존 기간에 따라 Young, Old 두가지 영역으로 물리적인 Heap 영역을 나눔

### Young 영역(Young Generation)
- 새롭게 생성된 객체가 할당되는 영역
- 대부분 객체가 금방 unreachable 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라짐
- Young 영역의 가비지 컬렉션 -> Minor GC

### Old 영역(Old Generation)
- Young에서 reachable 상태를 유지하여 살아남은 객체가 복사됨
- 복사되는 과정에서 Young 영역보다 크게 할당됨, 큰 만큼 가비지는 적게 발생
- Old 영역의 가비지 컬렉션 -> Major GC

### GC의 동작 방식
Young과 Old의 세부적인 동작은 다르지만 기본적으로 두가지 공통적인 단계를 따름  

1. Stop the World
- GC 실행을 위해 JVM이 어플리케이션의 실행을 멈춤
- GC를 실행하는 쓰레드를 제외한 모든 쓰레드의 작업 중단

2. Mark and Sweep
- Mark: 사용되는 메모리와 사용되지 않는 메모리 식별
- Sweep: 사용되지 않는 메모리 해제
