# 다중화와 다중 접속

[TOC]

## 다중화와 다중 접속: 하나의 채널을 여러 데이터와 사용자가 공유하는 방법

앞선 글에서는 제한된 대역폭과 전력, 잡음이 존재하는 채널에서 데이터를 얼마나 안정적으로 전달할 수 있는지를 살펴보았다.

그런데 실제 통신 시스템에서는 또 다른 문제가 존재한다.

> **하나의 Channel을 한 종류의 데이터와 한 명의 사용자만 사용한다면 자원이 너무 낭비되지 않는가?**

실제 통신망에서는

- 음성
- 영상
- 센서 데이터
- 인터넷 데이터

가 동시에 이동하고,

수많은 사용자가 하나의 제한된 주파수 자원을 함께 사용한다.

따라서 현대 통신에서 중요한 문제 중 하나는

> **제한된 Channel을 여러 데이터와 여러 사용자가 어떻게 효율적으로 공유할 것인가?**

이다.

전체적인 흐름은 다음과 같이 볼 수 있다.



```text
One Physical Channel
        │
        ├── Multiple Data Streams
        │        ↓
        │   Multiplexing
        │
        └── Multiple Users
                 ↓
          Multiple Access
```



그리고 현대 디지털 네트워크에서는 여기에 **Packet Switching**이 결합되어 필요할 때마다 통신 자원을 동적으로 공유한다.

---

## 1. 왜 Channel을 공유해야 하는가?

주파수와 물리적인 통신망은 무한한 자원이 아니다.

예를 들어 하나의 사용자에게 넓은 주파수 대역을 계속 독점적으로 할당한다면, 사용자가 실제로 데이터를 보내지 않는 순간에도 해당 자원은 다른 사용자가 사용할 수 없다.



```text
User A

████████████████████████

사용하지 않는 순간에도
Channel을 계속 독점
```



사용자가 많아질수록 이러한 방식은 비효율적이다.

따라서 통신 시스템에서는 하나의 물리적 Channel을 가능한 많은 데이터와 사용자가 효율적으로 공유하도록 설계한다.

이를 시스템 설계 관점에서는 **System Utilization**을 높이는 문제로 볼 수 있다.

---



## 2. Multiplexing이란?

**Multiplexing(다중화)**은 여러 개의 독립적인 데이터나 신호를 하나의 공통된 통신 Channel을 이용해 전달할 수 있도록 결합하는 것이다.

개념적으로 다음과 같다.



```text
Data A ──┐
Data B ──┤
Data C ──┼──> Multiplexer ──> One Channel
Data D ──┘
```



수신단에서는 다시 각 데이터를 분리한다.



```text
One Channel
     ↓
Demultiplexer
     │
     ├── Data A
     ├── Data B
     ├── Data C
     └── Data D
```



즉,



```text
여러 데이터
     ↓
Multiplexing
     ↓
하나의 Channel
     ↓
Demultiplexing
     ↓
원래 데이터
```



의 과정이다.

---

## 3. 디지털 환경에서 Multiplexing이 쉬운 이유

음성, 영상, 문자, 센서 데이터는 원래 서로 완전히 다른 형태의 정보이다.

하지만 ADC와 디지털 처리 과정을 거치면 모두



```text
0과 1의 Bit Stream
```



으로 표현할 수 있다.

예를 들어



```text
Voice ────┐
Video ────┤
Text ─────┼──> Binary Data
Sensor ───┘
```



가 된다.

따라서 디지털 시스템 입장에서는 데이터의 원래 종류보다 **Bit Stream이라는 공통된 표현**이 중요해진다.

이 때문에 서로 다른 정보도

- Packet
- Time Slot
- Frame

등의 단위로 분리하여 하나의 Channel 안에 섞어 전송하기 쉬워진다.

---



## 4. Multiplexing의 핵심 아이디어

여러 신호를 구분할 수 있으려면 서로에게 다른 **자원 영역**을 할당해야 한다.

대표적으로 사용할 수 있는 영역은



```text
Frequency
Time
Code
```



등이다.

즉,



