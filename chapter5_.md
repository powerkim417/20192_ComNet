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
  - D(v): 시작점에서 v까지의 현재 기준 경로 비용
  - p(v): 시작점에서 v까지의 경로에서 v 이전 노드
  - N': 현재까지 최소 비용 경로가 정해진 노드의 집합(visited)
  
- 알고리즘

  - 초기화
    - 시작점 u는 경로가 0이므로 N'에 넣는다.
    - 다른 모든 노드 v에 대해
      - v가 u와 인접한다면 D(v)를 c(u, v)로 초기화하고,
      - v가 u와 인접하지 않다면 D(v)를 ∞로 초기화한다.
  - 반복
    1. N'에 있지 않은 노드 중 D(w)가 가장 작은 노드 w를 택한다.
    2. w를 N'에 추가한다.
    3. w와 인접하고, N'에 있지 않은 모든 노드 v에 대해 D(v)를 갱신한다.
       - D(v) = min( D(v) , D(w) + c(w, v) )
    4. 모든 노드가 N'에 들어갈 때까지 1~3을 반복한다. 

- 예시

  - ![image-20191119145334287](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119145334287.png)

  - ![image-20191119150818729](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119150818729.png)

    - <u>라우터 u 기준</u> 최단경로 트리 및 포워딩 테이블

      ![image-20191119151145174](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119151145174.png)

- 특징

  - 알고리즘 복잡도
    - 각 반복: N'에 없는 모든 노드에 대해 확인해야 한다.
    - 총 n + n-1 + ... + 1 = n*(n+1)/2 회의 비교 발생: O(n^2)
    - 힙을 이용하여 더 효율적으로 구현하면 O(nlogn) 까지 나온다!
  - oscillation 가능
    - 링크 비용과 전송되는 트래픽 양이 같을 때!
    - link cost가 바뀌면 shortest path로 패킷이 몰리는 현상 발생
    - Ex) ???
      - ![image-20191119160544146](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119160544146.png)
      - 그림2에서 C→A로 가는 최단경로가 B에서 D로 바뀌면 패킷이 D로 몰리게 됨
      - 그러면 congestion이 발생하여 link cost가 올라가고 C→A로 가는 최단경로가 다시 B로 바뀜
      - 이렇게 C→A로 가는 경유지가 B가 되었다가 D가 되었다가 반복되는 현상

### 5.2.2 Distance vector(DV) Routing Algorithm

#### Bellman-Ford equation(Dynamic Programming)

- 용어
  - $d_x(y)$:= x에서 y로 가는 최소 비용 경로의 비용 = $min_v \{c(x,v) + d_v(y)\}$ (<u>x의 이웃인 모든 v</u>에 대해 해당 식의 최소값)
- 예시
  - ![image-20191119162147264](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119162147264.png)
    - d_v(z) = 5, d_x(z) = 3, d_w(z) = 3을 미리 구해놓으면
    - d_u(z) = min {c(u,v)+d_v(z) , c(u,x)+d_x(z) , c(u,w)+d_w(z)} = min{7, 4, 8} = 4
    - 최소를 만족하는 노드가 최단경로의 다음 hop이 되며, 포워딩 테이블에 사용됨

#### Distance vector algorithm

- 용어
  -  Dx(y): x에서 y까지 최소 비용 <u>예측</u> 값
    - x는 자신의 거리 벡터 Dx = [Dx(y): y є N] 를 maintain
  - 노드 x
    - 각 이웃 v에 대하여 모든 c(x ,v)를 알고 있음
    - 자신의 모든 이웃의 거리 벡터 Dv를 모두 maintain
- 기본 아이디어
  - 시간마다 각 노드는 자신의 예측 거리 벡터를 이웃에게 보냄
  - 만약 x가 자신의 이웃으로 부터 새 예측 거리 벡터를 수신하면 자신의 거리 벡터를 B-F 방정식을 사용하여 갱신한다.
  - 이 동작이 계속되면 각 예측 거리 벡터 Dx(y)는 실제 최소 비용 dx(y)에 근접해진다.
