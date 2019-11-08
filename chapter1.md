# Chapter 1. Introduction

## 1.1 What is the Internet? (3~9)

### 1.1.1 구성 요소로 본 인터넷

- 연결된 컴퓨팅 디바이스 = 호스트(host) = 종단 시스템(end systems)
  - 네트워크 어플리케이션을 실행시키는 수단
  - Ex) PC, 서버, 무선 노트북, 스마트폰
  - 통신 링크와 패킷 교환기의 네트워크로 연결됨
- 통신 링크
  - 섬유, 구리선, 라디오,위성 등..
  - 전송률(transmission rate, 링크 대역폭)
  - Ex) 무선 링크, 유선 링크
- 패킷 교환기(packet switch)
  - 패킷(데이터 뭉치)을 최종 목적지 방향으로 전달
  - Ex) 라우터, 스위치
- 인터넷(네트워크)
  - 상호 연결된 ISP(Internet Service Provider)
  - end system은 ISP를 통해 인터넷에 접속함
- 프로토콜: 메시지의 전송과 수신을 제어
  - Ex) TCP, IP, HTTP, Skype, 802.11
  - 종단 시스템, 패킷 교환기, 인터넷의 다른 구성요소들이 수행하는 것! 
- 인터넷 표준
  - RFC: Request for comments (인터넷 표준화 문서)
  - IETF: Internet Engineering Task Force (인터넷 표준화 기구)

### 1.1.2 서비스 측면에서 본 인터넷

- 어플리케이션에 서비스를 제공하는 인프라
  - Ex) 웹, VoIP, 이메일, 게임, e-커머스, social nets, ...
- 어플리케이션에 프로그래밍 인터페이스 제공
  - 앱 프로그램을 인터넷에 연결할 수 있음
  - 우편서비스와 유사한 서비스 옵션 제공

### 1.1.3 프로토콜이란 무엇인가?

- Network protocol: 프로토콜의 적용을 받는 인터넷의 모든 통신 활동
- 프로토콜이 정의하는 것
  - **양식**(포맷)
  - 네트워크 내에서 **메시지가 전달/수신되는 순서**
  - 메시지의 전달/수신 등의 이벤트에 대해 **취할 행동**
- "Hi" "Hi" "Got the time?" "2:00": human protocol
- "TCP 연결 request", "TCP 연결 response", "GET http:// ~~", "\<file\>": network protocol

## 1.2 Network edge (10~22)

- <u>Network edge</u>
  - 호스트, 즉 종단 시스템을 의미
  - 호스트는 클라이언트와 서버로 구분됨
  - Ex) 데이터센터와 같은 곳에 자주 있는 서버들
- <u>Access networks, physical media</u>
  - 유/무선 통신 링크
  - 종단 시스템을 edge router에 연결하는 방법
    - 주거용 접속망
    - 기관 접속 네트워크(학교, 회사 등)
    - 모바일 액세스 네트워크
  - 접근하는 네트워크의 대역폭(초당 비트수), 공유/전용 여부 확인!!
- Network core
  - 상호통신하는 라우터
  - Network of networks

### 1.2.1 Access Network

<u>종단 시스템</u>과 다른 종단 시스템까지의 경로상에 있는 <u>첫번째 라우터(edge router)</u>를 연결

#### DSL(Digital Subscriber Line)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1569505926240.png" alt="1569505926240" style="zoom:67%;" />

- 이미 존재하는 전화선을 central office DSLAM(DSL access multiplexer)으로 보냄
  - DSL 전화를 이용한 데이터는 인터넷으로 전송
  - DSL 전화를 이용한 음성은 전화망으로 전송
- Upstream transmission rate < 2.5 Mbps (일반적으로 1 Mbps 미만)
- Downstream transmission rate < 24 Mbps (일반적으로 10 Mbps 미만)

#### Cable network

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1569505948796.png" alt="1569505948796" style="zoom:67%;" />

