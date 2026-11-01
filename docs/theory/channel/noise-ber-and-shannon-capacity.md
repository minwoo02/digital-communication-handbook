# 채널·잡음·BER와 Shannon Capacity

[TOC]

앞선 글에서는 디지털 비트를 Symbol로 묶고 ASK, FSK, PSK, QAM 등의 변조 방식을 이용하여 실제 전송 파형으로 만드는 과정을 살펴보았다.

하지만 송신기에서 아무리 완벽한 파형을 만들어도 수신기에 그대로 도착하는 것은 아니다.

신호는 반드시 **물리적 채널(Channel)**을 지나야 하고, 그 과정에서 감쇠, 잡음, 간섭, 페이딩 등의 영향을 받는다.

따라서 디지털 통신의 핵심 문제는 결국 다음과 같이 정리할 수 있다.

> **불완전한 채널을 통과해 변형된 신호로부터 송신기가 보낸 Symbol과 Bit를 얼마나 정확하게 복원할 수 있는가?**

전체 흐름은 다음과 같다.



```text
Transmitted Symbol
        ↓
Physical Channel
        ↓
Attenuation
Noise
Interference
Fading
Distortion
        ↓
Received Signal
        ↓
Detection
        ↓
Estimated Symbol
        ↓
Recovered Bits
```



이번 글에서는 이 과정을 이해하기 위해

- Channel
- Noise
- Fading
- SNR
- Eb/N0
- BER
- Bandwidth
- Shannon Capacity
- Channel Coding

을 하나의 흐름으로 연결해서 살펴본다.

---

## 1. Channel이란?

Channel은 송신기에서 만들어진 신호가 수신기까지 이동하는 **물리적 경로**이다.

대표적인 Channel에는 다음이 있다.



```text
Copper Wire
Optical Fiber
Free Space
Biological Tissue
```



즉 Channel은 단순히 공기만을 의미하지 않는다.

신호가 이동하는 모든 물리적 매질을 통신 관점에서는 Channel이라고 볼 수 있다.

---



## 2. 유선 Channel과 무선 Channel

Channel은 크게 **유선(Wire / Guided)**과 **무선(Wireless / Unguided)**으로 구분할 수 있다.

| 구분 | 유선 Channel | 무선 Channel |
| --- | --- | --- |
| 전송 매질 | 구리선, 광섬유 등 | 공기, 자유 공간, 생체 조직 등 |
| 전달 경로 | 비교적 고정됨 | 주변 공간으로 전파됨 |
| 환경 예측성 | 상대적으로 높음 | 상대적으로 낮음 |
| 주요 문제 | 감쇠, 분산, 선로 특성 | Path Loss, Fading, Multipath |
| 외부 간섭 | 차폐 등을 통해 관리 가능 | 주변 환경과 다른 송신기의 영향 큼 |

유선 채널은 신호가 정해진 물리적 경로를 따라 이동한다.

반면 무선 채널에서는 전자기파가 공간으로 퍼져나가기 때문에 훨씬 다양한 환경의 영향을 받는다.

---



## 3. 무선 Channel이 어려운 이유

무선 채널에서는 송신한 신호가 하나의 깨끗한 경로만을 따라 수신기에 도착하지 않는다.

예를 들어



```text
Transmitter
     │
     ├───────────────→ Receiver
     │
     ├──→ Wall ─────→ Receiver
     │
     └──→ Object ───→ Receiver
```



처럼 여러 경로로 전달될 수 있다.

각 경로는 서로 다른

- 거리
- 지연
- 크기
- 위상

을 가진다.

따라서 수신기에서는 여러 개의 신호가 합쳐진 형태를 받게 된다.

---

## 4. Path Loss

무선 신호는 공간으로 퍼져나가면서 에너지가 분산된다.

따라서 송신기에서 멀어질수록 수신되는 신호의 크기가 작아진다.

이를 **Path Loss**라고 한다.

개념적으로는



