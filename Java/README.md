# Java 관련 지식
- [Call by value, Call by reference](#call-by-value,-call-by-reference)
- [Garbage Collection](#garbage-collection)
- [JVM](#jvm)
- [OOP](#oop)
- [String StringBuffer StringBuilder](#string-stringbuffer-stringbuilder)
- [Strong Reference Weak Reference](#strong-reference-weak-reference)

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


## JVM

### JVM이란?
- Java Virtual Machine (자바 가상머신)
- java코드를 컴파일하여 얻은 Java Byte Code를 해석하여 OS가 이해할 수 있는 기계어로 바꿔주는 역할
- Byte Code는 JVM 위에서 OS 상관 없이 실행됨
- java의 큰 장점이 됨 (OS 종속되지 않음, 어떤 디바이스든 JVM 위에서 실행 가능)
- JVM은 자동으로 메모리 관리를 해줌

### JVM 구조
1. Class Loader
- java 바이트코드를 읽어서 JVM의 Execution Engine이 사용할 수 있도록 Runtime Data Area에 적재  

2. Execution Engine (실행 엔진)
- 클래스를 실행시키는 역할
- 바이트코드를 실제로 JVM내부에서 기계가 실행할 수 있는 형태로 변환
- Interpreter, JIT (Just in Time)  

3. Runtime Data Areas
- JVM이 프로그램을 수행하기 위해 OS로부터 할당받은 메모리 공간
- PC Register, JVM Stack, Native Method Stack은 스레드마다 하나씩 생성
- Heap, Method Area는 모든 스레드 공유

**PC Register**  
현재 수행중인 JVM 명령의 주소를 가짐  
스레드가 시작할 때 생성    

**JVM Stack**  
프로그램 실행과정에서 임시로 할당되었다가 메소드를 빠져나가면 바로 소멸되는 특성의 데이터 저장  
ex) 지역변수, 매개변수, 메소드 정보, 연산 중 발생하는 임시 데이터  

**Native Method Stack**  
스레드에서 네이티브 방식의 메소드가 실행되는 경우 여기에 쌓임(C/C++ 메소드 등)  

**Heap**  
동적 할당 데이터 저장 (객체, 배열 등)  
Heap에 할당된 데이터는 [GC](#garbage-collection)의 대상  

**Method Area**  
JVM이 시작할 때 생성  
class, interface, method, field, static변수의 바이트코드 등을 보관  

![jvm](https://user-images.githubusercontent.com/38305511/139861445-8f593f8e-aac0-4f66-b773-6fc434f00caf.png)  
### JVM 프로그램 실행 과정
1. JVM이 OS로부터 필요한 메모리 할당받음
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 읽어들여 자바 바이트코드(.class)로 변환
3. Class Loader로 Class 파일을 JVM으로 로딩
4. 로딩된 Class 파일은 Execution Engine을 통해 해석됨
5. 해석된 바이트코드는 Runtime Data Areas에 배치되고 수행이 이루어짐

## OOP

### OOP란
- Object-Oriented Programming
- 프로그램을 어떻게 설계해야 하는지에 대한 방법론
- 프로그램을 수많은 **객체**라는 단위로 나누고 이 객체들의 상호작용 표현

### OOP의 특성
**1. 상속 (Inheritance)**
- 자식클래스가 부모클래스를 물려받고 기능을 추가하며 확장하는 개념
- 코드의 재사용성 증대

**2. 추상화 (Abstraction)**
- 공통된 특징을 파악해 인식의 대상으로 삼음
- 공통된 속성이나 기능을 묶음
- 추상메소드, interface 등

**3. 다형성 (Polymorphism)**
- 같은 형태이지만 다른 기능을 가질 수 있음
- 기능 확장에 용이

#### Overriding (오버라이딩)
수퍼클래스에서 구현된 메소드를 서브클래스에서 동일한 이름으로 재정의

#### Overloading (오버로딩)
클래스 내에서 이름이 같지만 서로 다르게 동작하는 메소드를 사용하는 것 (매개변수의 개수, 타입 으로 구분)

**4. 캡슐화 (Encapsulation)**
- 하나의 객체에 대해 그 객체가 특정한 목적을 위해 필요한 변수나 메소드를 하나로 묶는 것 (데이터 캡슐화)
- 정보의 **은닉화**(외부에서 객체의 상태를 변경하는 것을 막음)가 목적
- 프로그램 개발 시 **높은 응집도**(변경 시 해당 클래스만 변경)와 **낮은 결합도**(다른 클래스들의 간섭이 낮음)가 필요한데, 낮은 결합도를 캡슐화가 유지시켜줌
클래스 내에서 이름이 같지만 서로 다르게 동작하는 메소드를 사용하는 것 (개수나 타입이 다른 매개변수 사용)

## String StringBuffer StringBuilder
### String
- 불변(immutable)의 속성을 가짐
- 문자열을 수정하는 시점에 새로운 String 인스턴스 생성
- 문자열 추가, 삭제, 수정 등의 연산이 빈번하게 발생하면 Heap 메모리에 임시 Garbage가 생성되어 힙 메모리 부족으로 성능에 안좋은 영향을 끼침

### StringBuffer
- 가변(mutable)성을 가짐
- .append() .delete() 등의 API를 이용하여 동일 객체 내에서 문자열 변경이 가능
- 동기화 키워드를 지원하여 멀티쓰레드 환경에서 안전함 (thread-safe)

### StringBuilder
- 가변성
- 동기화를 지원하지 않지만 단일 스레드에서 성능은 StringBuffer 보다 뛰어남

## Strong Reference Weak Reference
- Strong Reference  
일반적으로 new를 통해서 객체를 생성하게 되면 생기게 되는 참조  
강한 참조를 통해 참조되고 있는 객체는 가비지 컬렉션의 대상에서 제외된다.  

- Weak Reference
대상 객체를 참조하는 경우가 WeakReferences 객체만 존재하는 경우 GC의 대상이 됨  
다음 GC 실행시 무조건 힙 메모리에서 제거됨  

WeakReference<MyClass> weakRef = new WeakReference<MyClass>(ref); 