- 특징
  - 반복적이고 비동기적
    - 각 local iteration은 local link cost의 변화 또는 이웃의 거리벡터 갱신 메시지에 의해 발생
  - 분배적
    - 각 노드는 자신의 거리벡터가 변경되었을 때만 이웃 노드에게 알린다.
  - 각 노드는
    1. local link cost의 변동 또는 이웃으로부터 메시지를 기다림(wait)
    2. 예상 거리벡터를 재계산(recompute)
    3. 예상 거리벡터가 변경되었을 경우 이웃들에게 알림(notify)
- 예시
  - ![image-20191119165349449](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119165349449.png)
  - 업데이트된 값만 주변 노드에 전달됨
  - 시간이 지나면 모두 수렴함

#### Link cost가 변하는 경우

1. 노드가 local list cost의 변화를 감지한다.
2. 라우팅 정보를 갱신하고, 거리벡터를 재계산한다.
3. 거리벡터에 변동이 생겼을 경우, 이웃 노드에 알린다.

##### 비용이 감소하는 소식은 네트워크에 빨리 전파됨(한번에)

![image-20191119165918629](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119165918629.png)

- t0: y가 link cost 변화를 확인하고 DV를 업데이트하고 이웃에게 알림
- t1: z가 y의 변화를 수신하고, 자신의 테이블을 업데이트한다. z는 x까지의 최소비용을 계산하고(5→2), 이웃에게 알림
- t2: y는 z의 갱신 메시지를 받아서 테이블 갱신. y의 최소 비용 변경이 없으므로 추가 메시지를 보내진 않음

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119171137197.png" alt="image-20191119171137197" style="zoom:80%;" />

##### 비용이 증가하는 소식은 네트워크에 느리게 전파됨(1씩 증가)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119171010669.png" alt="image-20191119171010669" style="zoom:80%;" />

- count-to-infinity 문제! (위의 예시 경우 44회 반복 후 수렴)

- Poisoned reverse???

  - Z가 Y를 통해 X로 갈 때

    - Z는 Dz(x)가 무한대라고 Y에게 말한다(Y가 X를 통해 Z로 가는걸 막기 위해)
    - Z는 X로 가는 경로를 Z-X로 바꾸고, Dz(x)=50임을 Y에 알린다.
    - 그러면 Y가 Dy(x)를 51로 바꾸지만 반대 경로(Z-X)를 poison하여 Dy(x)가 무한대임을 알린다.

    이게 뭐야....

![image-20191119171155101](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119171155101.png)

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

> ### *Intra-AS routing: OSPF*

- 라우팅의 크기를 크게 하는 방법
  - 현재까지는 이상적인 라우팅(모든 라우터가 같고, 평면 구조 네트워크)에 대해서만 배웠지만, 실제 라우팅은 규모가 매우 크다
- Scale(10억개 이상의 목적지)
  - 라우팅 테이블에 모든 도착지를 저장할 수 없다.
- 자율적 관리
  - 각 네트워크 관리자는 자신의 네트워크 내에서 라우팅을 제어할 수 있어야 함
- 라우팅 스케일링의 인터넷적 접근
  - 라우터를 자율적 시스템(Autonomous Systems, AS = domain)으로 통합
  - intra-AS routing(AS 내부)
    - 같은 AS(네트워크) 내의 호스트 및 라우터 간 라우팅
    - AS에 있는 모든 라우터는 같은 intra-domain 프로토콜 내에서 실행되어야 한다.
      - 다른 AS에 있는 라우터는 다른 intra-domain 프로토콜에서 돌아가도 됨
    - 게이트웨이 라우터: AS의 말단에 있는 라우터로, 다른 AS의 라우터와 연결되어 있음
  - inter-AS routing(AS 간)
    - AS들 간에서 일어나는 라우팅
    - 게이트웨이는 intra-domain 라우팅과 함께 inter-domain 라우팅도 실시한다. 

#### Interconnected ASes

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119201612621.png" alt="image-20191119201612621" style="zoom:80%;" />

- 포워딩 테이블은 intra-AS 라우팅 알고리즘과 inter-AS 라우팅 알고리즘 모두에 의해 구성된다.
  - intra-AS 라우팅으로 AS 내의 목적지를 결정
  - inter-AS & intra-AS 라우팅으로 AS 외부의 목적지를 결정

