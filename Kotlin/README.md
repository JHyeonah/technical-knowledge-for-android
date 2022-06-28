# Kotlin 관련 지식
- [Scope Function](#scope-function)
- [직접참조와 get()](#직접참조와-get())
- [Companion Object](#companion-object)
- [Sealed와 Enum](#sealed와-enum)
- [Volatile](#volatile)
- [Inline](#inline)
- [Invoke](#invoke)
- [Value class](#value-class)

## Scope Function
### Scope Function(범위지정함수)이란?
- 객체의 Context 내에서 코드블럭 {}을 실행하기 위한 목적
- 이 함수들은 람다식으로 호출할 때, 임시로 범위를 형성
- 이 범위 내에서 객체의 이름이 없어도 객체에 접근 가능
- 객체의 컨텍스트를 참조하는 방식, 리턴값으로 사용 여부 판단

### Context Object : this or it
- 컨텍스트 객체, 객체를 접근하는 방법
- this : run, with ,apply (receiver로 암시적 전달, this 생략 가능)
- it : let, also (parameter로 명시적 전달, it 생략 불가)

### 리턴 값 (return value)
- 코드블럭이 반환하는 결과
- apply, also -> 리시버(context) 객체를 마지막에 리턴 (자기자신 리턴), return 문에 사용될 수 있음
- let, run, with -> 람다 함수의 마지막 결과 리턴 (코드 블럭의 결과 리턴), 함수의 결과를 변수에 할당 가능

**1. .let**
- it으로 리시버 접근, 람다 함수 마지막 결과 리턴
- 지정된 값이 null이 아닌 경우에 코드를 실행해야 하는 경우 : person?.let { // null아닐 경우 실행 }
- 단일 지역변수의 범위 제한  
Dao().let { dao ->  
  dao.insert()  
}

**2. .apply**
- 수신객체 람다내부에서 수신객체의 함수를 사용하지 않고 수신 객체 자신을 다시 반환
- 초기화 과정 수행 시 많이 사용  
val peter = Person().apply {  
  name = "Peter"  
}

**3. .also**
- 수신객체 람다가 전달된 수신 객체를 전혀 사용하지 않거나 객체의 속성을 변경하지 않는 경우
- 객체의 프로퍼티에 값을 할당하기 전 데이터의 유효성 검사에 사용  
val author = author.also {  
  requireNotNull(it.age)  
  print(it.name)  
}

**4. .with**
- Non-nullable 수신 객체
- 결과가 필요하지 않은 경우 사용 (리턴값을 사용하지 않는 것 권장)  
val person: Person = getPerson()  
with (person) {  
  print(name)  
  print(age)  
}

**5. .run**
- 어떤 값을 계산할 필요가 있거나 여러 개의 지역 변수 범위 제한  
val inserted: Boolean = run {  
  val person: Person = getPerson()  
  personDao.insert(person)  
}  
- 매개변수로 전달된 명시적 수신객체를 암시적 수신객체로 변환  
fun printAge(person: Person) = person.run {  
  print(age)  
}  

## 직접참조와 get()
val a = MutableLiveData<String>()  
val b: LiveData<String> = a  
val c: LiveData<String>  
    get() = a  

- 직접 선언 시 (a, b) 서로 동일한 객체를 바라봄
- get() 선언 시 내부적으로 함수가 호출되고, 해당 함수의 결과값으로 a 객체 리턴
- int 같은 primitive type 이고, a의 초기값이 이후에 변경된다면 get() 호출 시 변경된 값을 가져옴, 직접 선언 시 원래 변수의 값이 변경되어도 변화 없음
  
## Companion Object
- Kotlin에는 static이 없지만, 패키지 수준의 최상위 함수와 객체 선언을 사용 가능  
- class 대신 object를 사용하면, 싱클턴 선언 가능
- companion object는 클래스 내부에 정의되는 object의 특수한 형태
- **클래스가 메모리에 적재되면서 함께 생성**되는 **동반(companion)** 객체
- const val로 선언된 상수는 자바로 변경 시 static 변수가 됨 
  
## Sealed와 Enum
### Sealed Class 
Super Class를 상속받는 Child class 종류 제한 가능  
Sealed Class 내에 정의된 class 이외의 형태는 가질수 없는, 제한된 계층구조 정의 (when문 사용시 else 필요없음)  
컴파일러에게 sealed class 안의 자식 말고 없다고 알려줌 (when문 사용시 else 필요없음)  
같은 서브클래스에서 서로 다른 값을 줄 수 있음
  
### Enum Class  
열거형, 각 서브 클래스들이 상수화 된 클래스  
각 서브클래스들이 고정적인 형태를 지님  
각 상수들이 부모 타입의 객체  
  
## Volatile
@Vollatile 어노테이션을 변수 선언 시 지정 가능  
**휘발성**이라는 뜻, 값을 메인 메모리에만 적재하게 됨  

#### 일반적인 변수 사용 시 
일반적인 변수 사용의 경우 내부적으로 성능 향상을 위해 메인 메모리로부터 읽어온 값을 CPU 캐시에 저장  
-> 멀티스레드 환경에서는 각 쓰레드에서 캐싱한 값이 상이할 수 있음  

#### Volatile 사용 시  
값을 메인 메모리에만 저장  
멀티 쓰레드 환경에서 메인 메모리의 값을 참조하므로 변수 값 불일치 문제 해결  
CPU 캐시를 참조하는 것보다 메인 메모리를 참조하는 것이 비용이 더 크기 때문에 성능은 떨어짐  
멀티 쓰레드 환경에서 하나의 Thread만 read&write 하고 나머지 쓰레드가 read 하는 상황에서 적합  
여러 쓰레드가 write 하는 상황에서는 synchronized를 통해 변수의 원자성을 보장해야 함  
**변수 값 일치를 보장해야 하는 경우에만 사용하는 것이 좋음**

## Inline
고차함수를 사용하면 런타임 패널티가 있기 때문에 함수 구현 자체를 코드 내부에 넣음으로서 오버헤드를 없앰  
-> 람다식마다 각각의 '객체'가 생성되어 메모리에 저장되고 해당 객체의 invoke() 함수가 각각 호출되기 때문에, 메모리 할당과 가상 호출에 의한 런타임 오버헤드 발생 가능  
* 고차함수 : 함수를 매개변수로 취하거나 결과로 반환하는 함수  
Inline 함수로 정의된 함수는 컴파일 단계에서 호출하는 방식이 아니라 **코드 자체가 복사되는 방식**으로 컴파일됨  
자바로 변환될 때 Function 인스턴스를 만들지 않고 함수 내부에 바로 삽입됨  
private 키워드를 사용하여 함수 정의 불가 -> internal로 가능
  
## Invoke
  - 이름 없이 간편하게 호출될 수 있는 함수
  ~~~java
class Test { 
      operator fun invoke(str: String) {
        print(str)
      }
}

fun main() {
      val test = Test()
      test("hello world")
}
  ~~~  
위와 같이 함수 이름 생략 가능
#### Operator 키워드
  - 연산자 : 이름을 부여한 함수임에도 불구하고 실행을 간편하게 할 수 있도록 지원  
  - 연산자에는 +, -, invoke 등이 있는데 이러한 연산자를 overloading 할 수 있도록 제공하는 키워드
  
#### 람다와 invoke  
람다는 컴파일되면서 코틀린에 정의된 FunctionN(P1, ..., PN, R) 형태로 변환됨  
Function 인터페이스에는 invoke만이 정의되어 있음
  
## Value class
함수 생성 시 마다 객체를 생성하여 호출하는 비용을 줄여줌  
JVM이 바이트코드로 컴파일하는 과정에서 객체를 제거하고 value class의 프로퍼티로 대체
