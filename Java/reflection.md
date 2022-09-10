## 리플렉션

구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 기본 API

런타임 시점에서 클래스 타입을 동적으로 바인딩 할 수 있는 기술이다.

## 원리

![Untitled](https://lh4.googleusercontent.com/6WFR269BkOXIAaZxySgJt4ZOFKDPgN_G5lOa0eKtpW3add440v_wTdqe-pbs64BimfEY4R3f6hKB_fCbTh55SD-OcgDFexD0dA8ulqblZe1aB0yY_7o7PQ-MKA)

자바는 컴파일 타임이 아니라 런타임에 클래스를 로드하고 링크한다. 컴파일 타임에서는 클래스파일을 컴파일러를 통해 바이트코드(.class)로 변경한다. 런타임에서 클래스는 최초로 사용되는 시점에 ClassLoader를 통해 JVM의 메서드 영역에 로드된다.

리플렉션은 메서드 영역에 접근하여 클래스의 정보를 가져오는 기술이다.

## 동작

### 리플렉션을 통해 알아낼 수 있는 정보는 다음과 같다

- ClassName
- Class Modifiers
- Package Info
- Superclass
- Implemented Interfaces
- Constructors
- MethodsFields
- Annotations

### 주의사항

- 리플렉션은 고급기능이기 때문에 시스템 자원을 많이 사용한다.
- 컴파일 타임에 확인되지 않는 에러를 발생시킬 가능성이 있다.
- 접근지시자를 무시할 수 있다.

[](https://www.baeldung.com/java-reflection)