```text
Distance ↑
    ↓
Received Power ↓
```



라고 볼 수 있다.

따라서 장거리 통신에서는 송신 전력뿐 아니라

- 안테나 이득
- 수신기 감도
- 채널 환경

등을 함께 고려해야 한다.

---



## 5. Multipath Fading

송신된 신호가 건물, 벽, 물체 또는 생체 조직 등에 반사되면 여러 경로를 통해 수신기에 도착할 수 있다.

이를 **Multipath**라고 한다.



```text
            ┌──────────────→
Transmitter ├── Reflection ─→ Receiver
            └── Reflection ─→
```



문제는 각 신호의 위상이 다를 수 있다는 것이다.

여러 신호가 같은 위상으로 만나면 서로 강해질 수 있지만,



```text
Signal A
   +
Signal B
   ↓
Stronger Signal
```



반대 위상으로 만나면 서로 상쇄될 수도 있다.



```text
Signal A
   +
Opposite Signal B
   ↓
Weak Signal
```



이로 인해 수신 신호의 세기가 시간과 위치에 따라 크게 변할 수 있다.

이를 **Fading**이라고 한다.

---



## 6. Noise와 Interference

Channel에서 신호를 방해하는 요소를 모두 같은 것으로 생각하면 안 된다.

### Noise

Noise는 통신 시스템 내부와 외부에서 발생하는 무작위적인 잡음 성분이다.

디지털 통신 이론에서는 대표적으로 **AWGN(Additive White Gaussian Noise)** 모델이 등장한다.

개념적으로는



```text
Received Signal
=
Transmitted Signal
+
Noise
```



와 같이 생각할 수 있다.

---



### Interference

Interference는 다른 통신 신호나 시스템이 내 신호를 방해하는 경우이다.



```text
Desired Signal
       +
Other Transmitter
       ↓
Interference
```



따라서



```text
Noise
≠
Interference
```



이다.

Noise와 Interference는 모두 수신 성능을 떨어뜨리지만 원인과 대응 방식은 서로 다를 수 있다.

---



## 7. 수신기는 결국 Decision을 한다

앞선 글에서 Constellation Diagram을 살펴보았다.

송신기는 미리 정해진 Symbol 중 하나를 선택하여 보낸다.

하지만 채널을 통과하면 Noise 등의 영향으로 실제 수신 위치가 변한다.



```text
Transmitted

●

     ↓ Noise

Received

   ×
```



수신기의 역할은 이 `×`를 보고

> **원래 어떤 Symbol이 전송되었는가?**

를 결정하는 것이다.

따라서 디지털 통신에서 Receiver는 본질적으로 **Decision System**이라고 볼 수 있다.

---



## 8. Decision Error는 어떻게 발생하는가?

예를 들어 두 개의 Symbol이 있다고 생각해보자.



```text
Symbol A          Symbol B

   ●----------------●
```



수신기는 중간에 Decision Boundary를 둘 수 있다.



```text
Symbol A        |        Symbol B
   ●            |            ●
                |
         Decision Boundary
```



송신기가 A를 보냈더라도 Noise가 충분히 크게 발생하면



```text
A 전송
  ●

       Noise
         →

                 ×
```



수신 신호가 B의 영역으로 넘어갈 수 있다.

그러면 수신기는 B가 전송되었다고 잘못 판단한다.

이것이 디지털 통신에서의 **Error**이다.

---



## 9. BER

통신 시스템의 신뢰성을 나타내는 대표적인 지표가 **BER(Bit Error Rate)**이다.

개념적으로는

[  
BER =  
\frac{\text{잘못 수신된 Bit 수}}  
{\text{전체 수신 Bit 수}}  
]

로 이해할 수 있다.

예를 들어



```text
Transmitted
10110101

Received
10100101
```



이라면 일부 Bit가 잘못 복원된 것이다.

BER가 낮을수록 통신 시스템의 신뢰성이 높다.