```text
하나의 Channel
     │
     ├── Frequency로 분리
     │
     ├── Time으로 분리
     │
     └── Code로 분리
```



와 같이 생각할 수 있다.

이러한 개념이 이후

- FDM
- TDM
- FDMA
- TDMA
- CDMA
- OFDM/OFDMA

등으로 확장된다.

---



## 5. Frequency Division Multiplexing

**FDM(Frequency Division Multiplexing)**은 하나의 넓은 주파수 범위를 여러 개의 작은 주파수 대역으로 나누어 여러 신호를 동시에 전송하는 방식이다.



```text
Frequency →

| Signal A | Signal B | Signal C | Signal D |
```



각 신호가 서로 다른 주파수 위치를 차지하기 때문에 **같은 시간에 동시에 존재할 수 있다.**

예를 들어



```text
s1(t)
+
s2(t)
+
s3(t)
```



가 시간 영역에서는 모두 합쳐져 하나의 복잡한 파형처럼 보일 수 있다.

하지만 각 신호가 서로 다른 주파수 대역에 존재한다면 주파수 영역에서 분리할 수 있다.

---



## 6. 시간 영역에서는 섞여 있는데 어떻게 분리하는가?

서로 다른 주파수를 가진 두 신호를 동시에 전송한다고 하자.

Channel에는

[  
x(t)=s_1(t)+s_2(t)  
]

처럼 두 신호가 합쳐진 상태로 존재한다.

시간 영역만 보면 신호가 뒤섞여 있다.



```text
Time Domain

~~~~~/\/\/\/~~~~~~/\/~~~~
```



하지만 Fourier Transform을 통해 Frequency Domain으로 바꾸면 각 신호가 서로 다른 위치에 나타날 수 있다.



```text
Frequency Domain

     ███             ███
─────███─────────────███────→ f

     Signal 1       Signal 2
```



따라서 특정 주파수 대역만 통과시키는 Filter를 사용하면 원하는 신호를 선택할 수 있다.

---



## 7. 우리가 라디오 방송을 골라 들을 수 있는 이유

여러 방송국은 같은 시간에 동시에 신호를 전송한다.

하지만 방송국마다 서로 다른 주파수를 사용한다.



```text
Frequency →

| Station A | Station B | Station C |
```



라디오에서 주파수를 선택한다는 것은 사실상

> **원하는 주파수 대역의 신호만 Filter를 통해 선택한다**

는 의미이다.

따라서 여러 신호가 공기 중에 동시에 존재하더라도 주파수로 구분할 수 있다.

---



## 8. FDM의 문제: Guard Band

현실의 신호는 주파수 영역에서 하나의 정확한 선만 차지하지 않는다.

각 신호는 일정한 Bandwidth를 가진다.

따라서 서로 너무 가까이 배치하면 Spectrum이 겹칠 수 있다.



```text
Signal A       Signal B

████████       ████████
       ↑
   겹치면 간섭 발생
```



이를 방지하기 위해 신호 사이에 사용하지 않는 주파수 공간을 둘 수 있다.

이를 **Guard Band**라고 한다.



```text
| Signal A | Guard | Signal B |
```



Guard Band는 간섭을 줄여주지만 그만큼 사용하지 못하는 주파수 자원이 생긴다.

따라서



```text
Guard Band ↑
      ↓
Interference ↓

하지만

Spectrum Utilization ↓
```



이라는 trade-off가 존재한다.

---



## 9. Time Division Multiplexing

주파수가 아니라 **시간을 나누어 Channel을 공유**하는 것도 가능하다.

이를 **TDM(Time Division Multiplexing)**이라고 한다.

개념적으로는



```text
Time →

| A | B | C | A | B | C | A | B | C |
```



처럼 하나의 주파수 Channel을 여러 시간 구간으로 나눈다.

각 데이터는 자신의 Time Slot에서만 전송한다.

따라서



```text
같은 Frequency
+
서로 다른 Time
```



을 이용해 여러 데이터가 Channel을 공유할 수 있다.

---



## 10. TDM에서 중요한 것은 Synchronization이다

