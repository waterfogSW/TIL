# Static

static은 클래스 변수라고도 불리며 다음과 같은 특징을 갖고 있다.

- 모든 인스턴스에 공유 되며 인스턴스가 없어도 직접 접근이 가능하다.
- 인스턴스가 아니라 클래스에 속하게 된다.
- 클래스를 사용 하기 이전에 이 변수들은 미리 메모리를 할당 받아 있는 상태가 된다.
- final class 변수는 상수로 치환 되어 Runtime Constant Pool에 값을 복사한다.
- static 변수는 Method Area의 Class Variable에 저장된다
  - 기본형이 아닌 static 클래스형 변수는 레퍼런스 변수만 저장되고 실제 인스턴스는 Heap에 저장되어 있다. 그 후 이 인스턴스의 변수를 저장하기 위해 Heap에 메모리가 확보가 되고 Heap의 인스턴스가 Method Area의 어느 클래스 정보와 연결되는지 설정 하게 된다.



## Static의 Method Area할당 시점

.java파일은 JVM의 컴파일러(javac)에 의해 java bytecode인 .class파일로 변환된다.
컴파일된 바이트 코드들은 ClassLoader를 통해 Rumtime Data Area에 적재되는데, static 키워드가 붙은 멤버는 Runtime Data Area의 Method Area(=Static Area)에 할당된다.

## Runtime Data Area

Runtime Data Area 는 다음과 같이 크게 5개 영역으로 나뉜다.

- Method Area(=Static Area, Class Area)
  - 프로그램 실행 중 클래스가 사용되면 JVM은 해당 클래스 파일을 읽어서 분석하여 클래스의 인스턴스 변수, 메소드 코드 등을 Method Area에 저장한다. 이 때 클래스 변수도 이 영역에 함께 생성된다.
- Stack Area
  - 데이터의 참조값이 할당된다(heap 영역에 대한 레퍼런스 변수)
  - 원시타입의 데이터가 값과 함께 할당된다.
  - 각 Thread는 자신만의 stack을 가진다
- Heap Area
  - 긴 생명주기를 가진 데이터들이 저장된다. 모든 객체는 heap영역에 생성된다.
  - 몇개의 스레드가 존재하든 단 하나의 heap영역에 존재한다
- Native Method Stack Area
- PC Register
