# Chapter 3. Transport Layer

## 3.1 Transport-layer services (3~6)

#### Transport services and protocols

- 서로 다른 호스트에서 실행중인 앱 프로세스 간에 논리적 통신을 제공
- 전송 프로토콜은 end system에서 실행됨
  - 송신자 측: 앱 메시지를 세그먼트 단위로 나눠 네트워크 계층으로 보냄
  - 수신자 측: 세그먼트들을 메시지로 조합하여 애플리케이션 계층으로 보냄
- 애플리케이션에 2개 이상의 전송 프로토콜 혼용 가능!
  - 인터넷의 경우 TCP, UDP(2개 동시에는 불가)

### 3.1.1 Transport VS Network Layer

- Network Layer: **호스트** 간의 논리적 통신
- Transport Layer: **프로세스** 간의 논리적 통신
  - Network layer service에 의존하고, 이를 향상시킨다.

#### '집'에 비유하여 이해하기

- Ann의 집에 있는 12명의 아이들과 Bill의 집에 있는 12명의 아이들이 서로 편지를 보낸다.
  - host: 집
  - process: 아이들
  - app message: 봉투 안의 편지
  - transport protocol: Ann, Bill(아이들이 쓴 편지를 수거하고, 자신의 집에 온 편지를 아이들에게 나눠줌)
  - network-layer protocol: 우편 배달 서비스(집배원 포함)

### 3.1.2 Internet transport-layer protocols

- TCP: 신뢰적, 순서적 전달
  - 혼잡 제어, 흐름 제어, 연결 지향
- UDP: 비신뢰적, 비순서적 전달
  - 기존의 최선의 노력 서비스인 "IP"와 크게 다르지 않음
- 지연 보장 및 대역폭 보장 서비스는 없음..

## 3.2 Multiplexing and demultiplexing (7~14)

- 송신자에서의 Multiplexing(편지 수거)
  - 소켓에서 데이터를 모으고, transport header를 더해(나중에 demuxing에 쓰임) 세그먼트를 만든다
- 수신자에서의 Demultiplexing(집배원의 편지 분배)
  - 헤더 정보를 보고, 받은 세그먼트를 올바른 수신 소켓에 전달한다

#### Demultiplexing 작동 원리

1. 호스트가 IP 데이터그램을 수신한다
   - 각 데이터그램은 출발지/도착지 IP 주소를 가지고 있음
   - 각 데이터그램은 하나의 전송계층 세그먼트를 가지고 있음
   - 각 세그먼트는 출발지/도착지 포트 번호를 가지고 있음
     - 출발지/도착지 포트번호, 그 외 헤더필드, 애플리케이션 데이터(페이로드)
2. 호스트는 이 IP주소와 포트번호를 통해 세그먼트를 적절한 소켓으로 보낸다.

#### Connectionless demultiplexing

연결이 따로 없는 형태에서 나눠주는 것(UDP)

- 생성된 소켓은 호스트 로컬 포트 번호가 있으며, 데이터그램을 UDP소켓을 통해 보내려면 목적지의 IP주소와 포트번호를 명시해야 한다.
- 호스트가 UDP 세그먼트를 받으면 세그먼트에 있는 목적지 포트번호를 확인하고, 해당 포트번호가 있는 소켓으로 세그먼트를 보낸다.
  - 소켓은 호스트별로 있는게 아니고 포트번호 별로 있다!
  - <u>출발지 IP주소나 포트 번호가 달라도 **같은 목적지 포트 번호를 가진 IP 데이터그램**은 **같은 목적지 소켓으로 가게 된다!**</u>

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571728536728.png" alt="1571728536728" style="zoom:67%;" />

#### Connection-oriented demultiplexing

연결이 생성된 형태에서 나눠주는 것(TCP)

- TCP 소켓은 4가지 요소의 집합(tuple)으로 식별된다.
  - 출발지 IP주소, 출발지 포트번호, 목적지 IP주소, 목적지 포트번호