TDM에서는 각 사용자가 정확한 Time Slot에 데이터를 넣어야 한다.



```text
| A | B | C |
```



그런데 Timing이 어긋나면



```text
| A | B |
       | C |
```



처럼 서로 다른 Slot의 데이터가 겹칠 수 있다.

따라서 시간 기반 공유에서는 **Synchronization**이 매우 중요하다.

즉



```text
Time Resource 공유
       ↓
Precise Timing 필요
```



라는 관계가 존재한다.

---



## 11. Multiplexing과 Multiple Access

비슷한 개념으로 **Multiple Access(다중 접속)**가 있다.

이번 자료에서는 Multiple Access를

> **여러 사용자가 하나의 공통된 통신 Channel 자원을 나누어 사용하는 기술**

로 다룬다.

대표적으로

- FDMA
- TDMA
- CDMA

가 있다.

큰 그림에서 보면



```text
One Shared Channel
       ↓
Multiple Users
       ↓
How should resources be divided?
```



라는 문제이다.

---



## 12. FDMA

**FDMA(Frequency Division Multiple Access)**는 전체 주파수 대역을 여러 사용자에게 나누어 할당한다.



```text
Frequency →

| User A | User B | User C | User D |
```



각 사용자는 자신의 Frequency Band를 사용한다.

따라서 여러 사용자가 동시에 데이터를 보낼 수 있다.

핵심은



```text
Same Time
+
Different Frequency
```



이다.

---



## 13. FDMA의 장점과 한계

FDMA는 구조가 직관적이다.

각 사용자에게 별도의 Frequency Band를 할당하면 되기 때문이다.

하지만 사용자가 데이터를 보내지 않는 순간에도 해당 주파수 자원이 비어 있을 수 있다.

또한 인접 사용자 간의 간섭을 막기 위해 Guard Band가 필요할 수 있다.

따라서



```text
구현 단순성 ↑

하지만

Frequency Resource Utilization ↓ 가능
```



이라는 문제가 생길 수 있다.

---



## 14. TDMA

**TDMA(Time Division Multiple Access)**는 사용자마다 서로 다른 Time Slot을 할당한다.



```text
Time →

| User A | User B | User C | User A | User B |
```



사용자들은 동일한 Frequency를 사용할 수 있지만 동시에 전송하지 않는다.

핵심은



```text
Same Frequency
+
Different Time
```



이다.

---



## 15. TDMA와 Timing 문제

TDMA에서는 송신 Timing이 정확해야 한다.

특히 이동통신에서는 사용자가 기지국과 서로 다른 거리에 위치할 수 있다.



```text
User A ---- Base Station

User B ---------------- Base Station
```



거리가 다르면 전파가 기지국에 도착하는 시간도 달라진다.

이를 제대로 보정하지 않으면 한 사용자의 신호가 다른 사용자의 Time Slot을 침범할 수 있다.

따라서 TDMA에서는 정밀한 Timing Synchronization이 중요한 문제가 된다.

---



## 16. CDMA

**CDMA(Code Division Multiple Access)**는 FDMA나 TDMA와 다른 접근을 사용한다.

모든 사용자가



```text
Same Frequency
Same Time
```



을 사용할 수 있지만, 사용자마다 서로 다른 **Code**를 사용한다.



```text
User A → Code A
User B → Code B
User C → Code C
```



송신 신호들은 Channel에서 모두 겹친다.



```text
A + B + C
      ↓
Shared Channel
```



수신단에서는 원하는 사용자의 Code를 이용하여 해당 신호를 추출한다.

---



## 17. CDMA와 Correlation

CDMA의 핵심은 **Correlation**이다.

수신된 여러 신호에 내가 알고 있는 특정 사용자의 Code를 적용하면 해당 Code와 연관된 신호가 강조된다.

개념적으로는



```text
Mixed Signal
      ↓
Correlation with Code A
      ↓
User A Signal
```



과 같이 볼 수 있다.

즉 서로 다른 사용자를 Frequency나 Time으로 나누는 대신 **Code Domain**에서 구분한다.

---



## 18. CDMA의 Near-Far Problem