- FDM(Frequency division multiplexing): 다른 주파수 대역에서 전송되는 다른 채널
- HFC(Hybrid Fiber Coax)
  - 비동기적(최대 30Mbps의 downstream 전송률, 최대 2Mbps의 upstream 전송률)
- 각각의 집을 ISP 라우터에 연결하는 케이블, 광섬유 네트워크
  - 각 집들은 케이블 헤드엔드에 대한 접속 네트워크를 공유함
  - DSL과 달리 central office에 전용 출입이 가능함

#### Home network

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1569506005438.png" style="zoom:67%;" />

#### Enterprise access networks (Ethernet)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1569506384594.png" alt="1569506384594" style="zoom:67%;" />

- 일반적으로 회사, 학교 등에서 사용
- 10Mbps, 100Mbps, 1Gbps, 10Gbps 의 전송률
- 오늘날 종단 시스템들은 일반적으로 이더넷 스위치에 연결되어 있음

#### Wireless access networks

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1569507004396.png" alt="1569507004396" style="zoom:67%;" />

- 공유 무선 접속 네트워크는 종단 시스템을 라우터에 연결

  - Base station(기지국)(=access point)을 경유!

- Wireless LANs

  - 빌딩에서 사용

  - 802.11b/g/n (WIFI): 11, 54, 450Mbps의 전송률
  - 802.11ay: 20~30Gbps (200Gbps)

- Wide-area wireless access

  - Telco 에서 제공 ???
  - 1~10Mbps
  - 3G, 4G: LTE

#### (Host)

데이터를 패킷 단위로 전송

- 어플리케이션 메시지를 받는다
- L 비트 길이의 패킷(더 작은 덩어리)으로 나눔(bit)
- 패킷을 접속 네트워크에 전송률 R의 속도로 전송함(bit/sec)
  - = link transmission rate = link capacity = link bandwidth
- 패킷 전송 지연(packet transmission delay) = L/R(sec)
  - L비트의 패킷을 링크에 전송하는데 걸리는 시간

### 1.2.2 Physical media

#### 주요 용어

- bit: 송신기/수신기 쌍 사이에서 전파됨
- physical link: 송신기와 수신기를 연결하는 수단
- guided media: 견고한 매체를 따라 파형을 유도(Ex. 구리 케이블(copper), 광섬유케이블(fiber), 꼬임쌍선(Twisted-pair), 동축케이블(coax))
- unguided media: 대기와 야외 공간으로 파형 전파(Ex. 무선 LAN, 디지털 위성 채널, 라디오)

#### 종류

- Twisted Pair Cable(꼬임쌍선, TP Cable): 2개의 절연 구리선

  - Category 5: 100Mbps, 1Gbps Ethernet

  - Category 6: 10Gbps
  - LAN에서 주로 사용(UTP)

- Coaxial cable(동축 케이블)

  ![1571470907970](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571470907970.png)

  - 동심원 형태의 구리선 2개
  - 양방향(bidirectional)
  - 광대역(broadband): 여러 채널 존재, HFC(Hybrid Fiber Coax)
  - 케이블 TV 시스템에서 주로 사용

- Fiber optic cable(광섬유 케이블)

  - 빛의 파동을 전하는 가는 유리 섬유. 하나의 파동이 하나의 bit를 의미
  - 고속 point-to-point(순서대로) 전송! (10-100Gbps)
  - 낮은 오류율
    - 리피터끼리 멀리 떨어져 있음???
    - 전자기 노이즈에 영향을 받지 않음

- Radio

  - 전자기 스펙트럼으로 신호 전달
  - 물리적 선로가 필요 없음!
  - 양방향
  - 전파 환경의 영향을 많이 받음(반사, 장애물, 간섭 등)
  - 종류: 지상 마이크로파(terrestrial microwave), LAN, 광역(wide-area) 네트워크, 인공위성

## 1.3 Network core (23~41)

