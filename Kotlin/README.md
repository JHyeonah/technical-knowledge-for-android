# Kotlin 관련 지식
- [Scope Function](#scope-function)
- [직접참조와 get()](#직접참조와-get())
- [Companion Object](#companion-object)

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