CDMA에서는 여러 사용자가 같은 Frequency와 Time에 동시에 신호를 보낸다.

이때 기지국 가까이에 있는 사용자의 신호가 매우 강하고 먼 사용자의 신호가 약하면



```text
Strong User >>> Weak User
```



강한 신호가 약한 신호를 압도할 수 있다.

이를 **Near-Far Problem**이라고 한다.

따라서 CDMA에서는 정밀한 송신 전력 제어가 중요하다.

---



## 19. FDMA, TDMA, CDMA 비교

| 방식 | 나누는 자원 | 핵심 구조 | 주요 문제 |
| --- | --- | --- | --- |
| FDMA | Frequency | 사용자별 다른 주파수 | 주파수 자원 및 Guard Band |
| TDMA | Time | 사용자별 다른 Time Slot | Synchronization |
| CDMA | Code | 동일 시간·주파수에서 다른 Code | Power Control, Near-Far Problem |

결국 세 방식은 모두 같은 문제를 해결한다.

> **하나의 공유 Channel에서 여러 사용자를 어떻게 구분할 것인가?**

다만 사용하는 구분 기준이 다르다.

---



## 20. OFDM이 등장하는 이유

주파수를 나누어 사용하는 방식에서는 각 신호의 Spectrum이 서로 겹치지 않도록 충분한 간격을 두어야 한다.

그러나 Guard Band를 많이 사용하면 Frequency Resource가 낭비된다.

이를 더 효율적으로 사용하는 대표적인 아이디어가 **OFDM(Orthogonal Frequency Division Multiplexing)**이다.

OFDM에서는 하나의 넓은 Channel을 매우 많은 좁은 **Subcarrier**로 나눈다.



```text
Frequency →

|f1|f2|f3|f4|f5|f6|f7|...
```



각 Subcarrier에 데이터를 나누어 실어 동시에 전송한다.

---



## 21. OFDM에서 중요한 Orthogonality

OFDM의 핵심은 단순히 Frequency를 여러 개 사용하는 것이 아니다.

각 Subcarrier 사이에 **Orthogonality**를 갖도록 설계한다.

원자료에서는 한 Subcarrier의 주파수 Spectrum Peak와 인접 Subcarrier의 Null 위치를 맞추는 방식으로 설명한다.

개념적으로는 여러 Spectrum이 서로 겹쳐 보일 수 있지만 수학적으로 구분할 수 있도록 배치한다.



```text
Frequency

   /\    /\    /\
  /  \  /  \  /  \
_/____\/____\/____\_
```



이를 통해 넓은 Guard Band를 매번 둘 필요를 줄이고 주파수 자원을 더 촘촘하게 사용할 수 있다.

---



## 22. OFDM과 Fourier Transform

OFDM에서는 여러 Subcarrier가 동시에 합쳐져 전송된다.

시간 영역에서는 매우 복잡한 신호처럼 보일 수 있다.



```text
Many Subcarriers
       ↓
Combined Time Signal
```



하지만 각 Subcarrier가 Orthogonal하게 설계되어 있기 때문에 수신단에서는 FFT와 같은 Fourier Transform 연산을 통해 각 주파수 성분을 분리할 수 있다.



```text
Received Signal
       ↓
FFT
       ↓
Subcarrier 1
Subcarrier 2
Subcarrier 3
...
```



즉 앞에서 살펴본

> **시간 영역에서 섞여 있더라도 주파수 영역에서 분리할 수 있다**

는 원리가 현대 디지털 통신에서 직접 활용되는 것이다.

---



## 23. OFDM에서도 완벽한 것은 아니다

Subcarrier 사이의 직교성이 유지되지 않으면 서로 간섭할 수 있다.

이를 원자료에서는 **ICI(Inter-Carrier Interference)**와 연결해서 다룬다.



```text
Orthogonality 유지
      ↓
Subcarriers 분리 가능

Orthogonality 손상
      ↓
ICI 발생
```



따라서 실제 시스템에서는 주파수와 Timing의 정확성이 중요하다.

---



## 24. OFDMA