- 수신자는 위의 4가지 요소를 모두 사용하여 적절한 소켓으로 보낸다.
- 서버 호스트는 동시에 여러 소켓을 사용할 수 있다.
  - 각 소켓은 4가지 요소로 식별됨
  - 웹 서버는 각 연결된 클라이언트에 대해 다른 소켓을 갖고 있다.
    - non-persistent HTTP는 각 요청에 대해 다른 소켓을 가짐
    - <u>즉, **목적지 IP와 포트 번호가 같아도 서로 다른 소켓에 demux 된다!**</u>
    - 한 프로세스에서 여러 소켓을 쓸 수 있음(threaded server)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571728592911.png" alt="1571728592911" style="zoom:67%;" />

## 3.3 Connectionless transport: UDP (15~19)

- 기본적인 인터넷 전송 프로토콜
- 최선형 서비스(best-effort service): 통신하는 호스트 간에 최대한 노력하지만, 어떤 보장도 하지 않음
  - UDP 세그먼트가 손실되거나, 순서가 바뀌어 전달될 수 있음
- 비연결성
  - UDP 송/수신자 간 핸드셰이킹 과정 없음
  - 각 UDP 세그먼트는 독립적으로 처리된다
- UDP가 사용하는 것
  - 스트리밍 멀티미디어 앱(loss tolerant, rate sensitive)
  - DNS
  - SNMP
- UDP에 신뢰적 전송을 하려면?
  - application layer에 reliability를 추가
  - application별 오류 복구

#### UDP를 사용하는 이유?

- 연결 설정이 없다(연결 설정은 지연을 유발)

- 간단하다: 송/수신자에게 연결 상태라는 개념이 없음, 즉 연결을 유지하지 않음
- 헤더의 크기가 작다
- Congestion control이 없다: UDP는 원하는 만큼 빠르게 전송 가능

### 3.3.1 UDP segment header

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571730518348.png" alt="1571730518348" style="zoom:67%;" />

- 헤더 구성 요소
  - 출발지 포트 번호
  - 도착지 포트 번호
  - (헤더 포함) UDP 세그먼트의 길이(byte)
  - Checksum

### 3.3.2 UDP Checksum

- 목적: 세그먼트가 전송되는 동안 filpped bit와 같은 오류가 발생했는지 검출
- 송신자
  - UDP 세그먼트의 비트를 16비트 단위 워드로 자르고 각각 다 더한다.
    - 반올림이 발생하면 맨 아래자리로 다시 넣는다(wrap around)
  - 더한 값의 1의 보수인 16비트 워드를 'Checksum'으로 결정!
- 수신자
  - UDP 세그먼트의 비트를 16비트 단위 워드로 자른 뒤, 모든 워드들과 Checksum을 더한다
    - 합이 1111.....111 이 나오지 않는 경우 오류 검출!
    - 합이 1111.....111 이 나오면 checksum에 의한 오류는 검출되지 않은 것
      - 그러나 다른 오류가 있을 수도 있음

## 3.4 Principles of reliable data transfer (20~55)

RDT(Reliable Data Tranfer protocol)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571734180656.png" alt="1571734180656" style="zoom:67%;" />

- (a)까지가 이미 제공된 서비스라면
- 우리는 (b), 즉 하위 계층에서의 신뢰적 데이터 전송을 구현하는 것을 목표로!

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571734778119.png" alt="1571734778119" style="zoom:67%;" />

- rdt_send(): 상위 계층에서 호출됨. 데이터를 rcv의 상위 계층으로 전달해야 함
- udt_send(): rdt에 의해 호출됨. 패킷을 unreliable channel을 통해 rcv로 보냄
- rdt_rcv(): 패킷이 채널의 rcv로부터 도착할 때 호출
- deliver_data(): rdt에 이해 호출됨. 데이터를 상위 계층으로 전달

### 3.4.1 Reliable data transfer 프로토콜 구축

Undirectional data transfer인 경우만 따짐! (실제로 제어 정보는 양방향)

FSM(Finite State Machine)을 사용하여 송/수신자 정보를 표현(state, event/actions)

- event: 상태 변화를 일으키는 이벤트
- action: 상태 변화시 취하는 조치

#### rdt1.0: 완벽하게 신뢰적인 채널 상에서의 신뢰적인 데이터 전송

- 완전히 신뢰 가능한 채널

  - 비트 오류 X, 패킷 손실 X