- 상호 연결된 라우터들의 연결망(mesh)
- 패킷 교환: 호스트가 application layer message를 패킷 단위로 분할
  - 한 라우터에서 다음 라우터로 가는 링크를 통해 패킷을 전달
  - 각 패킷은 링크의 최대 용량으로 전송됨

### 1.3.1 Packet Switching

#### Store-and-forward 전송 방식

- 저장 후 전달
- 패킷의 모든 비트가 다 라우터에 도착한 뒤에 다음 링크로 전송되어야 함

- L비트 패킷이 R bps 속도로 전송될 때 총 L/R초 소요
- source - router 까지 가고, router에서 처리한 뒤 router - destination 까지 가므로 **end-end delay는 2*L/R** 이 된다!! (propagation delay가 없다는 가정)
- one-hop transmission delay(end-router???): L/R

#### Queueing delay & Packet loss

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571474416538.png" alt="1571474416538" style="zoom:67%;" />

- 링크 도착 속도가 링크의 전송률을 초과하는 경우
  - 패킷들이 queue되고 다음에 전송되기까지 기다리게 된다.(Queueing delay)
  - 만약 메모리 버퍼가 대기중인 패킷들로 꽉 차면 패킷(도착하는 or 대기중인)을 drop하여 패킷 손실이 발생한다.(Packet loss)

#### 2 Key Network-core functions

- Routing: 패킷을 전송할 때 패킷의 경로(source-destination)를 결정하는 것
- Forwarding: 패킷이 라우터의 입력링크에 도달했을 때 패킷을 적절한 출력링크로 이동시키는 것

###  1.3.2 Circuit Switching

end간에 통신을 제공하기 위해 경로상에 필요한 자원을 확보 또는 예약함(패킷 교환에서는 그러지 않음!)

- 출발지와 도착지 사이에서 "call"을 위해 예약하고자 할당된 end-end resource

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571479440423.png" alt="1571479440423" style="zoom:67%;" />

- 위 link의 2번째 circuit, 오른쪽 link의 1번째 circuit을 사용
- 자원이 사용중일 때는 공유가 불가능(circuit-like (guaranteed) performance)
- call을 통해 사용하지 않으면 circuit segment는 idle상태가 된다.
- 전통적인 전화망에서 일반적으로 사용됨

#### FDM VS TDM

Circuit Switching에는 2가지 방법의 Multiplexing(다중화) 존재!

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571480368035.png" alt="1571480368035" style="zoom:67%;" />

- FDM(Frequency-Division Multiplexing, 주파수-분할 다중화): 각 연결에 고정된 주파수를 제공
- TDM(Time-Division Multiplexing, 시분할 다중화): 특정 시간에 특정 연결에게만 모든 주파수를 제공 

#### Packet Switching VS Circuit Switching

  (예약이 필요없는 레스토랑)           (예약을 해야 하는 레스토랑)

- 패킷 교환이 더 많은 사용자가 네트워크를 사용할 수 있게 함!

- Ex) 1Mb/s 링크가 있고, 각 사용자는 활성 상태일 때 100kb/s 속도이고 전체 시간중 10%만 활성 상태.

  - circuit switching은 100kb/s가 항상 각각의 사용자에게 예약이 되어야 하므로 **10명**만 가능.

  - packet switching의 경우

    - 한 사용자가 활동중일 확률은 10% = 0.1

    - 35명의 사용자가 있을 때 10명 초과의 사용자가 동시에 활동할 확률(=터질 확률)은 0.0004(0.04%)

      - $$
        1 - \sum_{n=0}^{10}\binom{35}{n}0.1^n(0.9)^{35-n}
        $$

    - 이렇게 35명중 10명을 넘는 경우도 매우 희박하므로 35명도 사실상 허용된다고 봄

    - 즉 packet switching이 3배 이상의 사용자를 허용!

    - 35명을 넘는다면?

      - 36명일 경우 위의 확률은 0.0005, 37명일 경우 0.0007, 40명일 경우 0.001
      - 되긴 되는건가..?

