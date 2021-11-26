# 전송 계층

전송 계층에서는 Process to Process간의 logical communication을 담당하는 계층으로 응용 계층에서 보낸 데이터를 Segment(이하 세그먼트)로 포장하여 네트워크 계층으로 전달 또는 네트워크 계층에서 받은 Datagram(이하 데이터그램)를 Segment로 잘라 응용 계층으로 보내는 역할을 하게 된다.

전송 계층에서 지원하는 프로토콜은 2가지로 각각 다음과 같은 특징을 가지고 있다.

- TCP
  - Reliable, In-order
  - 혼잡 제어(congestion control) - 송신 버퍼를 제어
  - 흐름 제어(flow control) - 수신 버퍼를 제어
  - 연결 수립(connection setup)
- UDP
  - Unreliable, Unordered
  - 어떠한 기능은 없지만, 신속하게 전송하기 위한 프로토콜

하지만 전송 계층은 대역폭 보장(bandwidth guarantees), 또는 지연 보장(delay guarantees)을 제공해주진 못한다.

## Multiplexing / Demultiplexing

전송 계층은 다른 호스트PC에 있는 프로세스와 통신하기 위해 가장 기본적인 기능으로 **Multiplexing과 Demultiplexing**을 제공한다. 응용 계층에선 다양한 응용 프로그램들이 UDP와 TCP를 통해 데이터를 전달한다. 즉, 여러 데이터들이 전송 계층으로 *모여서 전달되기 때문에 각 데이터가 목적지와 어떤 프로세스에 전달되기를 원하는지 구별하는 작업을 `Multiplexing`*이라고 하며, 반대로 *전송받은 세그먼트들을 해당하는 프로세스로 전달하기 위한 작업을 `Demultiplexing`*이라고 한다.

### Connectionless demux