- 송신자, 수신자 각각에 대한 FSM(두 FSM을 분리!)

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571736697187.png" alt="1571736697187" style="zoom:67%;" />

  - 송신자는 아래 채널에 데이터를 보냄
  - 수신자는 위 채널로부터 데이터를 읽어옴

#### rdt2.0: 비트 오류가 있는 채널 상에서의 신뢰적 데이터 전송

- 아래 채널을 통과하는 과정에서 패킷의 비트가 바뀔 수 있다

  - checksum을 통해 비트 에러 검출

- 에러를 어떻게 복구할 것인가?

  - acknowlegement(ACK): 수신자가 올바른 패킷을 전달받았을 때의 응답
  - negative ack~~(NAK): 수신자가 받은 패킷에 오류가 있을 때의 응답
    - 이 경우 NAK에 대한 반환으로 패킷을 다시 송신자에게 보낸다

- rdt2.0에서 새로 나온 메커니즘

  - 오류 검출
  - 피드백: 수신자가 송신자에게 보내는 컨트롤 메시지(ACK, NAK)

- FSM

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571738896901.png" alt="1571738896901" style="zoom:67%;" />

  - rdt_rcv(rcvpkt): 패킷을 수신자로부터 받아들임
  - 송신자
    - 만약 ACK 패킷이 수신되면 송신자는 가장 최근에 전송된 패킷이 정확하게 수신되었다는 것을 알게 되고, 프로토콜은 상위 계층으로부터 데이터를 기다리는 상태로 돌아감
    - 만약 NAK 패킷이 수신되면 마지막 패킷을 재전송하고 ACK/NAK를 기다리는 상태로 돌아감
  - 수신자
    - 만약 수신한 패킷이 손상되었으면 NAK를 보내고 다시 원 상태로 돌아감
    - 만약 수신한 패킷이 손상되지 않았으면 ACK를 보내고 다시 원 상태로 돌아감

- rdt2.0의 치명적 결점: ACK/NAK가 손상될 경우

  - 송신자는 수신자에게 무슨 일이 일어났는지 모름!
  - 중복이 일어날 수 있으므로 재전송도 안됨

  - 중복 문제를 해결하기 위한 방법
    - ACK/NAK가 손상된 경우 송신자는 패킷을 다시 보내는데,
    - 송신자는 각 패킷에 seq num을 추가함!
    - 수신자는 패킷을 받으면서 중복인 패킷은 버림
  - stop-and-wait 방식: 송신자가 패킷 하나를 보내면 수신자의 응답이 올 때 까지 기다린다.

#### rdt2.1: seq_num을 붙여 중복 전송 후 처리

- FSM

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571740295428.png" alt="1571740295428" style="zoom:67%;" />![1571740356408](C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571740356408.png)

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571740412973.png" alt="1571740412973" style="zoom:67%;" />

  - 송신자
    - Sequence number가 패킷에 추가됨
    - 손상되지 않았고 ACK일 때만 넘어감
    - 손상되었거나 NAK이면 다시 보냄
    - seq_num은 1씩 증가하므로 mod 2해서 0과 1로만 상태를 처리해도 된다.
    - ACK/NAK의 손상 여부를 꼭 확인
    - seq num이 0일 때, 1일 때가 둘다 필요하므로 상태의 갯수가 2배
  - 수신자
    - 손상되지 않고, 해당 seq num을 갖고 있을 때 다음으로 넘어감
    - 패킷의 중복 여부 확인
      - 예상 seq_num과 받은 seq_num의 mod 2 값을 비교
    - 수신자는 자신의 마지막 ACK/NAK가 송신자에게 잘 전달되었는지 여부를 모름!

#### rdt2.2: NAK-free protocol

- rdt2.1과 같은 기능을 하지만 ACK만 사용함!

- 수신자는 NAK 대신 마지막으로 올바르게 수신한 패킷의 ACK를 보낸다.

  - 이 때, ACK된 패킷의 seq num을 포함해서 보내야 함

- 중복 ACK가 발견되었을 때 NAK가 들어온것 처럼 처리(현재 패킷을 다시 보냄)