```text
BER ↓
  ↓
Reliability ↑
```



---



## 10. SER과 BER

Symbol 하나가 여러 Bit를 담을 수 있기 때문에 **Symbol Error**와 **Bit Error**는 반드시 같은 것은 아니다.

예를 들어 QPSK에서는 한 Symbol에 2 Bit가 들어간다.



```text
00
01
11
10
```



수신기가 하나의 Symbol을 잘못 판정했다고 해서 반드시 두 Bit가 모두 틀리는 것은 아니다.

이 때문에 실제 변조 시스템에서는 Symbol 배치와 Bit Mapping 방식도 중요하다.

앞선 글에서 살펴본 **Gray Coding**이 이 문제와 연결된다.

---



## 11. SNR

Receiver가 Symbol을 정확하게 판단하려면 원하는 신호가 Noise보다 충분히 강해야 한다.

이를 표현하는 대표적인 지표가 **SNR(Signal-to-Noise Ratio)**이다.



```text
SNR
=
Signal Power
────────────
Noise Power
```



개념적으로



```text
Signal ↑
Noise ↓
   ↓
SNR ↑
   ↓
Detection 쉬움
```



이다.

반대로



```text
Signal ↓
Noise ↑
   ↓
SNR ↓
   ↓
Detection 어려움
```



이 된다.

---



## 12. SNR과 BER의 관계

SNR이 높으면 Constellation Point 주변의 Noise 영향이 상대적으로 작아진다.



```text
High SNR

     ×
    ●
```



수신된 신호가 원래 Symbol 주변에 집중되기 때문에 판정이 쉽다.

반대로 SNR이 낮으면



```text
Low SNR

 ×   ×
   ●
×      ×
```



수신 위치가 넓게 흩어질 수 있다.

따라서 일반적인 관계는



```text
SNR ↑
 ↓
Decision Error ↓
 ↓
BER ↓
```



로 이해할 수 있다.

---



## 13. 그런데 SNR만으로는 부족하다

디지털 통신에서는 SNR과 함께 **Eb/N0**라는 값이 자주 등장한다.

여기서

- (E_b): 한 Bit를 전달하는 데 사용되는 Energy
- (N_0): Noise Power Spectral Density

를 의미한다.

SNR이 전체 신호와 Noise의 전력 관계를 본다면, Eb/N0는 이를 **Bit 단위로 정규화해서 보는 관점**이라고 이해할 수 있다.

즉,

> **한 Bit를 얼마나 충분한 에너지로 Noise 속에서 전달하고 있는가?**

를 생각하는 지표이다.

---



## 14. 왜 Eb/N0가 중요한가?

통신 시스템마다 Bit Rate와 Bandwidth가 다를 수 있다.

따라서 단순 SNR만 비교하면 서로 다른 시스템의 성능을 직접 비교하기 어려울 수 있다.

Eb/N0를 사용하면 Bit 하나를 기준으로 통신 시스템의 신뢰성과 에너지 효율을 비교할 수 있다.

특히



```text
BER vs Eb/N0
```



그래프는 디지털 통신에서 매우 중요한 성능 평가 방법이 된다.

---



## 15. BER vs Eb/N0 그래프

개념적으로 BER과 Eb/N0의 관계는 다음과 같이 생각할 수 있다.



```text
BER
 ↑
 │\
 │ \
 │  \
 │   \
 │    \____
 │
 └────────────→ Eb/N0
```



Eb/N0가 증가할수록 일반적으로 BER은 감소한다.

즉



```text
Bit Energy ↑
      ↓
Noise 대비 판정 여유 ↑
      ↓
BER ↓
```



이다.

하지만 실제 시스템에서 송신 전력을 무한정 증가시킬 수는 없다.

특히

- 배터리 기반 IoT
- Wearable Device
- Implantable Device
- Living Hybrid Chip

과 같은 시스템에서는 전력이 매우 중요한 제약이 된다.

---



