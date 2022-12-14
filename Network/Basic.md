# 네트워크 기초

## 처리량과 지연시간

- 처리량 : 링크를 통해 전달되는 단위 시간당 데이터량
- 지연시간 : 요청이 처리되는 시간

## 네트워크 토폴로지

- 트리 토폴로지
  - 장점
    - 노드의 추가 삭제가 쉽다
  - 단점
    - 특정 노드에 트래픽이 집중될 때 하위 노드에 영향을 끼칠 수 있다
- 버스 토폴로지
  - 장점
    - 설치비용이 적다
    - 신뢰성이 우수하다
    - 노드의 추가 삭제가 쉽다
  - 단점
    - 스푸핑 : 네트워크의 스위칭 기능을 마비시켜 패킷을 악의적인 노드로 전달하도록 함
- 스타 토폴로지
  - 장점
    - 노드 추가 삭제가 쉽다 
    - 패킷 충돌 발생 가능성이 적다
    - 장애가 발생하더라도 중앙 노드가 아닐 경우 다른 노드에 영향을 끼치는 것이 적다
  - 단점
    - 중앙노드에 장애가 발생하면 전체 네트워크를 사용할 수 없고 설치비용이 비싸다
- 링형 토폴로지
  - 장점
    - 노드수가 증가되어도 네트워크 상의 손실이 거의 없다
    - 패킷 충돌 발생 가능성이 적다
    - 노드 고장을 쉽게 찾을 수 있다
  - 단점
    - 구성 변경이 어렵다.
    - 장애발생시 전체 네트워크에 영향을 준다
- 메시 토폴로지
  - 장점
    - 장애가 발생하더라도 다른 여러개의 경로로 네트워크를 계속 사용할 수 있다
    - 트래픽 분산 처리가 가능하다
  - 단점
    - 구축 비용과 운용 비용이 고가이다

## 네트워크 분류

- LAN 
  - 근거리 통신망으로 같은 건물이나 캠퍼스 같은 좁은 공간에서 운영
- MAN
  - 대도시 지역 네트워크
- WAN
  - 광역 네트워크