- FSM

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571743003095.png" alt="1571743003095" style="zoom:67%;" />

  - 송신자
    - ACK 0을 기다리는데 isACK(rcvpkt, 1)가 온 경우: NAK
  - 수신자
    - ACK1을 받을 때만 0을 기다리는 상태로 전환됨

#### rdt3.0: 비트 오류와 손실 있는 채널 상에서의 신뢰적 데이터 전송

- 새로운 가정: 하위 채널에서 손실이 일어날 수도 있음!(data 또는 ACK)

- 접근: 송신자는 타당한 시간만큼 ACK를 기다려준다!

  - 어느 시간동안 ACK가 오지 않으면 그때 재전송
  - 만약 패킷이 손실된게 아니고 그냥 지연된 거였으면
    - 재전송을 하게 되는데, 이는 seq num으로 앞의 메커니즘에서 해결
    - 따라서 수신자는 ACK되는 패킷의 seq num을 명시해야 한다.
  - 카운트다운 타이머 필요

- FSM

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571747184466.png" alt="1571747184466" style="zoom:67%;" />

  - 송신자만 FSM이 달라짐!
    - start_timer: 패킷을 보낼 때 타이머 시작
    - timeout: 패킷을 보내고 ACK를 기다리던 중 시간이 만료되면 재전송 후 start_timer 리셋
    - stop_timer: ACK 도착시 타이머 멈춤

- 흐름

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571747454385.png" alt="1571747454385" style="zoom:67%;" />

  - (b) 패킷 1을 보냈는데 손실이 일어나면 timeout 후 다시 패킷 1을 보냄.
  - (c) 패킷 1에 대한 ACK를 보냈는데 손실이 일어나면 timeout 후 송신자가 다시 패킷 1들 보냄
    - 패킷 1 수신은 중복 감지..
  - (d) 패킷 1이 지연되고 있었는데 timeout이 발생하는 경우

### 3.4.2 Piplined protocols

#### rdt3.0의 성능

- rdt3.0은 성능이 안좋음
- Ex) R=1 Gbps 링크, RTT = 30ms, L=패킷당 8000비트
  - D_trans = L/R = 8000bits / 10^9bits/sec = 8 msec(전송 지연율)
  - U_sender = (L/R) / (RTT + (L/R)) = .008 / 30.008 = 0.00027 = 0.027%(송신자 이용률)
    - 매우 조금만 사용되고 있음
  - RTT=30ms이면 30ms마다 1kb 패킷을 보내는 것. 즉 1Gbps 링크에서 33kb/s 의 처리량으로 하고 있었던 것!
- 네트워크 프로토콜이 물리적 자원의 사용을 제한한다.

#### stop-and-wait operation

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571749425298.png" alt="1571749425298" style="zoom:67%;" />

- 마지막 패킷 비트가 도착했을 때 ACK를 보냄
- **U_sender = (L/R) / (RTT + (L/R))**

#### Pipelined Protocols

- 파이프라이닝: 확인응답을 기다리지 않고 여러 패킷을 전송하는 것

  - seq num의 범위가 커져야 한다

  - 송신 측과 수신 측은 한 패킷 이상을 버퍼링해야 한다

  - 2가지 기본적 접근 방법: GBN, SR

    <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571749644345.png" alt="1571749644345" style="zoom:67%;" />

    - 한번에 3개 패킷을 파이프라이닝했을 경우 U_sender가 3배 증가한다

- Go-back-N

  - 송신자는 한번에 N개 패킷을 전송할 수 있다
  - 누적 형식. cumulative ACK 형태로 보낸다.
  - 송신자는 가장 오래된 ACK되지 않은 패킷에 대한 타이머 존재
    - 타이머가 만료되면 모든 ACK되지 않은 패킷을 다시 전송

- Selective Repeat

  - 송신자는 한번에 N개 패킷을 전송할 수 있다
  - 수신자는 각 패킷에 대해 ACK를 보낸다.
  - 송신자는 각 unACKed 패킷에 대해 타이머 존재
    - 타이머가 만료되면 해당 unACKed 패킷만을 다시 전송

### 3.4.3 Go-Back-N(GBN)