- 패킷 스위칭의 이점

  - 데이터 폭증에 좋음??
    - 자원의 공유
    - call 설정이 없이 간단하다
  - congestion(혼잡)이 많아도 가능 -> packet delay, loss???
    - 신뢰할수 있는 데이터 전송과 혼잡 제어를 위해 프로토콜 필요
  - circuit같은 특성을 어떻게 추가할까?
    - 오디오/비디오 앱에 대역폭 보장이 필요하다
    - 아직 해결되지 않음.. (chapter 7에서 다룸)

- Reserved resources(circuit switching) VS On-demand allocation(packet switching) 을 비유???

### 1.3.3 Internet Structure: Network of networks

- 종단 시스템은 ISP(인터넷 서비스 공급자)를 통해 인터넷에 연결
  - 주택, 회사, 대학 ISP
- Access ISP는 차례대로 상호 연결되어야 한다
  - 두 호스트가 서로 패킷을 보낼 수 있도록 하기 위해
- 그 결과로 생성된 network of network는 매우 복잡한 구조
  - 경제와 국가적 정책에 의해 구조가 발전함
- 수백만개의 access ISP를 어떻게 연결할까?
  1. O(N^2)로 전부 연결하는 방식
  2. 중앙의 1개의 Global ISP에 연결되는 방식
     - Customer ISP와 Provider ISP는 경제적 약속을 맺음
  3. 여러개의 Global ISP가 있는 방식
     - Global ISP간의 직접 연결인 peer link
     - 연결 지점인 IXP(Internet eXchange Point)
     - access net들과 Global ISP를 연결하는 regional net
     - 자신 소유의 네트워크를 소유한 content provider networks(Ex. google, MS, akamai, ...). 이들은 end user에게 서비스와 컨텐츠를 제공
     - <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571485861687.png" alt="1571485861687" style="zoom:67%;" />
     - 중앙에는 적은 갯수의 well-connected large network 존재
       - tier-1 commercial ISP (Ex. Level 3, Sprint, AT&T, NTT), national & international 범위
       - Content Provider Network (Ex. Google): 데이터센터를 인터넷에 연결하는 사설 네트워크. 종종 tier-1, regional ISP를 우회한다

## 1.4 Delay, Loss, Throughput in network (42~54)

- 패킷이 라우터 버퍼에 queue된다
  - 패킷이 라우터에 도착하는 속도가 라우터에서 보내는 용량을 초과하는 경우 delay 발생
  - 패킷은 queue되고 다음 턴까지 대기한다.
- 이 때 버퍼가 꽉 차있는 경우에 패킷이 또 들어오려 하면 loss가 발생

### 1.4.1 Packet Delay

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571487950099.png" alt="1571487950099" style="zoom:67%;" />

- d_proc(노드 처리 지연)
  - 패킷 헤더를 조사(check bit error)하고, 패킷을 어디를 보낼지 결정하는 시간
  - 일반적으로 msec 보다 작다 
- d_queue(큐잉 지연)
  - 큐에서 링크로 전송되기까지를 기다리는 시간
  - 라우터의 congestion level에 영향을 받는다
  - traffic intensity = a*L/R(a: 평균 패킷 도착 속도) 일 때
    - <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571543227258.png" alt="1571543227258" style="zoom:67%;" />
    - aL/R이 0 근처일 때: d_queue가 작다.
    - aL/R이 1을 향해 갈 때: d_queue가 크다.
    - aL/R이 1보다 클 때: 처리할 수 있는 양보다 더 큰 작업량이 도착하므로 평균 delay는 무한대! 
      - 즉, traffic intensity가 1보다 크지 않게 시스템을 설계해야한다
- d_trans(전송 지연)
  - L: packet 길이 (bits)
  - R: 링크의 대역폭 (bps)
  - d_trans = L/R
- d_prop(전파 지연)
  - d: 물리적 링크의 길이
  - s: 전파 속도(~2*10^8 m/s)
  - d_prop = d/s