자료에서는 4G와 5G에서 **OFDMA(Orthogonal Frequency Division Multiple Access)**가 중요하게 등장한다고 설명한다.

큰 흐름에서는 OFDM이 여러 Orthogonal Subcarrier를 이용해 Frequency Resource를 세밀하게 나누는 개념이라면, OFDMA에서는 이러한 자원을 **여러 사용자에게 분배하는 문제**로 연결된다.



```text
Many Orthogonal Subcarriers
          ↓
Resource Allocation
          ↓
Multiple Users
```



즉 Frequency와 Time 자원을 더욱 세밀하게 나누어 여러 사용자가 공유할 수 있도록 하는 방향으로 발전한 것이다.

---



## 25. 여기서 또 다른 문제가 생긴다

지금까지 살펴본 방식은 주로



```text
Frequency
Time
Code
```



등의 자원을 어떻게 나눌 것인가에 초점이 맞춰져 있다.

하지만 실제 사용자는 항상 데이터를 보내고 있는 것이 아니다.

예를 들어 스마트폰 사용자는



```text
웹페이지 요청
    ↓
잠시 대기
    ↓
영상 데이터
    ↓
다시 대기
```



처럼 데이터가 불규칙하게 발생할 수 있다.

그렇다면 사용자가 데이터를 보내지 않을 때에도 자원을 계속 고정적으로 할당하는 것은 낭비이다.

여기에서 **Packet 방식**이 중요해진다.

---



## 26. Packet이란?

디지털 데이터는 하나의 거대한 데이터 덩어리로만 전달할 필요가 없다.

작은 단위로 잘라서 보낼 수 있다.

이 작은 데이터 단위를 **Packet**이라고 한다.

개념적으로는



```text
Large Data
    ↓
Split
    ↓

[Packet 1]
[Packet 2]
[Packet 3]
[Packet 4]
```



와 같이 생각할 수 있다.

각 Packet에는 데이터뿐 아니라 주소나 순서와 같은 제어 정보도 포함될 수 있다.

---



## 27. Packet Switching

과거의 **Circuit Switching**에서는 통신을 시작하면 하나의 경로 또는 자원을 일정 시간 동안 사용자에게 전용으로 할당한다.



```text
User A
   │
Dedicated Path
   │
User B
```



이 방식에서는 사용자가 데이터를 보내지 않는 순간에도 할당된 자원이 유지될 수 있다.

반면 **Packet Switching**에서는 실제 데이터가 발생했을 때만 Packet 단위로 통신 자원을 사용한다.



```text
Packet A
Packet B
Packet C

      ↓

Shared Network Resource
```



따라서 여러 사용자가 동일한 Network를 더욱 유연하게 공유할 수 있다.

---



## 28. Statistical Multiplexing

Packet Switching의 중요한 장점 중 하나는 원자료에서 **Statistical Multiplexing**으로 설명되어 있다.

사용자가 항상 같은 양의 데이터를 보내는 것이 아니기 때문에



```text
User A : Data 있음
User B : Data 없음
User C : Data 있음
User D : Data 없음
```



이라면 실제로 데이터가 존재하는 사용자에게 자원을 사용할 수 있다.

즉



```text
필요할 때만 Resource 사용
        ↓
Idle Resource 감소
        ↓
System Utilization ↑
```



의 효과가 생긴다.

---



## 29. Packet 방식에서 서로 다른 데이터도 함께 보낼 수 있다

디지털 시스템에서는

- Voice
- Video
- Text
- Sensor Data

모두 Bit Stream으로 표현할 수 있다.

따라서 이를 Packet이라는 공통 형식으로 감싸 하나의 Network에서 전달할 수 있다.



```text
Voice ──────> Packet ─┐
Video ──────> Packet ─┤
Sensor ─────> Packet ─┼──> Shared Network
Text ───────> Packet ─┘
```



이것이 디지털 데이터 통합의 중요한 장점 중 하나이다.

---



## 30. Packet 방식과 오류 복구

데이터를 하나의 거대한 덩어리로 보냈는데 일부가 깨지면 전체 데이터를 다시 보내야 할 수 있다.