## 16. Bandwidth

통신에서 또 하나의 핵심 자원이 **Bandwidth**이다.

Bandwidth는 두 가지 관점으로 볼 수 있다.

### Signal Bandwidth

실제 신호가 주파수 영역에서 차지하는 폭이다.



```text
Frequency

      █████████
──────|───────|────────→
     f1      f2

Bandwidth = f2 - f1
```



---



### Channel Bandwidth

물리적 채널이나 시스템이 사용할 수 있도록 허용된 주파수 범위이다.

즉



```text
Signal Bandwidth
→ 신호가 필요한 폭

Channel Bandwidth
→ 채널이 제공할 수 있는 폭
```



으로 구분할 수 있다.

---



## 17. Bandwidth는 왜 한정된 자원인가?

무선 통신에서는 여러 시스템이 같은 전자기파 공간을 공유한다.

따라서 모든 사용자가 원하는 주파수를 무제한으로 사용할 수 없다.



```text
Frequency Resource

| User A | User B | User C | ... |
```



즉 Bandwidth는 시스템 설계에서 중요한 제한 조건이 된다.

따라서 통신 엔지니어는 항상

> **같은 Bandwidth 안에 얼마나 많은 정보를 넣을 수 있는가?**

를 고민하게 된다.

---



## 18. Spectral Efficiency

한정된 Bandwidth를 얼마나 효율적으로 사용하는지를 나타내는 지표가 **Spectral Efficiency**이다.

단위는



```text
bit/s/Hz
```



이다.

즉

> **1 Hz의 주파수 자원으로 초당 몇 Bit를 전달할 수 있는가?**

를 나타낸다.

앞선 글에서 다룬 고차 QAM은 Spectral Efficiency를 높이는 대표적인 방법이다.



```text
Higher-Order QAM
        ↓
Bits / Symbol ↑
        ↓
Spectral Efficiency ↑
```



하지만 동시에 Constellation Point 간 거리가 가까워지면서 Noise에 더 민감해질 수 있다.

---



## 19. Bandwidth를 줄이는 대표적인 방법

원자료에서는 Bandwidth 사용량을 줄이기 위한 방법을 크게 다음과 같이 연결하고 있다.

### Source Coding

전송해야 할 데이터 자체를 줄인다.



```text
Original Data
      ↓
Compression
      ↓
Fewer Bits
```



---



### High-Order Modulation

Symbol 하나에 더 많은 Bit를 넣는다.



```text
QPSK
 ↓
16-QAM
 ↓
256-QAM

Bits / Symbol ↑
```



---



### Pulse Shaping

불필요하게 넓게 퍼지는 주파수 Spectrum을 제한한다.



```text
Digital Pulse
      ↓
Pulse Shaping
      ↓
Controlled Spectrum
```



---



### OFDM 등의 Orthogonality 활용

여러 Subcarrier를 서로 간섭하지 않도록 촘촘하게 배치한다.

이 부분은 이후 **다중화와 다중 접속**에서 자세히 다룬다.

---



## 20. 하지만 Bandwidth를 무작정 줄일 수는 없다

Bandwidth를 줄이는 것은 좋은 것처럼 보이지만 항상 대가가 따른다.

예를 들어 높은 차수의 QAM을 사용하면 같은 Bandwidth 안에 더 많은 Bit를 넣을 수 있다.

하지만



```text
Bits / Symbol ↑
       ↓
Constellation Point ↑
       ↓
Decision Margin ↓
       ↓
Noise Sensitivity ↑
       ↓
BER ↑ 가능성
```



이라는 문제가 발생한다.

따라서 Bandwidth와 BER 역시 서로 독립적인 문제가 아니다.

---



## 21. Shannon Capacity

통신 시스템에서는 결국 한정된 Bandwidth와 Noise 환경에서 전달할 수 있는 정보량에 한계가 존재한다.

이를 설명하는 대표적인 개념이 **Shannon Capacity**이다.

