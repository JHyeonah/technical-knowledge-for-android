# Java 관련 지식
- [Call by value, Call by reference](#call-by-value,-call-by-reference)

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
