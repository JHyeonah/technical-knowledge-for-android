# Android 관련 지식
- [Android 4대 컴포넌트](#android-4대-컴포넌트)
- [Context란](#context란)
- [ViewModel](#viewmodel)
- [메모리 누수](#메모리-누수)
- [IntentFilter](#intentfilter)
- [RxJava Scheduler](#rxjava-scheduler)

## Android 4대 컴포넌트
- 각 컴포넌트들은 하나의 **독립적인 형태**로 존재
- 각 컴포넌트들은 **고유의 기능**을 수행
- 각 컴포넌트들은 **인텐트**를 통해 서로 상호작용

1. 액티비티 (Activity)  
사용자가 애플리케이션과 상호작용하는 단일화면  
즉, **사용자와 상호작용**을 담당  
애플리케이션은 반드시 하나 이상의 액티비티를 포함  
인텐트(Intent)를 통해 다른 애플리케이션의 액티비티 호출 가능  

2. 서비스 (Service)  
**백그라운드**에서 어떠한 작업을 처리하기 위해 사용  
**메인 스레드**에서 동작  
네트워크와 연동 가능  
별도의 UI를 가지지 않으며 백그라운드에서 수행됨  
애플리케이션이 종료되어도 이미 시작된 서비스는 백그라운드에서 계속 동작  

3. 방송 수신자 (BroadCast Receiver)  
**안드로이드 OS로부터 발생하는 각종 이벤트와 정보 핸들링**  
사용자 디바이스의 특수한 이벤트에 대한 처리나 배터리 부족, 문자 수신과 같은 정보를 받아 처리할때 동작

4. 콘텐트 제공자 (Content Provider)  
**데이터를 관리하고 다른 애플리케이션의 데이터를 제공**  
특정한 앱이 사용하고 있는 DB를 공유하기 위해 사용  
앱 간의 데이터 공유를 위해 표준화된 인터페이스 제공  
SQLite DB/Web/파일 입출력을 통해 데이터 관리  
프로바이더는 데이터의 읽기, 쓰기 권한이 있어야 앱에 접근 가능  

**Intent 란?**  
컴포넌트 간에 작업 수행을 위한 정보를 전달, 상호 통신  
컴포넌트에 Action, Data 등을 전달  
인텐트를 통하여 다른 애플리케이션의 컴포넌트 활성화 가능  
EX) 액티비티 간의 화면 전환  


## Context란
어플리케이션의 **현재 상태의 맥락(Context)** 을 의미  
Context는 생성된 객체가 **어떤 일이 일어나고 있는지** 알 수 있음  
Context를 사용하여 Activity, Application의 정보 알 수 있음  

#### 크게 두가지 역할을 하는 **추상 클래스(Abstract Class)**
- 앱에 관하여 시스템이 관리하고 있는 정보에 접근 : 리소스, 클래스, 어플리케이션 환경정보
- 안드로이드 시스템 서비스에서 제공하는 API 호출 : startActivity(), bindService()

#### 사용 예시
1. 리소스 로딩
2. 새 액티비티 생성
3. 뷰 생성
4. 시스템 서비스 얻기

#### Context의 종류
- Application Context :   
어떤 context보다 오래 유지됨  
Application 라이프사이클과 연관  
Singleton Instance -> 앱이 죽기 전까지 동일한 객체 반환
- Activity Context :   
액티비티의 라이프사이클과 연관

#### 메모리 누수 
메모리 누수를 피하기 위해 Activity Context에는 수명이 긴 참조를 피하고, 되도록이면 Application Context를 사용해야 함  
Context가 정리되지 않은 호출로 생성된 무언가를 오래 유지하고 있을 경우 메모리 누수 발생 


## ViewModel
- Android JetPack 구성 요소
- 라이프사이클을 고려하여 UI관련 데이터 저장, 관리
- 화면전환과 같이 설정이 변경되어도 Data를 유지
- Activity가 끝날 때까지 사라지지 않고, View의 생명주기와 별개로 동작
- ViewModel은 Activity 생명주기 외부에 존재하므로, UI Context를 ViewModel에 저장한다면 Memory Leak 

## 메모리 누수
- 메모리가 할당되었지만, 사용이 끝난 후 되찾아올 수 없는 현상
- 앱에서 사용하지 않는 객체가 사용중인 객체를 참조하면서 GC가 할당된 메모리를 되찾아올 수 없는 현상
1. Broadcast Receiver
  - 액티비티에서 BroadCast Receiver 선언 후 해제하지 않으면 메모리 릭 발생 
2. Static Activity, 뷰 참조
  - activity나 뷰 컴포넌트(textview 등)을 static 변수에 참조한다면 메모리 릭 발생
3. Singleton 클래스 
  - Singleton 클래스에 activity context를 사용하면 메모리 릭 (application context 사용해야함)  
  - activity context를 꼭 사용해야 한다면 activity 종료 전 null 확인
4. Inner class 참조
  - inner class 를 static 변수에 넣지 말아야 함
  - static inner class로 선언해야함
  - activity/view에 WeakReference를 사용해야함
5. handler를 액티비티 종료 이후에도 사용
6. ViewModel에서 액티비티의 context나 view 참조

## IntentFilter
암시적 인텐트를 통해 사용자가 어떤 앱을 사용할 지 선택하도록 할 때 필요  
안드로이드 내부에서 수많은 앱들에 의해 수많은 인텐트가 발생, 이 중 자신에게 필요한 인텐트만 받음  
암시적 인텐트 : 컴포넌트의 이름을 알 수 없을 때 사용. 컴포넌트가 처리할 작업만을 명시  

## RxJava Scheduler
1. single
단일 스레드를 생성해 계속 재사용  
한번 생성된 스레드로 여러 작업 처리  

2. computation
CPU에 대응하는 계산용 스케쥴러  
오래걸리는 작업을 할 때 사용  
스레드 수는 프로세서 수와 동일  

3. io 
파일 입출력 등 IO 작업이나 네트워크 통신 시 사용  
필요할 때마다 스레드를 계속 생성하여 비동기 처리    
내부적으로 cachedPool을 사용하기 때문에 thread가 동시에 계속 늘어나면서 생성될수 있으며, 유휴 thread가 있을 경우 재활용됨  

4. trampoline
새로운 스레드를 생성하지 않고 현재 스레드에 무한한 대기 큐 생성  
이전 작업이 완료될 때까지 대기했다가 다음 작업 실행하므로 단일 스레드에서 동작하는 것 처럼 사용 가능  
unit test 시 사용  

5. newThread
요청을 받을 때마다 매번 새로운 스레드 생성  