#### 카라반으로 비유하여 이해하기

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571490323494.png" alt="1571490323494" style="zoom:67%;" />

- 10개의 차(bit)가 100km/h 속도로 전파된다.
  - Toll booth는 차를 12초동안 서비스(전송)해줌 (bit transmission rate)
  - car ~ bit ; caravan ~ packet
  - 모든 caravan을 toll booth에서 통과시키는데 걸리는 시간: 120초 = 2분
  - 마지막 차가 1번째 toll booth에서 2번째 toll booth으로 전파되는데 걸리는 시간: 100km / 100km/h = 1시간 = 60분
  - caravan이 toll booth 앞에 나열되기까지 저장되는 시간: 62분
- 1000km/h 속도로 전파될 경우
  - Toll booth가 차를 1분동안 통과시킨다면
  - 모든 차가 첫번째 booth를 통과하기 전에 차가 두번째 booth에 도착할 것인가?
    - Yes, 7분(1분동안 통과하고 6분동안 100km 지남) 뒤에 첫번째 차는 두번째 booth에 도착하지만, 아직 3대의 차가 첫번째 booth에 남아있다.

#### 실제 인터넷에서의 delay

- traceroute 프로그램: end-end 인터넷 경로를 따라 중간 지점마다 발생하는 delay를 측정
  - 사이에 n-1개의 라우터가 있고, 모든 i(1~n)에 대해
    - 도착지까지 가는 경로의 i번째 라우터에 3개의 패킷을 보냄
    - i번째 라우터까지 갔다가 다시 돌아옴
  - 큐잉 지연은 시간에 따라 변하므로 i까지의 지연이 i+1까지의 지연보다 클 수 있음

### 1.4.2 Packet Loss

- Queue(=buffer)는 유한한 크기를 가지고 있음
- 꽉 찬 queue에 도착한 packet은 손실(drop, lost)된다.
- Lost packet은 이전 노드나 출발지에 의해 재전송될 수도 있고, 재전송되지 않을 수도 있다

### 1.4.4 Throughput(처리량, 전송률)

- 송/수신자 간에 bit가 전송되는 속도(bits/time 단위)
  - Instantaneous: 특정 시간에서의 속도
  - Average: 특정 기간동안의 속도
- 원래는 많을 수록 좋지만 real-time의 경우 최소만 만족해도 됨
- Bottleneck link(병목 링크)
  - <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571544901029.png" alt="1571544901029" style="zoom:67%;" />
    - 앞의 링크는 R_s의 throughput, 뒤의 링크는 R_c의 throughput일 때
    - end-end throughput은 <u>둘 중 더 작은 값</u>이 된다. (빠르게 도착해도 밀리므로)
    - end-end 간에 N개의 링크가 있을 때도 N개 중 가장 작은 값이 된다.
  - <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571545230917.png" alt="1571545230917" style="zoom: 67%;" />
    - 일반적인 인터넷 네트워크의 구조
    - 10개의 서버와 10개의 클라이언트가 1개의 코어 링크를 통과할 때
    - 이 경우 최소값은 min(R_c, R_s, R/10) 이지만, 일반적으로 R이 매우 큰 값을 가지므로 R_c 또는 R_s가 bottleneck이 된다.

## 1.5 Protocol layers, service models (55~62)

### 1.5.1 Protocol "Layers"

네트워크는 다양한 구성 요소가 존재: host, router, link, app, protocol, hw/sw, ...

이러한 네트워크 구조를 조직하기 위해 만든 것이 Layer(계층 구조)!

- 각각의 Layer는 서비스를 의미
  - 자신의 레이어의 action과 자신의 하위 레이어의 서비스를 통해 서비스 제공
- **왜 Layering이 필요할까? 복잡한 네트워크 시스템을 다루기 위해!**
  - 명확한 구조 정의를 통해 복잡한 시스템 조각들을 식별하고 관계를 정의함
  - 모듈화는 시스템의 유지보수와 업데이트를 용이하게 만들어줌
    - 특정 부분의 구현을 변경하는 것은 다른 시스템에게 영향을 미치지 않음!