하지만 Packet 단위로 나누어 보내면 문제가 발생한 일부 Packet만 다시 처리할 수 있다.



```text
Packet 1 ✓
Packet 2 ✓
Packet 3 ✕
Packet 4 ✓

      ↓

Packet 3 재전송
```



원자료에서는 이와 관련하여 **ARQ와 Error Correction**을 언급하고 있다.

즉 Packet화는 효율적인 자원 공유뿐 아니라 오류 처리에도 유리하다.

---



## 31. Packet 방식의 문제: Latency와 Buffer

Packet 방식도 장점만 있는 것은 아니다.

여러 사용자의 Packet이 하나의 Network에 몰리면 데이터가 즉시 전송되지 못하고 잠시 기다릴 수 있다.

이를 처리하기 위해 **Buffer**가 필요하다.



```text
Packets
   ↓
[ Buffer ]
   ↓
Channel
```



Traffic이 많아지면 Buffer에 데이터가 쌓일 수 있고 전송 지연이 증가할 수 있다.



```text
Traffic ↑
   ↓
Queue ↑
   ↓
Latency ↑
```



---



## 32. Jitter

모든 Packet이 동일한 시간에 도착하는 것은 아니다.

Packet마다 전송 지연이 달라질 수 있다.

이를 **Jitter**라고 한다.

예를 들어



```text
Packet 1 → 10 ms
Packet 2 → 15 ms
Packet 3 → 9 ms
Packet 4 → 20 ms
```



처럼 도착 시간이 일정하지 않을 수 있다.

특히 실시간 데이터에서는 이러한 시간 변화가 중요한 문제가 된다.

---



## 33. Packet Loss와 순서 문제

Packet은 전송 과정에서 유실될 수도 있다.

또한 원자료에서는 Network 상황에 따라 Packet의 도착 순서가 뒤바뀔 수 있다는 문제도 다룬다.



```text
Send

1 → 2 → 3 → 4

Receive

1 → 3 → 2 → 4
```



따라서 Receiver에서는

- Buffering
- Sequence 관리
- Synchronization
- Error Recovery

등이 필요해질 수 있다.

즉 Channel을 효율적으로 공유할수록 시스템 제어는 더 복잡해질 수 있다.

---



## 34. 초저지연 Packet

일부 시스템에서는 단순히 많은 데이터를 보내는 것보다 **얼마나 빨리 도착하는가**가 더 중요하다.

원자료에서는

- 의료용 로봇
- 자율주행
- Implantable Bio Sensor

같은 시스템을 예로 들며 **Short Packet과 초저지연 통신**의 중요성을 언급한다.

즉 모든 Packet을 크게 만들어 전송 효율만 높이는 것이 항상 최적은 아니다.



```text
Large Packet
→ Overhead 비율 ↓ 가능
→ 하지만 Delay 증가 가능

Short Packet
→ 빠른 처리 가능
→ 하지만 Efficiency 문제 가능
```



결국 응용 목적에 따라 Packet 구조 역시 달라져야 한다.

---



## 35. 저전력 시스템에서의 Packet Communication

웨어러블이나 체내 소자는 배터리가 매우 제한되어 있다.

이러한 시스템에서 항상 무선 송신기를 켜 두고 데이터를 계속 보내면 많은 에너지가 필요하다.

따라서 원자료에서는 다음과 같은 아이디어를 제시한다.



```text
Normal State
     ↓
Sleep / Waiting

Event 발생
     ↓
Short Packet Transmission

Transmission 종료
     ↓
Sleep
```



즉 필요한 순간에만 짧게 통신하여 전력을 줄이는 것이다.

---



## 36. Multiplexing에서 결국 발생하는 Trade-off

하나의 Channel을 더 많은 데이터와 사용자가 함께 쓰면 자원 활용도는 증가한다.



```text
More Users
    ↓
System Utilization ↑
```



하지만 동시에



```text
More Users
    ↓
Scheduling Complexity ↑
Interference ↑ 가능성
Synchronization Difficulty ↑
Buffering ↑
Latency ↑
```



와 같은 문제가 생길 수 있다.

