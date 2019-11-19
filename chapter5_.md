# Chapter 5. Network Layer: The Control Plane

2가지 함수 중 **Routing**에 대해 배움

## 5.1 Introduction (3~6)

- Network control plane 구조에 관한 2가지 접근

  - Per-router control(전통적)

    - **개별의 라우팅 알고리즘 구성 요소를 가진 각각의 모든 라우터**가 control plane에서 서로 상호작용하여 포워딩 테이블을 계산하는 방식

    <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191118021347062.png" alt="image-20191118021347062" style="zoom:80%;" />

  - Logically centralized control(소프트웨어 정의 네트워킹)

    - **고유한 컨트롤러(원격)가 로컬 제어 에이전트(CA)와 상호작용**하여 포워딩 테이블을 계산하는 방식

      <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191118021822282.png" alt="image-20191118021822282" style="zoom:80%;" />

## 5.2 Routing protocols (7~31)

- 라우팅 프로토콜의 목적: 라우터 네트워크를 통해 송신 호스트로부터 수신 호스트까지 가는 "좋은" 경로를 결정하는 것!
  - 경로: 패킷이 주어진 출발지 호스트부터 최종 도착지 호스트까지 이동하는 라우터의 순서
  - "좋은" 경로: <u>비용이 적게</u> 들고, <u>빠르고</u>, <u>혼잡이 적은</u> 경로

#### Graph abstraction of the Network

![image-20191118103134974](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191118103134974.png)

- 그래프: G = (N, E)
- 라우터의 집합: N = {u, v, w, x, y, z}
- 링크의 집합: E = {(u,v), (u,x), (v,x), (v,w), (x,w), (x,y), (w,y), (w,z), (y,z)}
- 그래프 추상화는 다른 network context를 나타내는데도 유용하다!
  - Ex) P2P의 경우 N은 peer의 집합이 되고, E는 TCP 연결의 집합이 됨
- **Cost(비용)**
  - c(x, x'): link (x, x')의 cost
    - Ex) c(w, z) = 5
  - cost는 모두 1일 수도, 대역폭에 반비례할 수도, 또는 congestion에 반비례할 수도 있다!
  - 경로 (x1, x2, ..., xp)의 cost = c(x1, x2) + c(x2, x3) + ... + c(xp-1, xp)
  - 핵심: u와 z 사이의 가장 cost가 적은 경로는 무엇일까?
    - 라우팅 알고리즘: 가장 cost가 적은 경로를 찾는 알고리즘

#### Routing algorithm classification

##### Global information vs. Decentralized information

- Global(중앙 집중형, logically centralized control)
  - 모든 라우터가 전체 토폴로지와 링크의 비용 정보를 알고 있을 때
  - "Link state" 알고리즘 사용
- Decentralized(분산형, per-router control)
  - 라우터가 물리적으로 연결된 이웃 라우터와 그 링크 비용 정보만 알고 있을 때
  - 계산이 반복적으로 실행되며, 이웃 라우터와 정보를 공유함
  - "Distance vector" 알고리즘 사용

##### Static vs. Dynamic

- Static
  - 경로가 시간에 따라 조금씩 변화함
- Dynamic
  - 경로가 빠르게 변화함
    - 주기적 업데이트
    - link cost의 변화에 대한 응답으로 변하는 것임

### 5.2.1 Link state(LS) Routing Algorithm

#### Dijkstra's algorithm

- 모든 노드가 네트워크 토폴로지와 링크 비용을 알고 있을 때 사용
  - link state broadcast를 통해 알려지며, 이를 통해 모든 라우터가 같은 정보를 가질 수 있게 됨
- 하나의 노드로부터 다른 모든 노드까지의 최소비용 경로를 계산하는 방식
  - 해당 노드의 포워딩 테이블을 통해
- k회의 반복이 지나면 적어도 k개의 도착지에 대한 최소비용 경로를 알 수 있게 됨
- 용어
  - c(x, y): x에서 y까지의 link cost. 두 노드가 인접해있지 않다면 ∞의 값을 가진다.

### 5.2.2 Distance vector(DV) Routing Algorithm

#### Bellman-Ford equation(Dynamic Programming)

#### Link cost가 변하는 경우

### Comparison of LS and DV algorithms

- 메시지 복잡도(이 알고리즘을 사용하려면)
  - LS: n개의 노드와 E개의 링크가 있다면 <u>O(nE)의 메시지가 전송되어야 함</u>
  - DV: 비용이 바뀐 링크에 인접한 노드 중 <u>최소 비용 경로에 영향을 준 경우에만 메시지 전송</u>
    - 수렴 시간이 다양할 수 있음
- 수렴 속도
  - LS: O(n^2) 시간복잡도의 알고리즘인 경우 <u>O(nE)의 메시지 전송을 통해 계산을 마침</u>
    - oscillation 발생 가능
  - DV: 수렴 시간이 다양할 수 있음
    - 자신의 이웃 노드에 관한 정보만을 통해 포워딩 테이블을 만들어야 하므로 <u>느림</u>
- Robustness(견고성): 라우터가 고장난 경우
  - LS
    - 라우터는 브로드캐스팅을 통해 자신과 인접한 링크에 대해서만 잘못된 정보를 전달
    - 라우터가 각자의 포워딩 테이블을 갖고 있기 때문에 라우팅 계산이 독립적
    - 따라서 <u>고장에 있어 견고함!</u>
  - DV
    - 반복적인 과정을 통해 잘못된 정보가 모든 네트워크로 전달되므로 <u>고장에 취약!</u>

## 5.3 Intra-AS routing in the Internet: OSPF (32~41)

## 5.4 Routing among the ISPs: BGP (42~56)

### 5.4.1 BGP의 역할

### 5.4.2 BGP 경로 정보 알리기

### 5.4.3 최고의 경로 설정

### 5.4.4 IP Anycast

### 5.4.5 라우팅 정책

### 5.4.6 조각 맞추기: 인터넷에서의 존재 획득

## 5.5 The SDN control plane (57~78)

### 5.5.1 SDN 제어 평면: SDN 컨트롤러와 SDN 네트워크 제어 응용들

### 5.5.2 OpenFlow Protocol

### 5.5.3 데이터 평면과 제어 평면의 상호 작용: 예제

### 5.5.4 SDN: 과거와 미래

## 5.6 ICMP: The Internet Control Message Protocol (79~81)

## 5.7 Network management and SNMP (82~87)

### 5.7.1 네트워크 관리 프레임워크

### 5.7.2 Simple Network Management Protocol (SNMP)