원자료에서 다루는 관계는 다음과 같다.

[  
C = B\log_2(1+S/N)  
]

여기서

- (C): Channel Capacity
- (B): Bandwidth
- (S/N): Signal-to-Noise Ratio

이다.

이 식이 보여주는 핵심은 매우 중요하다.

> **통신 속도를 무한정 높일 수는 없으며, Channel Capacity는 Bandwidth와 SNR의 제약을 받는다.**

---



## 22. Shannon Capacity가 말해주는 것

Channel Capacity를 증가시키고 싶다면 크게 두 가지 방향을 생각할 수 있다.



```text
Capacity ↑
   │
   ├── Bandwidth ↑
   │
   └── SNR ↑
```



즉

#### 방법 1

더 넓은 주파수 자원을 사용한다.



```text
Bandwidth ↑
     ↓
Capacity ↑
```



#### 방법 2

신호를 Noise보다 강하게 만든다.



```text
SNR ↑
  ↓
Capacity ↑
```



하지만 둘 다 현실적인 비용이 있다.

---



## 23. Bandwidth와 Power의 Trade-off

Bandwidth를 늘리면 더 많은 정보를 전달할 여지가 생긴다.

하지만 주파수 자원은 제한되어 있다.

반대로 Bandwidth를 아끼기 위해 한정된 Spectrum에 더 많은 정보를 넣으려면 고차 변조 등을 사용해야 한다.

그러면 Noise에 대한 요구 조건이 높아질 수 있다.

이를 개념적으로 정리하면



```text
Bandwidth ↓
      ↓
Spectral Efficiency 요구 ↑
      ↓
Higher-Order Modulation
      ↓
Noise Margin ↓
      ↓
Higher SNR 요구
```



와 같이 연결할 수 있다.

즉

> **Bandwidth와 Power는 통신 설계에서 서로 교환되는 중요한 자원**

으로 생각할 수 있다.

---



## 24. 통신에는 왜 절대적인 최적 방식이 없는가?

예를 들어 다음 두 가지 시스템을 비교해보자.

### System A



```text
Low-Order Modulation
+
Wide Bandwidth
+
Low Complexity
```



### System B



```text
High-Order Modulation
+
Narrow Bandwidth
+
High SNR Requirement
+
High Complexity
```



어느 쪽이 무조건 더 좋은 시스템이라고 말하기 어렵다.

상황에 따라 중요한 제약 조건이 다르기 때문이다.



```text
Smartphone
Satellite
IoT Sensor
Implant
Optical Link
```



각 시스템은 서로 다른

- Bandwidth
- Power
- Distance
- Data Rate
- Reliability
- Complexity

요구 조건을 가진다.

---



## 25. Digital Regeneration에도 한계가 있다

디지털 통신의 큰 장점 중 하나는 Threshold를 이용해 손상된 신호를 다시 `0`과 `1`로 판정할 수 있다는 것이다.



```text
Noisy Signal
     ↓
Threshold Decision
     ↓
Clean Bit
```



하지만 Noise가 충분히 커서 Decision Boundary를 넘어가면 잘못된 Bit로 판정된다.



```text
Noise < Margin
→ Correct Decision

Noise > Margin
→ Wrong Decision
```



따라서 디지털 통신도 Noise에 완전히 면역인 것은 아니다.

Digital Regeneration은 **판정이 올바르게 이루어진 경우**에만 원래 신호를 복원할 수 있다.

---



## 26. 그래서 Channel Coding이 필요하다

Noise 때문에 일부 Bit Error가 발생하더라도 데이터를 복원하기 위해 사용하는 기술이 **Channel Coding**이다.

Channel Coding의 핵심은 일부러 **Redundancy**를 추가하는 것이다.



```text
Original Bits
      ↓
Add Redundancy
      ↓
Encoded Bits
      ↓
Channel
      ↓
Some Errors
      ↓
Decoder
      ↓
Recovered Bits
```