#### Internal Protocol Stack

Internet protocol은 OSI 7계층 중 Application(7), Transport(4), Network(3), Link(2), Physical(1) 총 5개 layer 존재 

1. Application: 네트워크 어플리케이션 지원

   - 한 end system의 application이 다른 end system의 application과 정보 패킷을 교환할 때 application protocol을 사용
     - <u>정보 패킷: 메시지(Message)</u>

   - Ex) FTP, SMTP, HTTP

2. Transport: 데이터 전송

   - 클라이언트와 서버 간에 message를 전송하는 서비스 제공
     - <u>트랜스포트 계층 패킷: 세그먼트(Segment)</u>
   - Ex) TCP(연결지향), UDP(비연결형)

3. Network: 한 호스트에서 다른 호스트로 datagram을 라우팅(패킷 스위치)

   - 출발지에서 Transport 계층 프로토콜은 Segment와 목적지 주소를 Network 계층으로 전달함
   - Network 계층은 목적지의 Transport 계층으로 세그먼트를 운반
     - <u>세그먼트 + 목적지 주소: 데이터그램(Datagram)</u>
   - Ex) IP, 라우팅 프로토콜

4. Link: 네트워크 요소간 데이터 전송

   - Network 계층에서 패킷을 이동할 때 Link 계층 서비스에 의존함
     - 각 노드에서 network 계층은 데이터그램을 아래의 link 계층으로 보내고,
     - link 계층은 그 데이터그램을 경로상의 다음 노드에 전달한다.
     - 다음 노드에서 link 계층은 그 데이터그램을 상위 network 계층으로 보낸다.
   - <u>링크 계층 패킷: 프레임(Frame)</u>
   - Ex) Ethernet, 802.111(WiFi), PPP

5. Physical: 실제 wire

   - 프레임 내부의 각 비트를 한 노드에서 다음 노드로 이동
   - 링크와 실제 전송 매체(ex. TP, fiber, ...)에 의존
     - Ex) 이더넷은 여러가지 물리계층 프로토콜을 갖고 있음!(TP를 위한 것, Coax를 위한 것, ...) 각각 다른 방식으로 링크 반대편으로 이동
   - <u>비트(bit)</u>

#### ISO/OSI reference model

Application(7) - Transport(4) 사이에 Presentation(6), Session(5) 존재

- Presentation: 애플리케이션이 데이터의 의미를 이해하도록 함
  - Ex) 암호화, 압축, 기계별 규약
- Session: 동기화, 체크포인트 지정, 데이터 교환으로부터의 복구

인터넷 스택에는 이 두가지 레이어가 없다!

- 꼭 필요하지 않으며, 만약 필요할 경우 이 서비스들은 애플리케이션 차원에서 구현이 가능하다.

### 1.5.2 Encapsulation

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571554673739.png" alt="1571554673739" style="zoom:67%;" />

- 캡슐화: 전송할 데이터에 상위 계층의 통신 프로토콜 정보를 추가하여 하위 계층으로 전송하는 것
- 역캡슐화: 하위 계층에서 추가한 정보와 수신한 데이터를 분리하여 상위 계층으로 전송하는 것
- 캡슐화를 하는 이유: 각 네트워크 계층 별로 사용되는 프로토콜 정보를 추가하여 전달하기 위한 준비
  - 운반하고 싶은 데이터에 헤더를 추가해 캡슐을 만듦

## ~~1.6 Networks under attack: security~~

## 1.7 History (63~68)

어차피 역사는 시험에 안나올테니.. 대강 흐름만!

- 1961~1972: 초기의 Packet switching 개발
- 1972~1980: 인터네트워킹, 신규 및 독점 네트워크
- 1980~1990: 새로운 프로토콜, 네트워크의 확산
- 1990, 2000's: 상용화, 웹, 새로운 애플리케이션
- 2005~현재!