- 송신자

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571750867034.png" alt="1571750867034" style="zoom:67%;" />

  - 한번에 여러개를 보낸 후 하나의 누적 ACK를 받고, 후속 데이터 전송
  - 송신자는 패킷 n에 대해 timeout 발생 시 패킷 n과 window 안의 패킷 n 이후의 패킷들을 모두 재전송
  - 수신자가 예기치 않은 패킷을 받을 경우 그 패킷을 버리고 가장 최근에 받은 패킷의 ACK를 재전송

- FSM

  <img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571751329831.png" alt="1571751329831" style="zoom:67%;" />

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571751371298.png" alt="1571751371298" style="zoom:67%;" />

- base: 윈도우의 첫 패킷, N: 윈도우 크기, nextseqnum: 다음 전송할 패킷
- Sender
  - rdt_send(data)
    - nextseqnum이 윈도우를 안에 있으면 패킷을 보낸다
      - 만약 base가 nextseqnum이면 start.
      - nextseqnum++
    - 윈도우 밖이라면 패킷 보내지 않음
  - timeout
    - 타이머 재시작
    - base부터 nextseqnum-1 까지 다 재전송
  - rdt_rcv(rcvpkt) && not corrupt(누적ACK를 받았을 때)
    - base는 현재 ACK를 받은 패킷 바로 다음 패킷으로 설정된다
    - base가 nextseqnum이면 stop, 아니면 start???
  - rdt_rcv(rcvpkt) && corrupt
- Receiver
  - default
    - 수신받은 패킷을 다시 보냄
  - 패킷을 받고, 손상되지 않았고, seq num이 있는 경우
    - ACK를 보낸다 
- ACK-only: 올바르게 받은 패킷의 가장 높은 seq num을 ACK로 보낸다
  - expectedseqnum만 기억하면 됨
- 고장난 패킷
  - 버퍼에 넣지 않고 버린다

#### 흐름

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571752755051.png" alt="1571752755051" style="zoom:67%;" />

- pk2의 ACK가 없으므로 pk3 이상의 패킷도 전부 버린다
- pk2에서 타임아웃이 발생했으므로 pk2부터 다시 보낸다

### 3.4.4 Selective Repeat(SR)

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571753142119.png" alt="1571753142119" style="zoom:67%;" />

- 오류가 발생된 패킷 이후 또는 오류 발생 패킷만을 재전송
- 수신자는 순서가 다르더라도 window 안에 포함되는 패킷이라면 받을 수 있으며, buffer해놓음 

#### 흐름

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571753202417.png" alt="1571753202417" style="zoom:67%;" />

- pk2 이상의 패킷은 버퍼에 저장했다가
- pk2가 도착하면 그제서야 버퍼를 함께 저장한다.

#### 딜레마

????

## 3.5 Connection-oriented transport: TCP (56~83)

- 특징
  - point-to-point: 송신자 1, 수신자 1
  - 신뢰적, 순서 바이트 스트림
  - 파이프라이닝: TCP 혼잡/흐름 제어에 의한 윈도우 크기 설정
  - Full-duplex data: 같은 연결에서 양방향 통신
    - MSS: maximum segment size
  - 연결지향
    - handshaking은 데이터 교환 전에 송/수신자를 초기화한다
  - 흐름 제어
    - 송신자는 수신자를 overflow시키지 않음

### 3.5.2 Segment structure

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571753646402.png" alt="1571753646402" style="zoom:67%;" />

#### TCP seq num, ACK

<img src="C:\Users\KJH\AppData\Roaming\Typora\typora-user-images\1571754013476.png" alt="1571754013476" style="zoom:67%;" />

답변 패킷의 ACK는 이전 패킷의 seq + 1

### 3.5.3 RTT 예측, Timeout

### 3.5.4 Reliable data transfer

### 3.5.5 Flow control

### 3.5.6 TCP Connection management

- Handshake
  - 연결을 설정하겠다는 동의(서로 연결 설정 의향이 있다는 것을 확인)
  - 연결 파라미터에 대한 동의

#### 2-way handshake

- 변수 딜레이

## 3.6 Principles of congestion control (84~94)

## 3.7 TCP congestion control (95~107)