즉 오류가 발생했을 때 수신기가 이를 찾아내거나 수정할 수 있도록 추가적인 정보를 함께 보낸다.

---



## 27. Source Coding과 Channel Coding의 차이

두 개념은 이름이 비슷하지만 목적은 반대이다.



```text
Source Coding

Redundancy 제거
      ↓
Data Size ↓
      ↓
Efficiency ↑
```



반면



```text
Channel Coding

Redundancy 추가
      ↓
Error Correction Ability ↑
      ↓
Reliability ↑
```



이다.

따라서 통신 시스템에서는

> **효율성을 위해 Redundancy를 제거한 뒤, 신뢰성을 위해 필요한 만큼 다시 Redundancy를 추가한다.**

라는 흥미로운 구조가 나타난다.

---



## 28. Block Coding

원자료에서는 Channel Coding의 대표적인 구조 중 하나로 **Block Coding**을 다룬다.

입력 Bit를 일정한 크기의 Block으로 묶는다.



```text
k bits
  ↓
Encoder
  ↓
n bits
```



여기서

[  
n > k  
]

이므로 추가적인 Redundant Bit가 포함된다.

Code Rate는

[  
R_c = \frac{k}{n}  
]

으로 표현할 수 있다.

Code Rate가 낮아질수록 더 많은 Redundancy를 추가하는 구조라고 볼 수 있다.

---



## 29. Convolutional Coding

Convolutional Coding은 입력 데이터를 독립적인 Block으로만 처리하지 않고 **이전 입력의 상태까지 함께 이용하는 구조**이다.

개념적으로는



```text
Current Bit
     +
Previous State
     ↓
Encoded Output
```



과 같이 생각할 수 있다.

원자료에서는 Shift Register와 modulo-2 연산을 이용하여 연속적인 Bit Stream을 부호화하는 방식으로 설명하고 있다.

복호 과정에서는 Viterbi Algorithm과 같은 방식이 연결된다.

---



## 30. Channel Coding의 Trade-off

Channel Coding을 강하게 적용하면 Error를 복구할 가능성을 높일 수 있다.



```text
Redundancy ↑
      ↓
Error Correction Ability ↑
      ↓
BER ↓
```



하지만 무료로 얻는 것은 아니다.



```text
Redundancy ↑
      ↓
Effective Data Rate ↓

Decoder Complexity ↑
Latency ↑
Power Consumption ↑
```



라는 문제가 발생한다.

따라서 Channel Coding도 결국 시스템 전체의 trade-off 문제이다.

---



## 31. Coding Gain을 어떻게 바라볼 것인가?

원자료에서는 Channel Coding을 적용하면 동일한 BER을 얻기 위해 필요한 SNR 또는 Eb/N0 조건을 개선할 수 있다는 관점으로 설명하고 있다.

개념적으로



```text
No Coding
BER │\
    │ \
    │  \
    │   \____

With Coding
BER │ \
    │  \
    │   \____
    │
    └────────────→ Eb/N0
```



처럼 생각할 수 있다.

즉 강력한 Error Correction을 이용하면 송신 전력을 무작정 높이지 않고도 신뢰성을 높일 수 있다.

하지만 그 대신

- Redundant Bits
- Decoder Computation
- Latency
- Hardware Complexity

를 지불하게 된다.

---



## 32. 앞선 QPSK 질문을 다시 바라보기

앞선 자료에는 BPSK와 QPSK의 BER 및 (E_b/N_0) 관계에 대해 서로 다른 설명이 포함되어 있었다.

따라서 지금 단계에서는 이를 단순히



```text
M 증가
→ 무조건 Eb/N0 증가
```



처럼 일반화하지 않는 것이 좋다.

변조 성능을 비교할 때에는 최소한



```text
BER인가?
SER인가?

Bit Energy 기준인가?
Symbol Energy 기준인가?

동일한 Bit Rate인가?
동일한 Symbol Rate인가?

Gray Coding을 사용하는가?
```



