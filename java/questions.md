# Java

---

## Java를 사용하는 이유?
JVM 기반의 플랫폼 독립성, 강력한 객체지향 설계, 방대한 생태계와 안정성, GC를 통한 메모리 관리 자동화

---

## Java 컴파일 과정
javac로 .java > .class(바이트코드) 컴파일 > Class Loader가 JVM에 로딩 > 바이트코드 검증 > Execution Engine이 인터프리터/JIT로 기계어 변환 및 실행

인터프리터 - 바이트코드를 한 줄씩 읽어서 그때그때 기계어로 변환해 실행하는 방식
장점 : 프로그램 시작이 빠름(전체를 미리 변환할 필요 없이 바로 실행)
단점 : 같은 코드가 반복 실행돼도 **매번 다시 해석**하므로, 반복이 많아질수록 느려짐

JIT(Just-In-Time Compiler) - 인터프리터의 "반복 해석" 비효율을 보완하는 컴파일러. 이름 그대로 실행 시점에 컴파일
장점 : 반복 실행되는 코드의 성능이 크게 향상됨
단점 : 컴파일과 최적화에 시간·메모리를 쓰므로, 잠깐만 실행되는 코드엔 오히려 부담

---

## Java 컴파일과 빌드의 차이
Compile - 소스코드를 바이트코드로 변환하는 단일 단계
Build - 컴파일을 포함해 테스트, 의존성 관리, 패키징(JAR/WAR), 배포 준비까지 아우르는 전체 과정.

---

## JVM이 뭔지?
.java > .class(바이트코드) > JVM이 각 OS에 맞게 실행.
Class Loader → Runtime Data Area → Execution Engine, Garbage Collector 로 구성되어있음.

Class Loader(클래스 로더)는 JVM Data Area의 메소드 영역으로 클래스 파일을 동적으로 로드하고 링크를 통해 배치하는 작업을 수행
Execution Engine(실행 엔진)은 메소드 영역에 배치된 .class 파일(바이트 코드)들은 JVM에 의해 실행엔진에 제공되고, 정의된 내용대로 바이트 코드를 실행. 이때 로드된 바이트 코드를 실행하는 런타임 모듈이 바로 실행엔진.
Garbage Collector(가비지 컬렉터)는 힙 메모리 영역에 생성된 객체들 중에서 참조되지 않은 객체들을 탐색 후 제거.
Runtime Data Area(런타임 데이터 영역) - JVM이 프로그램을 수행하기 위해 OS로부터 할당받는 메모리 영역

---

## JVM 메모리 구조
Runtime Data Area를 세분화하며 총 5가지로 구분됨

메소드영역(method area) - 클래스 정보, 변수 정보, static으로 선언한 변수가 저장되고 모든 Thread가 공유하는 영역
힙 영역(heap area) - 동적으로 생성된 객체와 배열이 생성되는 영역이며, gc의 대상이 되는 공간
스택 영역(stack area) - 지역 변수나 메서드의 매개변수, 임시적으로 사용되는 변수, 메서드의 정보가 저장되는 공간
PC Register - Thread가 시작될 때 생성되며, 현재 수행중인 JVM의 명령어 주소를 저장하는 영역(Thread가 어떤 부분을 어떤 명령어로 수행할지를 저장하는 공간)
Native Method Stack - Java가 아닌 다른 언어(C/C++ 등)로 작성된 코드를 위한 영역.

---

## Pass by value VS Pass by reference
자바는 무조건 Pass by value이다. 단, 객체를 넘길 때 **주소값(참조)을 복사해서** 전달하기 때문에 reference처럼 보이는 것뿐

Pass by value(값에 의한 전달)란? 복사 된 데이터를 전달하여 구성함으로써, 값을 수정하여도 원본의 데이터에는 영향을 주지 않도록 하는 방식.

Pass by reference(참조에 의한 전달)란? 주소 값을 전달하여 실제 값에 대한 Alias를 구성함으로써, 값을 수정하면 원본의 데이터가 수정되도록 하는 방식

---

## Stack과 Heap에 뭐가 저장되나? Pass By Value로 이걸 설명하면?
- Stack : 메서드 호출 정보, 지역변수, 매개변수, 참조변수(주소값). 스레드마다 독립적
- Heap : 실제 객체. 모든 스레드가 공유

```java
void method(Person p) {  // p는 Stack에 있는 참조변수(주소 복사본)
    p.setName("Kim");    // 주소를 따라가 Heap의 진짜 객체를 수정 → 원본도 바뀜
    p = new Person();    // p에 새 주소를 넣음 → 원본과 무관해짐
}
```

자바는 주소값(참조)을 복사해서 Stack에 넘기므로 Pass by Value임. 그 주소가 가리키는 Heap 객체를 수정하면 원본도 바뀌지만, 참조변수 자체에 새 객체를 대입하면 원본과 끊김. 이게 참조처럼 보이지만 사실은 값 전달인 이유.

---

## GC는 어떻게 동작하나? 어떤 객체가 대상인가? Young/Old를 왜 나누나?
GC는 더 이상 참조되지 않는 객체를 자동으로 메모리에서 제거하는 기능. 어떤 변수도 가리키지 않는 객체가 대상.

Heap을 Young + Old 영역으로 나누는 이유는 실제로 만들어지는 객체 대부분이 아주 잠깐 쓰이고 금방 버려진다는 사실 때문.
- Young 영역 : 새로 생긴 객체가 들어감. 대부분 여기서 금방 사라짐 → 자주, 빠르게 청소
- Old 영역 : Young에서 여러 번 살아남은 객체가 승격됨 → 가끔 청소

이렇게 나누면 자주 죽는 객체는 작은 영역에서 빠르게 처리하고, 오래 사는 객체는 따로 관리해 GC 효율이 올라감.

---

## new String("a") vs 리터럴 "a"
- 리터럴 `"a"` 는 String Pool(Heap 내 특별 영역)에 저장되고, 같은 리터럴은 재사용됨
- `new String("a")`는 Pool과 별개로 Heap에 매번 새 객체를 만듦

```java
String a = "a";
String b = "a";
String c = new String("a");
a == b        // true  (둘 다 Pool의 같은 객체)
a == c        // false (c는 new로 만든 별도 객체)
a.equals(c)  // true  (값은 같음)
```

그래서 문자열 비교는 `==`(주소 비교) 아닌 `.equals()`(값 비교)를 써야 함.