따라서 다중화와 다중 접속도 결국 통신 시스템의 전형적인 Trade-off 문제이다.

---



## 37. 지금까지의 개념을 한 번에 연결하기

지금까지 배운 구조를 큰 그림으로 보면 다음과 같다.



```text
Multiple Data Sources
        │
        ▼
   Multiplexing
        │
        ▼
Multiple Users
        │
        ▼
 Multiple Access
        │
        ├── Frequency
        │      ├── FDM
        │      ├── FDMA
        │      └── OFDM / OFDMA
        │
        ├── Time
        │      ├── TDM
        │      └── TDMA
        │
        └── Code
               └── CDMA
        │
        ▼
 Shared Physical Channel
        │
        ▼
 Packet-Based Resource Sharing
```



결국 모든 기술의 공통된 목적은 같다.

> **한정된 통신 자원을 최대한 많은 데이터와 사용자가 효율적으로 공유하도록 만드는 것**

이다.

---



## 38. 앞선 개념들과 연결하기

지금까지의 시리즈를 다시 보면 모든 것이 연결된다.

### Modulation



```text
하나의 Symbol을
어떤 Physical Waveform으로 보낼 것인가?
```



### Channel / BER



```text
그 Symbol이 Noise 속에서도
얼마나 정확하게 살아남는가?
```



### Multiplexing / Multiple Access



```text
그 Channel을
몇 개의 데이터와 사용자가 함께 쓸 것인가?
```



즉 각각 따로 보이던 개념들이 사실 하나의 통신 시스템을 서로 다른 관점에서 바라본 것이다.

---



## 39. Living Hybrid Chip 관점에서 바라보기

Living Hybrid Chip이나 Implantable Bioelectronic Device를 생각해보자.

하나의 Chip에서 여러 종류의 데이터가 발생할 수 있다.



```text
Neural Signal ────┐
Sensor Data ──────┤
Device Status ────┼──> Communication Link
Control Data ─────┘
```



이 데이터를 각각 별도의 통신 회로로 전달한다면 시스템이 매우 복잡해질 수 있다.

따라서 여러 데이터 Stream을 하나의 Digital Link로 묶어 전달하는 Multiplexing을 생각할 수 있다.



```text
Multiple Bio Signals
        ↓
Digital Multiplexing
        ↓
Single Communication Link
```



---



## 40. Implant에서 계속 데이터를 보내야 하는가?

초저전력 체내 시스템에서는 이 질문도 중요하다.



```text
계속 Streaming
       ↓
Radio Active Time ↑
       ↓
Power Consumption ↑
```



대신 특정 Event가 발생했을 때만 데이터를 보내는 구조라면



```text
Event Detection
      ↓
Packet 생성
      ↓
Short Transmission
      ↓
Sleep
```



과 같은 구조를 생각할 수 있다.

따라서 Living Hybrid Chip의 통신 문제에서도

> **어떤 데이터를 얼마나 자주 외부로 전송해야 하는가?**

가 중요한 시스템 설계 질문이 된다.

---



## 41. 데이터 자체를 줄이는 것도 중요하다

앞선 글에서는 Source Coding을 통해 필요한 Bit 수 자체를 줄일 수 있다고 살펴보았다.

이는 초저전력 시스템에서 특히 중요할 수 있다.



```text
Raw Biological Data
        ↓
Compression / Processing
        ↓
Reduced Data
        ↓
Packet Transmission
```



전송해야 하는 데이터가 줄어들면 통신 회로가 활성화되어 있어야 하는 시간도 줄일 가능성이 있다.

즉



```text
Data Amount ↓
      ↓
Required Transmission ↓
```



이라는 시스템적 연결을 생각할 수 있다.

---



## 42. Channel 공유에서 가장 중요한 질문

다중화와 다중 접속을 공부할 때 단순히



```text
FDMA
TDMA
CDMA
OFDM
```



을 암기하는 데 그치면 전체 구조가 잘 보이지 않는다.

오히려 다음 질문을 먼저 해야 한다.

> **사용자나 데이터를 서로 무엇으로 구분하고 있는가?**