#### Inter-AS tasks

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119203148025.png" alt="image-20191119203148025" style="zoom:80%;" />

- AS1 내의 라우터가 AS1 외부로 나가야 하는 데이터그램을 수신하는 경우
- 라우터는 게이트웨이 라우터로 이를 전달해야 함. 그런데 어느 게이트웨이 라우터로?
- AS1이 해야할 것(**inter-AS 라우팅이 해야 할 일**)
  - **AS2와 AS3을 경유하여 도달할 수 있는 목적지들을 학습**
  - **이를 AS1의 모든 라우터에게 전파**

#### Intra-AS routing (= Interior Gateway Protocols, IGP)

- 가장 일반적인 intra-AS 라우팅 프로토콜
- 주로 사용되는 intra-AS 프로토콜
  - RIP: Routing Information Protocol
  - OSPF: Open Shortest Path First(IS-IS protocol과 같은 개념)
  - IGRP: Interior Gateway Routing Protocol
    - 2016년까지 Cisco사 전용

#### OSPF(Open Shortest Path First)

- "Open": 공개적으로 접근 가능한
- LS 알고리즘 사용
  - LS 패킷 전파
  - 각 노드는 전체 AS에 대한 토폴로지 맵을 가지고 있음
  - 다익스트라 알고리즘을 이용한 경로 계산
- 라우터는 AS에 있는 다른 모든 라우터에 OSPF link-state advertisement를 플러딩한다.
  - (TCP, UDP가 아닌) IP상에서 직접 OSPF 메시지를 전송
  - Link state: 각 연결된 링크
- IS-IS 라우팅 프로토콜: OSPF와 거의 비슷
- OSPF의 기능
  - <u>보안</u>: 모든 OSPF 메시지는 인증을 거침(잘못된 접근을 막기 위해)
  - 같은 cost의 <u>여러 경로가 허용</u>됨(RIP에서는 한가지 경로만 가능)
  - 각 링크에서 <u>서로 다른 TOS(Type Of Service)에 따라 다른 cost</u>를 적용할 수 있음!
    - Ex) 위성 링크의 비용을 TOS가 best-effort인 경우 낮게 하고, real-time인 경우 높게
  - 큰 도메인에서 <u>계층적 OSPF</u> 적용
    - <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119214012138.png" alt="image-20191119214012138" style="zoom:80%;" />
    - 이 전체가 하나의 큰 AS
    - Local area와 backbone으로 구성됨
      - area 내에서만 link-state advertisement 가능
      - 각 노드는 속한 area의 토폴로지를 알고 있으며, 다른 area에 대해서는 그곳을 향한 방향(최단 경로)만 알고 있음
    - Area border router: 자신의 area와 network 간의 거리를 가지고 있으며???, 이를 다른 area border router에 advertise한다.
    - Backbone router: backbone에 한정하여 OSPF 라우팅 실행
    - Boundary router: 다른 AS들과 연결됨(게이트웨이 라우터?)

## 5.4 Routing among the ISPs: BGP (42~56)

> ### *Inter-AS routing: BGP*

### 5.4.1 Internet inter-AS routing: BGP

- BGP(Border Gateway Protocol): 사실상 인터넷 표준에 해당하는 inter-AS 라우팅 프로토콜
  - "인터넷들을 하나로 붙여주는 풀 역할"
- BGP가 각 AS에게 제공하는 수단
  - eBGP: 인접 AS로부터 서브넷 도달 가능성 정보를 얻음
  - iBGP: 모든 AS 내부 라우터에 도달 가능성 정보를 전파
  - 도달 가능성 정보와 정책을 기반으로 다른 네트워크들에게 "좋은" 경로를 결정!
- 서브넷이 나머지 인터넷에 자신의 존재를 광고할 수 있게 함

#### eBGP, iBGP connections

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\image-20191119225946877.png" alt="image-20191119225946877" style="zoom:80%;" />

- eBGP: 두개의 AS에 걸친 BGP 연결

- iBGP: 같은 AS 내의 라우터 간 BGP 연결
- 게이트웨이 라우터는 eBGP, iBGP 프로토콜 모두를 사용함



### 5.4.2 BGP path advertisement

### 5.4.3 BGP Route selection

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