와 같은 조건을 함께 확인해야 한다.

이 부분은 별도로 수식을 이용해 검증해야 하는 문제로 남겨둔다.

---



## 33. Adaptive Communication으로 연결

무선 채널은 시간에 따라 상태가 달라질 수 있다.

그렇다면 항상 같은 변조 방식과 Coding Rate를 사용할 필요는 없다.

채널 상태가 좋다면



```text
High SNR
   ↓
High-Order QAM
   ↓
High Data Rate
```



를 사용할 수 있다.

반대로 채널 상태가 나쁘다면



```text
Low SNR
   ↓
Lower-Order Modulation
+
Stronger Coding
   ↓
Reliability ↑
```



를 선택할 수 있다.

이러한 아이디어가 이후 배우게 되는 **Adaptive Modulation and Coding(AMC)**과 연결된다.

---



## 34. Living Hybrid Chip 관점에서 바라보기

Living Hybrid Chip 또는 Implantable Bioelectronic System에서 통신을 한다고 생각해보자.



```text
Biological Signal
        ↓
Electrode
        ↓
Analog Front-End
        ↓
ADC
        ↓
Digital Data
        ↓
Channel Coding
        ↓
Modulation
        ↓
Biological Tissue
        ↓
External Receiver
```



여기에서 중요한 문제는 단순히

> 데이터를 보낼 수 있는가?

가 아니다.

체내 시스템에서는 다음과 같은 제약을 동시에 고려해야 한다.



```text
Very Low Power
Small Chip Area
Heat Limitation
Biological Channel
Required Reliability
Limited Data Rate
```



특히 Channel이 공기가 아니라 **생체 조직**일 수 있다는 점이 중요하다.

즉 일반적인 무선 통신과 동일한 방식으로만 생각할 수 없고, 해당 환경에서 발생하는 감쇠와 채널 특성을 함께 고려해야 한다.

---



## 35. 초저전력 시스템에서 생기는 질문

체내 소자의 전력을 줄이기 위해 송신 Power를 낮추면



```text
Transmit Power ↓
       ↓
Received SNR ↓
       ↓
BER ↑ 가능성
```



이 생길 수 있다.

그러면 이를 보완하기 위해



```text
Stronger Channel Coding
Lower-Order Modulation
Lower Data Rate
```



등을 고려할 수 있다.

하지만 Channel Coding을 강화하면 Decoder Complexity가 증가한다.

이때 한 가지 중요한 시스템 설계 아이디어가 생긴다.



```text
Implant Side
→ 가능한 단순하고 저전력

External Receiver
→ 더 복잡한 연산 허용
```



즉 시스템 양쪽의 복잡도를 반드시 동일하게 설계할 필요는 없다.

전력이 극도로 제한된 쪽의 부담을 줄이고, 에너지 여유가 있는 외부 시스템에 더 많은 연산을 맡기는 구조를 생각할 수 있다.

---



## 36. 이 글의 핵심 Trade-off 지도

이번 글의 내용을 하나의 그림으로 정리하면 다음과 같다.



```text
                Data Rate ↑
                     │
                     │
        ┌────────────┼────────────┐
        │            │            │
        ↓            ↓            ↓
   Bandwidth ↑   High-Order    SNR 요구 ↑
                Modulation
                     │
                     ↓
                 BER ↑ 가능성
                     │
                     ↓
              Channel Coding 강화
                     │
          ┌──────────┴──────────┐
          ↓                     ↓
    Reliability ↑        Complexity ↑
                         Latency ↑
                         Power ↑
```



즉 디지털 통신 시스템에서는 어느 하나의 변수만 독립적으로 최적화할 수 없다.

---



## 37. 디지털 통신의 핵심 설계 문제

지금까지 내용을 압축하면 통신 시스템은 결국 다음 문제를 푸는 것이다.