![TCP/UDP segment format](https://i.imgur.com/ewp0kmg.png)

따라서 세그먼트에는 Demultiplexing을 위한 출발지 포트와 목적지 포트가 **반드시** 포함되게 된다. 전송 계층은 목적지 주소를 확인하여 제대로 왔는지 확인하고 목적지 포트를 통해 목적지 프로세스에게 전달하게 된다.

#### UDP (User Datagram Protocol)

비연결 지향 프로토콜인 UDP는 연결 수립을 위한 Handshaking(이하 핸드쉐이킹) 작업을 하지 않으며, 각 데이터는 어떤 사용자가 보냈는지 식별하지 않는다. 따라서 전송 프로토콜이 어떠한 작업을 할 필요가 없고 순서도 보장되지 않는다 UDP는 순수히 "전송"에 목적이 있다. 이 말은 네트워크 상황에 따라서 데이터가 유실되어도 서버와 애플리케이션은 알 수 없다는 걸 의미한다.

이런 UDP는 데이터 유실보다 전송 자체가 중요한 서비스에 적합한데, 동영상 스트리밍, DNS, SNMP와 같은 서비스들은 시간안에 데이터가 도착하기를 원하며 중간에 조금의 손실은 서비스 제공에 영향이 없다. 따라서 이런 서비스들은 TCP의 안전한 데이터 전송에 필요한 혼잡 제어와 같이 전송량을 조절하는 기능은 서비스 제공에 안좋은 영향을 미치게 된다. 또는 DNS의 경우 서로간의 통신은 1번만 일어나는데 이 1번을 위해서 비싼 비용을 지불하지 않기 원하는 서비스에 사용될 수 있다.

앞서 설명과 다르게 Reliable한 전송에도 UDP가 사용되는 경우가 있는데, 이때는 응용 프로그램이 해당 데이터가 제대로 왔는지 검사하는 과정이 필요하다.

![UDP: segment header](https://i.imgur.com/Je2WhRY.png)

UDP 세그먼트를 보면 UDP의 특성에 맞게 매우 간단하고, 헤더의 크기가 작아 전송에 필요한 Overhead가 적다. 하지만 포함된 정보가 적은 만큼은 혼잡 제어와 같은 기능은 제한된다.

**UDP checksum**

전송될 데이터가 유실된 경우는 없다고 생각하면 되지만 전달받은 데이터가 잘못된 경우는 조금 다르다. 따라서 UDP에도 최소한의 안정장치로 checksum을 가지는데 checksum은 전송할 데이터를 16-bit integer로 잘라 모두 더한 값에 보수를 취한 값을 저장한다. 상대측은 수신 받은 데이터에 같은 연산을 하여 받은 checksum과 비교해 데이터의 무결성을 검사한다. 단, 이것은 완벽한 장치가 아니며 이를 처리할 의무는 없다. 단지 오류가 있냐, 없냐만 알려준다.

### Connection-oriented demux

Connection-oriented(이하 연결 지향)에서 demuliplexing에는 `출발지 IP, 출발지 포트, 목적지 IP, 목적지 포트`에 대한 정보가 필요하다. 목적지 IP, 포트의 경우 전달 받은 세그먼트가 제대로 도착했는지(목적지 IP 이용)와 어떤 프로세스에 전달되어야 하는가(목적지 포트)를 위해 이용된다. 연결 지향 프로토콜에선 연결을 유지하기 위해서 하나의 서버는 상대방을 위한 전용 소켓을 여러 개 유지할 필요가 있다. 따라서 출발지 IP와 포트는 여러 연결된 소켓 중에서 어떤 소켓으로 들어온 데이터인지 식별할 수 있어야 한다.

## Principles of reliable data transfer

연결 지향 프로토콜 TCP를 보기 전에 어떻게 네트워크는 데이터의 전송 되었는지 알기 위해 몇가지 장치을 가지고 있다.

- Checksum
  - Checksum은 앞서 살펴본 내용와 같이 전송할 데이터를 이용해 생성된 값이다. 따라서 전송 받은 데이터를 동일한 연산을 통하면 같이 Checksum값이 나올거라는 아이디어이다. (하지만 완전하진 않다.)
- Acknowledgement
  - 송신측에서 보낸 데이터가 제대로 도착했는지 알기 위해선 전송받은 호스트에서 제대로 받았다는 신호를 송신측에 보낼 필요가 있다. Ack 신호는 개별 데이터에 대한 신호일 수 있고, 지금까지 받은 데이터 모두 잘 받았다는 의미가 있을 수 있다.
- Negative acknowledgement
  - 반대로 제대로 받지 못했을 경우 보낼 신호이다.
- Timer
  - 앞서 Checksum, Acknowledgement, Negative acknowledgment 등은 데이터가 수신됐을 경우만 판별할 수 있다. 하지만 네트워크 상황에 따라서 데이터가 유실된 경우를 판별하기 위해서 타이머는 일정한 시간 동안 어떤 신호도 받지 못한 경우 데이터가 유실됐다고 판단하기 위한 장치이다. 하지만 모든 데이터를 제대로 받았지만 Ack 신호가 유실된 경우 같은 데이터를 또 보내는 상황이 생길 수 있는데, 이 경우는 받았던 데이터는 버리게 된다.
- Window/Pipelining
  - 데이터 전송에 있어 송신자와 수신자가 서로 번갈아 가면서 데이터를 주고 받으면 비효율적인 비용이 발생하게 된다. 파이프라이닝은 송신자가 수신자에게 보낼 데이터를 쌓아두는 버퍼이다. 수신자는 버퍼에 쌓인 데이터를 계속 소비하며 데이터를 주고받게 된다. 하지만 무작정 데이터를 쌓아둘 순 없기 때문에 Ack을 받지 않는 동안에 Window 크기만큼 데이터를 쌓아두게 된다.
- Sequence number
  - 앞서 Timer를 설명하면서 받았던 데이터를 또 받은 경우를 생각하면 새로 받은 데이터가 중복된 데이터인지 구별할 수 있어야 한다. 이때 Sequence number가 이용된다. 

## TCP

연결 지향 프로토콜인 TCP는 다음과 같은 특징이 있다.

- Point-to-point
  - TCP는 송신측, 수신측간 1:1 통신을 위한 프로토콜이다.
- Reliable, in-order byte stream
  - TCP는 보낼 데이터를 버퍼에 연속된 데이터로 담아두게 되는데, 이때 전송은 길게 이어진 데이터를 MSS(물리적으로 보낼 수 있는 세그먼트의 최대 사이즈)만큼 잘라서 보내게 된다. 이때 보내는 속도는 Congenstion controlled과 Flow controlled에 관련있다.
- Pipelined
  - 클라이언트-서버간의 사이에 하나의 패킷만 존재할 수 있다면 통신에 낭비가 발생하기 때문에 Congestion controlled과 Flow controlled을 고려해 Window 크기를 정하고 그 크기만큼은 Ack을 받지 않아도 패킷을 보낼 수 있도록 하여 통신의 효율을 높일 수 있다.
- Full duplex data
  - TCP는 서로간 연결이 수립된 경우 양방향으로 통신이 가능한 프로토콜이다.
- Connection-oriented
  - TCP는 서로 데이터를 교환하기 전에 Handshaking 작업을 통해 서로가 통신이 가능한지 확인하는 작업을 수행한다.
- Flow controlled
  - 수신측의 버퍼가 더이상 데이터를 받지 못하는 경우를 막기 위해 조절하여 보낸다.
- Congestion controlled
  - 네트워크 상태가 데이터 송신에 문제가 없도록 조절하여 보낸다.

### TCP segment structure

![TCP segment structure](https://i.imgur.com/hYuLKpg.png)

TCP 세그먼트는 UDP와 달리 헤더 길이도 가변적이기 때문에 헤더의 길이를 표시하기 위한 필드(4번째 줄 첫번째 필드)가 존재한다.

- Sequence number
  - 이번 세그먼트의 Application data가 몇번째 바이트인지를 알려주는 필드 (TCP는 연결 수립에서 데이터의 시작 주소를 임의로 정한다.)
- Acknowledment number
  - TCP의 Full duplex 특성으로 상대방이 나한테 보내는 Acknowledment number를 나타낸다. 예를 들어 N을 보내면 N-1번까진 잘 받았고, 다음으로 N번째를 기대한다.고 의미한다.
- Receive window
  - Flow controll에 관련된 필드로 내가 수신받을 수 있는 버퍼의 크기를 나타낸다. 상대측은 이 필드에 적혀있는 값 만큼은 Ack을 보내지 않아도 보낼 수 있다.
- Checksum
  - 세그먼트의 오류를 검사하기 위한 필드
- Flags
  - 연결 시 사용되는 플래그
    - R(RST)
      - 연결 초기화
    - S(SYN)
      - 연결 수립
    - F(FIN)
      - 연결 종료
  - A(ACK)
    - 이 플래그가 Set된 패킷을 받는 수신측은 Acknowledgement 필드에 수신측에 대한 Ack 정보가 담겨있다고 알려준다.
  - U/P 안쓰임!

### TCP round trip time, timeout

TCP가 신뢰성 있는 통신을 위한 요소 중에 timer와 관련된 것으로, 세그먼트 또는 Ack의 유실을 확인하기 위해서 RTT를 정해 그 안에 기대하는 결과가 오지 않으면 잘못됐다고 판단한다. 하지만 RTT를 정하는건 상황에 따라 달라지기 때문에, 너무 짧으면 Ack이 오기 전에 실패됐다고 판단해버리고, 너무 길면 실제로 유실됐다고 해도 대처가 늦어진다. 따라서 과거 RTT에 대한 샘플을 구해서 앞으로 RTT가 어쩔지 예측하게 된다.

하지만 하나의 샘플RTT로 앞으로 예측을 구하면 적절한 RTT를 구하지 못하기 때문에 이전까지의 RTT에 대한 평균을 구하는데, 만약 재전송된 세그먼트에 대해선 무시하는데 이것은 TCP 헤더에 재전송된 원본 데이터가 언제 보냈던건지 알 수 있는 방법이 없기 때문에 무시한다.

![RTT](https://i.imgur.com/v9sQjUh.png)

EstimatedRTT를 보면 변화량이 SampleRTT보다 자연스럽게 만들어지는게 볼 수 있지만 현실과의 차이가 존재하기 때문에 Safety margin을 추가하여 구하게 된다.

![TimeoutInterval](https://i.imgur.com/LsT4Sz8.png)

EstimatedRTT 변화량에 따라서 Safety margin을 적절하게 줘서 문제를 해결한다.