그 기준이



```text
Frequency인가?
Time인가?
Code인가?
Packet 단위의 동적 자원인가?
```



를 보면 각 기술의 차이가 훨씬 명확해진다.

---



## 43. 생각해볼 질문

이번 내용을 이해했는지 확인하려면 다음 질문에 답해보는 것이 좋다.

1. **Multiplexing이 필요한 이유는 무엇인가?**
2. **디지털 환경에서 서로 다른 종류의 정보를 하나의 Channel로 묶기 쉬운 이유는 무엇인가?**
3. **FDM에서는 여러 신호를 무엇으로 구분하는가?**
4. **여러 주파수 신호가 같은 시간에 겹쳐 있어도 분리할 수 있는 이유는 무엇인가?**
5. **Guard Band는 왜 필요하며 어떤 자원을 희생하는가?**
6. **TDM에서는 Channel을 어떤 기준으로 나누는가?**
7. **TDM과 TDMA에서 Synchronization이 중요한 이유는 무엇인가?**
8. **FDMA, TDMA, CDMA는 각각 사용자를 무엇으로 구분하는가?**
9. **CDMA에서 Correlation이 필요한 이유는 무엇인가?**
10. **Near-Far Problem은 왜 발생하는가?**
11. **OFDM에서 Orthogonality가 중요한 이유는 무엇인가?**
12. **OFDM이 단순 FDM보다 주파수 자원을 촘촘하게 사용할 수 있는 이유는 무엇인가?**
13. **OFDMA에서는 OFDM의 Subcarrier 자원이 어떻게 Multiple Access 문제와 연결되는가?**
14. **Circuit Switching과 Packet Switching의 자원 사용 방식은 어떻게 다른가?**
15. **Statistical Multiplexing이 System Utilization을 높이는 이유는 무엇인가?**
16. **Packet을 사용하면 왜 일부 오류만 다시 처리하기 쉬워지는가?**
17. **Packet 통신에서 Buffering, Latency, Jitter 문제가 발생하는 이유는 무엇인가?**
18. **초저전력 Implant에서 Continuous Streaming 대신 Event-Based Packet Transmission을 고려할 수 있는 이유는 무엇인가?**
19. **Living Hybrid Chip에서 여러 Biological Data Stream을 하나의 통신 Link로 묶는다면 어떤 장점과 문제가 생길 수 있는가?**

---



## 핵심 정리

하나의 물리적 Channel을 효율적으로 사용하기 위해서는 여러 자원을 나누어 사용할 수 있다.



```text
Shared Channel
      │
      ├── Frequency
      │     ├── FDM
      │     └── FDMA
      │
      ├── Time
      │     ├── TDM
      │     └── TDMA
      │
      ├── Code
      │     └── CDMA
      │
      └── Orthogonal Subcarriers
            └── OFDM / OFDMA
```



그리고 디지털 Network에서는 데이터를 Packet 단위로 나누어 필요할 때 통신 자원을 사용하는 방식이 중요하다.



```text
Data
 ↓
Packetization
 ↓
Shared Resource
 ↓
Statistical Multiplexing
 ↓
Efficient Network Utilization
```



하지만 자원 활용도를 높일수록



```text
Synchronization
Interference
Scheduling
Buffering
Latency
Jitter
Complexity
```



와 같은 새로운 문제가 생긴다.

따라서 다중화와 다중 접속의 핵심도 결국 이전 글들과 동일하다.

> **제한된 자원을 더 효율적으로 사용하는 대신 발생하는 복잡성과 간섭을 어떻게 관리할 것인가?**

다음 글에서는 Channel을 단순히 나누는 것을 넘어 **여러 안테나와 적응형 제어를 이용해 Channel 자체를 더 적극적으로 활용하는 현대 무선 통신 기술**인 MIMO, Diversity, Spatial Multiplexing, Beamforming, Adaptive Modulation and Coding, Multiradio Coexistence 등을 다룬다.

---

블록도 기준: [05. 다중화](../../blocks/multiplexing.md) · [전체 로드맵](../../roadmap.md)