> **주어진 Channel과 Bandwidth, Power 안에서 원하는 Data Rate와 BER을 어떻게 만족시킬 것인가?**

이를 위해



```text
Source Coding
Modulation
Channel Coding
Power Control
Bandwidth Allocation
Receiver Detection
```



등의 기술을 조합한다.

각 기술은 하나의 문제를 해결하지만 동시에 다른 비용을 만든다.

이것이 통신 시스템에서 **Trade-off**가 계속 등장하는 이유이다.

---



## 38. 생각해볼 질문

이번 내용을 이해했는지 확인하려면 다음 질문에 답해보는 것이 좋다.

1. **Channel은 단순히 공기만을 의미하는가?**
2. **유선 채널보다 무선 채널의 예측이 어려운 이유는 무엇인가?**
3. **Path Loss와 Fading은 무엇이 다른가?**
4. **Multipath가 왜 신호를 강하게 만들기도 하고 약하게 만들기도 하는가?**
5. **Noise와 Interference는 어떻게 다른가?**
6. **왜 Receiver의 동작을 Decision 문제라고 볼 수 있는가?**
7. **Decision Boundary를 넘어가면 왜 Bit Error가 발생하는가?**
8. **BER는 무엇을 측정하는 지표인가?**
9. **SNR이 높아질수록 일반적으로 BER이 낮아지는 이유는 무엇인가?**
10. **SNR과 Eb/N0는 어떤 관점의 차이를 가지는가?**
11. **Signal Bandwidth와 Channel Bandwidth는 무엇이 다른가?**
12. **Spectral Efficiency의 단위가 bit/s/Hz인 이유는 무엇인가?**
13. **고차 QAM이 Bandwidth 효율을 높이면서 Noise에 취약해질 수 있는 이유는 무엇인가?**
14. **Shannon Capacity에서 Bandwidth와 SNR이 모두 등장하는 이유는 무엇인가?**
15. **송신 Power를 무한정 높일 수 없다면 BER을 줄이는 다른 방법에는 무엇이 있는가?**
16. **Source Coding과 Channel Coding이 Redundancy를 반대로 다루는 이유는 무엇인가?**
17. **Channel Coding으로 BER을 낮출 때 어떤 비용을 지불해야 하는가?**
18. **체내 Implant에서 송신기를 단순하게 하고 외부 Receiver를 복잡하게 만드는 구조가 왜 유리할 수 있는가?**

---



## 핵심 정리

송신된 Symbol은 Channel을 지나면서 손상된다.



```text
Transmitted Symbol
        ↓
Channel
        ↓
Noise / Fading / Interference
        ↓
Received Signal
        ↓
Receiver Decision
        ↓
Recovered Symbol
```



Receiver가 얼마나 정확하게 판정할 수 있는가는



```text
SNR
Eb/N0
Symbol Distance
Bandwidth
Modulation
Channel Coding
```



등과 연결되어 있다.

그리고 통신 시스템에는 근본적인 자원 제한이 존재한다.



```text
Bandwidth
Power
Complexity
Latency
```



이러한 제한 안에서



```text
Data Rate ↑
BER ↓
```



를 동시에 달성하려고 하는 것이 디지털 통신 설계의 핵심이다.

따라서 디지털 통신을 공부할 때 가장 중요한 사고방식은

> **“이 기술이 무엇인가?”에서 끝나는 것이 아니라, “이 기술이 어떤 자원을 사용해서 어떤 문제를 해결하며 그 대가로 무엇을 희생하는가?”까지 보는 것**

이다.

다음 글에서는 제한된 Channel을 여러 신호와 사용자가 어떻게 함께 나누어 사용하는지 살펴보기 위해 **Multiplexing, FDM, TDM, FDMA, TDMA, CDMA, OFDM/OFDMA, Packet Switching**을 연결해서 다룬다.

---

블록도 기준: [11. 채널](../../blocks/channel.md) · [전체 로드맵](../../roadmap.md)
