# 디지털 변조와 신호자리표: Bit에서 QAM까지

[TOC]

Digital Communication에서 Bit Stream이 만들어졌다고 해서 그대로 Wireless Channel을 통해 전송할 수 있는 것은 아니다.

Digital Bit를 실제 Physical Channel에 적합한 Waveform으로 변환해야 한다.

이 과정이 **Digital Modulation**이다.

전체적인 흐름은 다음과 같다.



```text
Bit Stream
    ↓
Bit Grouping
    ↓
Symbol
    ↓
Signal Mapping
    ↓
Modulation
    ↓
Bandpass Waveform
    ↓
Channel
```



대표적인 Digital Modulation 방식에는



```text
ASK
FSK
PSK
QAM
```



등이 있다.

이번 글에서는 특히



```text
BPSK
QPSK
M-PSK
M-QAM
Constellation
Gray Coding
SNR
BER
Decision Boundary
```



의 관계를 중심으로 정리한다.

---

## 1. Bit와 Symbol 다시 보기

Digital Communication에서 가장 먼저 구분해야 하는 것이



```text
Bit
vs
Symbol
```



이다.

Bit는



```text
0
1
```



이라는 Binary Information의 기본 단위이다.

반면 Symbol은 **한 Symbol Interval 동안 전송되는 하나의 Signal State**이다.

---



## 2. M-ary Modulation

한 Symbol이 가질 수 있는 가능한 상태의 수를



$$
M
$$



이라고 하자.

한 Symbol에 $k$ bit를 담으면



$$
M=2^k
$$



이다.

따라서



$$
k=\log_2M
$$



이다.

즉

> **M이 커질수록 한 Symbol에 더 많은 Bit를 담을 수 있다.**

---



## 3. Bits per Symbol

대표적인 Modulation 방식의 Bits/Symbol 관계는 다음과 같다.

| Modulation | $M$ | Bits/Symbol |
| --- | --- | --- |
| BPSK | 2 | 1 |
| QPSK | 4 | 2 |
| 8-PSK | 8 | 3 |
| 16-PSK | 16 | 4 |
| 4-QAM | 4 | 2 |
| 16-QAM | 16 | 4 |
| 32-QAM | 32 | 5 |
| 64-QAM | 64 | 6 |

관계식은 항상



$$
k=\log_2M
$$



이다.

예를 들어 64-QAM에서는



$$
\log_2 64=6
$$



이므로 한 Symbol에 6 bit를 담는다.

---

## 4. Symbol Rate와 Bit Rate

한 Symbol에 $k$ bit를 담는다고 하자.

Uncoded Mapping을 기준으로 하면



$$
R_b=kR_s
$$



이다.

여기서

- $R_b$ : Bit Rate
- $R_s$ : Symbol Rate
- $k$ : Bits/Symbol

이다.

따라서 동일한 Symbol Rate라면



```text
M ↑
→ k ↑
→ Bit Rate ↑
```



가 된다.

예를 들어 같은 Symbol Rate에서



```text
BPSK
→ 1 bit/symbol

QPSK
→ 2 bits/symbol

16-QAM
→ 4 bits/symbol

64-QAM
→ 6 bits/symbol
```



이므로 더 높은 $M$을 사용하면 같은 Symbol 수로 더 많은 Bit를 전송할 수 있다.

---



## 5. PSK

PSK는

**Phase Shift Keying**

의 약자이다.

Carrier의 **Phase**를 변화시켜 Digital Information을 표현한다.

Carrier를



$$
s(t)=A\cos(2\pi f_ct+\phi)
$$



라고 하면 PSK에서는



```text
A
→ 기본적으로 일정

fc
→ 일정

φ
→ Symbol에 따라 변경
```



된다.

---



## 6. BPSK

BPSK는

**Binary Phase Shift Keying**

이다.

$M=2$이므로 두 개의 Phase State를 사용한다.

대표적으로



```text
Bit 0 → 0°
Bit 1 → 180°
```



와 같이 표현할 수 있다.

Constellation으로 보면



```text
Q
↑
│
│
●───────────● → I
```



처럼 I축에 두 개의 점이 존재한다.

한 Symbol에 담는 Bit 수는



$$
\log_2 2=1
$$



이므로



```text
1 bit/symbol
```



이다.

---



## 7. BPSK의 특징

BPSK는 두 Symbol이 Constellation에서 매우 멀리 떨어져 있다.



```text
●────────────────●
```



따라서 제한된 평균 Signal Energy를 기준으로 할 때 다른 고차 PSK에 비해 Symbol을 구분하기 위한 여유가 크다.

장점은

- 구조가 단순함
- Detection이 비교적 쉬움
- Noise에 대한 Robustness가 좋음

이다.

단점은



```text
1 bit/symbol
```



밖에 전달하지 못하므로 높은 Spectral Efficiency가 필요할 때는 제한적이라는 점이다.

---



## 8. QPSK

QPSK는

**Quadrature Phase Shift Keying**

이다.

$M=4$이므로 4개의 Phase State를 사용한다.

대표적인 Phase 배치는



```text
45°
135°
225°
315°
```



처럼 구성할 수 있다.

Constellation은 다음과 같은 형태이다.



```text
Q
↑

●           ●


      +────────→ I


●           ●
```



한 Symbol에 담을 수 있는 Bit는



$$
\log_2 4=2
$$



이므로



```text
2 bits/symbol
```



이다.

---



## 9. QPSK Mapping

2 bit로 가능한 조합은



```text
00
01
10
11
```



이다.

따라서 각각의 Bit Pattern을 하나의 Constellation Point에 Mapping할 수 있다.

예를 들어 Gray Mapping에서는



```text
        Q
        ↑

  01         00


        +────────→ I


  11         10
```



과 같이 배치할 수 있다.

---



## 10. BPSK와 QPSK 비교

| 항목 | BPSK | QPSK |
| --- | --- | --- |
| $M$ | 2 | 4 |
| Bits/Symbol | 1 | 2 |
| Phase States | 2 | 4 |
| 구조 | 단순 | 상대적으로 복잡 |
| 동일 Symbol Rate에서 Bit Rate | 기준 | 2배 |

따라서 같은 Symbol Rate를 유지한다면 QPSK는 BPSK보다 두 배의 Bit Rate를 얻을 수 있다.

---



## 11. BPSK와 QPSK의 BER에서 주의할 점

여기서 중요한 부분이 있다.

교과서에서는 흔히

> **Coherent BPSK와 Gray-coded QPSK는 같은 $E_b/N_0$에서 동일한 BER을 가진다.**

라고 배운다.

그런데 강의 Simulation 표에서는



```text
BER = 10^-6

BPSK → SNR ≈ 10.6 dB
QPSK → SNR ≈ 13.6 dB
```



로 약 3 dB의 차이가 나타난다.

이 두 설명은 반드시 모순되는 것은 아니다.

---



## 12. $E_b$와 $E_s$

한 Symbol에 $k$ bit가 들어가면



$$
E_s=kE_b
$$



이다.

QPSK는



$$
k=2
$$



이므로



$$
E_s=2E_b
$$



이다.

dB로 변환하면



$$
10\log_{10}2 \approx3.01\ \mathrm{dB}
$$



이다.

따라서 BER Curve의 가로축을



```text
Eb/N0
```



로 두느냐,



```text
Es/N0
또는 Symbol-level SNR
```



로 두느냐에 따라 BPSK와 QPSK의 Graph 위치가 다르게 보일 수 있다.

즉



```text
같은 Eb/N0 기준
→ BPSK와 Gray-coded QPSK의 BER은 동일한 이론적 관계

Symbol Energy 또는 특정 SNR 정의 기준
→ 약 3 dB 차이가 나타날 수 있음
```



으로 이해하면 된다.

---



## 13. M-PSK

PSK를 일반화하면 **M-PSK**가 된다.



```text
M=2
→ BPSK

M=4
→ QPSK

M=8
→ 8-PSK

M=16
→ 16-PSK
```



이다.

Constellation Point는 일반적으로 원 위에 배치된다.



```text
              ●
        ●           ●

    ●                   ●

        ●           ●
              ●
```



---



## 14. M이 커지면 Phase 간격은 어떻게 되는가?

M-PSK에서 인접한 Symbol 사이의 Angular Separation은



$$
\Delta\theta=\frac{2\pi}{M}
$$



이다.

따라서



```text
M ↑
→ Phase Separation ↓
```



이다.

예를 들어



```text
BPSK
→ 180°

QPSK
→ 90°

8-PSK
→ 45°

16-PSK
→ 22.5°
```



가 된다.

즉 M이 증가할수록 Constellation Point가 점점 촘촘해진다.

---



## 15. QAM

QAM은

**Quadrature Amplitude Modulation**

이다.

QAM에서는 두 개의 직교 Carrier를 이용한다.



```text
I
→ In-phase

Q
→ Quadrature
```



이다.

복소 Baseband Signal은



$$
s=I+jQ
$$



로 표현할 수 있다.

실제 Passband Signal은



$$
s(t) = I\cos(2\pi f_ct) - Q\sin(2\pi f_ct)
$$



와 같이 표현할 수 있다.

---



## 16. QAM은 무엇을 변화시키는가?

I와 Q Component의 Amplitude를 각각 변화시키면 합성된 Carrier의

- Amplitude
- Phase

가 모두 바뀔 수 있다.

따라서 QAM은 직관적으로



```text
Amplitude Variation
+
Phase Variation
```



을 동시에 활용하는 Modulation이라고 볼 수 있다.

강의에서는 이를 ASK와 PSK의 특성을 함께 사용하는 방식으로 설명할 수 있다.

---



## 17. QAM Constellation

16-QAM을 예로 들면 16개의 Constellation Point를 사용한다.



```text
Q
↑

●   ●   ●   ●

●   ●   ●   ●

●   ●   ●   ●

●   ●   ●   ●

──────────────→ I
```



따라서



$$
M=16
$$



이고



$$
k=\log_2 16=4
$$



이므로



```text
4 bits/symbol
```



을 전송한다.

---



## 18. 고차 QAM

QAM도 M 값을 증가시킬 수 있다.



```text
4-QAM
16-QAM
32-QAM
64-QAM
256-QAM
...
```



예를 들어



$$
64=2^6
$$



이므로 64-QAM은 한 Symbol에 6 bit를 전달한다.



```text
64-QAM
→ 6 bits/symbol
```



이다.

---



## 19. QPSK와 4-QAM

이상적인 Constellation을 적절히 Normalize하면 QPSK와 4-QAM은 사실상 동일한 Signal Set으로 볼 수 있다.

둘 모두



```text
M = 4
2 bits/symbol
```



이며 네 개의 Constellation Point를 사용한다.

따라서 실제 통신이론에서는 4-QAM과 QPSK의 BER 성능이 동일하게 나타나는 경우가 일반적이다.

강의 표에서도



```text
QPSK  → 13.6 dB
4-QAM → 13.6 dB
```



로 같은 값을 보였다.

---



## 20. Constellation Diagram

Constellation Diagram은 Digital Modulation Signal을 I-Q Plane 위의 Point로 표현한다.



```text
       Q
       ↑

       │       ●
       │
───────┼────────────→ I
       │
       │ ●
```



각 Point는 하나의 Symbol을 의미한다.

따라서



```text
Constellation Point
↔
Symbol
↔
Bit Pattern
```



의 관계가 있다.

---



## 21. Decision Region

Receiver는 수신된 Point가 어느 Symbol에 가장 가까운지를 판단한다.

이를 위해 I-Q Plane을 여러 Region으로 나눈다.

예를 들어 QPSK라면



```text
        Q
        ↑

  S2    │    S1
        │
────────┼────────→ I
        │
  S3    │    S4
        │
```



처럼 각 영역을 특정 Symbol에 할당할 수 있다.

이때 영역을 나누는 경계가

**Decision Boundary**

이다.

---



## 22. Noise가 없을 때

이상적인 경우 송신 Point가



```text
●
```



였다면 Receiver에서도 정확히 같은 위치에 도달한다.

따라서 Symbol Detection이 매우 쉽다.

---



## 23. Noise가 존재하면

실제 Channel에서는 Noise가 더해진다.

따라서 하나의 Ideal Point가 Receiver에서는 여러 Point로 퍼져 보인다.



```text
Ideal

      ●

Received

    · ·
  · ·●··
   · · ·
```



Noise가 작으면 대부분의 Point가 원래 Decision Region 안에 남는다.

---



## 24. Decision Boundary를 넘으면 Error가 발생한다

Noise가 충분히 커지면 Received Point가 Decision Boundary를 넘어갈 수 있다.



```text
       │
   ●   │
       │ ·
───────┼────────
       │
```



Receiver는 가장 가까운 다른 Symbol로 판단하게 된다.

이를 **Symbol Error**라고 한다.

---



## 25. "Decision Boundary가 작아진다"의 정확한 의미

고차 Modulation에서 흔히

> "Decision Boundary가 좁아진다."

라고 표현하지만 조금 더 정확하게 말하면

> **Constellation Point와 가장 가까운 Decision Boundary 사이의 거리가 감소한다.**

이다.

Constellation에서 인접 Point 사이의 최소 거리를



$$
d_{\min}
$$



이라고 하면 이상적인 Decision Boundary까지의 거리는 대략



$$
\frac{d_{\min}}{2}
$$



와 관련된다.

따라서 평균 Signal Power를 고정한 상태에서 M을 증가시키면 일반적으로



```text
M ↑
→ Point Density ↑
→ dmin ↓
→ Noise Margin ↓
→ Symbol Error Probability ↑
```



가 된다.

---



## 26. 왜 M을 무한히 높이지 않는가?

M을 증가시키면



$$
k=\log_2M
$$



이 증가한다.

따라서



```text
M ↑
→ Bits/Symbol ↑
→ Data Rate ↑
```



라는 장점이 있다.

하지만 동시에



```text
Constellation Point 간 거리 ↓
→ Noise Margin ↓
→ 같은 BER을 유지하려면 더 높은 SNR 필요
```



라는 문제가 생긴다.

즉

> **Data Rate와 Reliability 사이에는 Trade-off가 존재한다.**

---



## 27. BER

BER은

**Bit Error Rate**

이다.

실험적인 BER은



$$
BER = \frac{\text{Number of Error Bits}} {\text{Total Transmitted Bits}}
$$



로 정의할 수 있다.

예를 들어



```text
1,000,000 bit 전송
1 bit 오류
```



라면



$$
BER=10^{-6}
$$



이다.

---



## 28. SNR

SNR은

**Signal-to-Noise Ratio**

이다.



$$
SNR = \frac{P_s}{P_n}
$$



이다.

dB로 나타내면



$$
SNR_{dB} = 10\log_{10} \left( \frac{P_s}{P_n} \right)
$$



이다.

같은 Noise Power를 가정하면



```text
Signal Power ↑
→ SNR ↑
```



이다.

---



## 29. SNR과 BER 관계

일반적으로 SNR이 증가하면 Detection이 쉬워지므로 BER이 감소한다.



```text
SNR ↑
→ Noise 영향 상대적으로 ↓
→ Detection 정확도 ↑
→ BER ↓
```



따라서 BER Curve는 일반적으로



```text
BER
↑
│\
│ \
│  \
│    \
│      \____
└────────────→ SNR
```



처럼 감소하는 형태를 가진다.

---



## 30. 오늘 강의 Simulation Result

강의에서는



$$
BER=10^{-6}
$$



을 만족시키기 위해 필요한 SNR을 비교하였다.

#### M-PSK

| Modulation | Bits/Symbol | 강의 Simulation의 $SNR_{\min}$ |
| --- | --- | --- |
| BPSK | 1 | 10.6 dB |
| QPSK | 2 | 13.6 dB |
| 8-PSK | 3 | 18.8 dB |
| 16-PSK | 4 | 24.3 dB |

#### M-QAM

| Modulation | Bits/Symbol | 강의 Simulation의 $SNR_{\min}$ |
| --- | --- | --- |
| 4-QAM | 2 | 13.6 dB |
| 16-QAM | 4 | 20.5 dB |
| 32-QAM | 5 | 24.4 dB |
| 64-QAM | 6 | 26.6 dB |

이 표에서 가장 중요한 것은 숫자 자체를 외우는 것이 아니라 **경향성**이다.



```text
M ↑
→ Bits/Symbol ↑

하지만

M ↑
→ 동일 BER에 필요한 SNR ↑
```



이다.

> **주의:** 위 SNR 값은 강의에서 사용한 특정 Simulation 조건의 결과이며 모든 Communication System에서 고정적으로 나타나는 보편 상수는 아니다.

---



## 31. BPSK → QPSK → 8-PSK → 16-PSK

PSK에서는 M이 커질수록 한 Symbol에 더 많은 Bit를 보낼 수 있다.



```text
BPSK
1 bit

QPSK
2 bits

8-PSK
3 bits

16-PSK
4 bits
```



하지만 Constellation Point의 Angular Separation은 줄어든다.

따라서 같은 BER을 유지하려면 더 좋은 SNR이 필요하다.

강의 Simulation에서는



```text
10.6
→ 13.6
→ 18.8
→ 24.3 dB
```



로 증가하였다.

---



## 32. 같은 $M$이라면 PSK와 QAM 중 어떤 것이 유리한가?

강의에서는 16-PSK와 16-QAM을 비교하였다.

둘 다



$$
M=16
$$



이므로



```text
4 bits/symbol
```



을 전달한다.

하지만 강의 Simulation에서



```text
16-PSK
→ 24.3 dB

16-QAM
→ 20.5 dB
```



였다.

즉 같은 BER 조건에서 16-QAM이 더 낮은 SNR을 요구하였다.

---



## 33. 왜 고차에서는 QAM이 유리할 수 있는가?

M-PSK는 모든 Point가 하나의 원 위에 있다.



```text
○ ○ ○ ○ ○ ○ ○ ○
```



따라서 M이 커지면 Angular Separation이 매우 빠르게 작아진다.

QAM은 I-Q Plane의 2차원 공간을 이용하여 Point를 배치한다.



```text
● ● ● ●
● ● ● ●
● ● ● ●
● ● ● ●
```



따라서 동일한 평균 Energy와 같은 M을 비교할 때 고차 Modulation에서는 QAM이 Constellation Space를 더 효율적으로 사용할 수 있다.

이 때문에 실제 고속 통신에서는 높은 M의 PSK보다 QAM이 널리 사용된다.

---



## 34. 16-PSK와 32-QAM 비교

오늘 강의 Simulation에서 흥미로운 비교는



```text
16-PSK
→ 4 bits/symbol
→ 24.3 dB

32-QAM
→ 5 bits/symbol
→ 24.4 dB
```



이다.

해당 Simulation에서는 요구 SNR이 거의 비슷한데 32-QAM은 Symbol 하나당 1 bit를 더 전달한다.



```text
거의 비슷한 요구 SNR

16-PSK
→ 4 bits/symbol

32-QAM
→ 5 bits/symbol
```



따라서 이 조건에서는 QAM이 더 높은 Data Efficiency를 제공할 수 있음을 보여준다.

다만 이 비교 역시 해당 Simulation 조건에서의 결과이며 일반적인 모든 환경에서 정확히 같은 수치가 나온다는 뜻은 아니다.

---



## 35. 그렇다면 항상 QAM을 사용하면 되는가?

그렇지는 않다.

QAM은 Amplitude와 Phase를 모두 정확하게 제어하고 복원해야 한다.

따라서 고차 QAM에서는

- Linear RF Amplifier
- 정확한 I/Q Generation
- Carrier Synchronization
- Channel Estimation
- Equalization

등의 요구조건이 더 까다로워질 수 있다.

즉



```text
QAM

Spectral Efficiency ↑
Power Efficiency가 유리할 수 있음

하지만

System Complexity ↑
```



이다.

반면 PSK는 Constant-envelope에 가까운 구조를 사용할 수 있는 경우가 있어 RF Hardware 관점에서 유리한 상황도 존재한다.

---



## 36. Gray Coding

Constellation Point에 Bit Pattern을 아무렇게나 할당하는 것은 아니다.

실제 Digital Communication에서는 **Gray Coding**을 많이 사용한다.

Gray Code의 핵심은

> **서로 인접한 Constellation Point의 Bit Pattern이 한 Bit만 다르도록 배치하는 것**

이다.

---



## 37. 4-QAM Gray Mapping

예를 들어



```text
        Q
        ↑

  01         00


        +────────→ I


  11         10
```



을 보자.

인접한 Point를 비교하면



```text
00 ↔ 01
→ 1 bit 차이

00 ↔ 10
→ 1 bit 차이

01 ↔ 11
→ 1 bit 차이

11 ↔ 10
→ 1 bit 차이
```



이다.

---



## 38. Binary Mapping과의 차이

단순 Binary 순서로 Mapping하면 인접한 Point 사이에서도 여러 Bit가 동시에 달라질 수 있다.

예를 들어



```text
01
↕
10
```



이라면 두 Bit 모두 다르다.

만약 Noise 때문에 01 Symbol을 10으로 잘못 판단하면



```text
Symbol Error 1회
→ Bit Error 2개
```



가 발생한다.

---



## 39. Gray Coding의 장점

통신에서 많은 Symbol Error는 충분히 높은 SNR 영역에서 가장 가까운 Neighbor Point로 잘못 판단되는 형태가 많다.

Gray Coding을 적용하면 인접 Symbol끼리 한 Bit만 다르기 때문에



```text
Nearest-neighbor Symbol Error
        ↓
대부분 1 Bit Error
```



로 만들 수 있다.

따라서 BER을 줄이는 데 유리하다.

---



## 40. Gray Coding이 Symbol Error를 줄이는 것은 아니다

중요한 점이 있다.

Gray Coding은 Constellation Point의 물리적 위치를 바꾸는 것이 아니다.

따라서 동일한 Constellation에서



```text
Symbol Error Probability
```



자체가 크게 달라지는 것은 아니다.

달라지는 것은

> **한 번의 Symbol Error가 몇 개의 Bit Error로 이어지는가**

이다.

즉



```text
Gray Coding
→ SER 자체를 줄이는 목적 X

Gray Coding
→ Symbol Error가 발생했을 때 Bit Error 수를 줄임
```



이라고 이해해야 한다.

---



## 41. BER과 SER

SER은

**Symbol Error Rate**

이다.

BER은 Bit Error Rate이다.

한 Symbol에 $k$ bit가 포함되어 있으므로 Symbol 하나가 잘못 판단되면 최대 여러 Bit가 동시에 잘못될 수 있다.

Gray Coding을 사용하고 높은 SNR에서 Nearest-neighbor Error가 지배적인 경우 흔히



$$
P_b \approx \frac{P_s}{k}
$$



와 같은 근사 관계를 사용할 수 있다.

하지만 이는 모든 Modulation과 모든 SNR 영역에서 정확한 등식은 아니다.

---



## 42. Noise가 Constellation에 미치는 영향

Ideal 32-QAM Signal이 있다고 하자.

송신기에서는



```text
● ● ● ●
● ● ● ●
● ● ● ●
...
```



처럼 정확한 Point에서 Signal이 전송된다.

하지만 Channel에서 Noise가 추가되면 Receiver에서는



```text
  ··●··
 · ● ···
··   ·●·
```



처럼 Point 주변에 Cloud가 형성된다.

Noise Power가 커질수록 Cloud의 크기도 커진다.

---



## 43. Noise만 있다면

AWGN과 같은 단순한 Noise를 생각하면 Ideal Constellation Point를 중심으로 Point들이 Random하게 퍼진다.



```text
Ideal Point
     ●

Noise

   · ·
 · ·● ·
   · ·
```



이때 평균적인 Constellation 중심 자체는 크게 회전하지 않는다.

---



## 44. Fading이 존재하면

Wireless Channel은 Complex Gain으로 표현할 수 있다.

Flat Fading Channel을 단순화하면



$$
y=hx+n
$$



이다.

Channel Coefficient를



$$
h=|h|e^{j\theta}
$$



라고 하면



$$
y = |h|e^{j\theta}x+n
$$



이다.

여기서



```text
|h|
→ Amplitude Scaling

e^{jθ}
→ Phase Rotation
```



의 역할을 한다.

따라서 Flat Fading이 발생하면 Constellation 전체가



```text
크기가 변하고
+
회전
```



할 수 있다.

---



## 45. 오늘 강의의 핵심: Delay와 Phase Rotation

강의에서는 Time Delay가 Frequency Domain에서 어떤 결과를 만드는지 Fourier Transform을 이용하여 설명하였다.

Original Signal이



$$
x(t)
$$



일 때 Fourier Transform은



$$
X(f) = \int_{-\infty}^{\infty} x(t)e^{-j2\pi ft}dt
$$



이다.

이번에는 Signal이 $\Delta t$만큼 Delay되었다고 하자.



$$
x(t-\Delta t)
$$



이다.

Fourier Transform을 하면



$$
\mathcal{F} \{x(t-\Delta t)\} = X(f)e^{-j2\pi f\Delta t}
$$



가 된다.

---



## 46. Time Shift Property

따라서 Fourier Transform의 중요한 Property는



$$
\boxed{ x(t-\Delta t) \longleftrightarrow X(f)e^{-j2\pi f\Delta t} }
$$



이다.

여기서



$$
e^{-j2\pi f\Delta t}
$$



는 Complex Exponential이다.

Magnitude는



$$
\left| e^{-j2\pi f\Delta t} \right| =1
$$



이고 Phase는



$$
-2\pi f\Delta t
$$



이다.

따라서 **순수한 Time Delay는 Frequency Domain에서 Phase Shift로 나타난다.**

---



## 47. Delay → Phase Rotation

흐름을 정리하면



```text
Propagation Delay Δt
        ↓
Frequency Domain
        ↓
e^(-j2πfΔt)
        ↓
Phase Shift
        ↓
Constellation Rotation
```



으로 이해할 수 있다.

특히 Narrowband Signal 또는 하나의 Carrier Frequency $f_c$를 생각하면



$$
e^{-j2\pi f_c\Delta t}
$$



가 거의 공통적인 Phase Rotation으로 나타날 수 있다.

---



## 48. Multipath에서는 더 복잡해진다

실제 Fading에서는 하나의 Delay만 존재하는 것이 아니다.

여러 Multipath가 존재하면



$$
h(t) = \sum_l a_l\delta(t-\tau_l)
$$



처럼 생각할 수 있다.

Frequency Response는



$$
H(f) = \sum_l a_l e^{-j2\pi f\tau_l}
$$



가 된다.

각 Path가

- 서로 다른 Amplitude
- 서로 다른 Delay
- 서로 다른 Phase

를 가지므로 모두 더해진 결과가 Channel Response가 된다.

---



## 49. Flat Fading에서는 Constellation 전체가 회전할 수 있다

Signal Bandwidth가 충분히 좁아 Channel을 하나의 Complex Coefficient로 근사할 수 있다면



$$
y=hx+n
$$



로 표현할 수 있다.

따라서



```text
Ideal Constellation
        ↓
h = |h|e^(jθ)
        ↓
Scaling + Rotation
```



이 된다.

강의에서 Constellation 축 전체가 돌아간 그림은 이러한 상황의 직관적인 예라고 볼 수 있다.

---



## 50. Frequency-selective Fading에서는 단순 회전이 아니다

주의할 점은 모든 Fading이 Constellation을 단순히 하나의 Angle만큼 회전시키는 것은 아니라는 것이다.

Frequency-selective Channel에서는



$$
H(f)
$$



가 Frequency에 따라 달라진다.

따라서 Signal의 서로 다른 Frequency Component가 서로 다른

- Gain
- Phase

를 경험한다.



```text
f1 → h1
f2 → h2
f3 → h3
```



결과적으로 단순한 전체 회전보다 더 복잡한 Distortion과 ISI가 발생할 수 있다.

이 내용은 다음 글인

**통신 채널과 페이딩: Multipath, Delay Spread, Coherence Time부터 MIMO까지**

에서 자세히 연결된다.

---



## 51. Phase Rotation이 Error를 만드는 이유

Receiver의 Decision Boundary는 원래 Constellation을 기준으로 만들어져 있다고 하자.



```text
        Q
        ↑

   ●    │    ●
        │
────────┼────────→ I
        │
   ●    │    ●
```



Fading에 의해 Constellation이 회전하면



```text
     ●
          ●

  ●
       ●
```



처럼 보일 수 있다.

원래 Point가 자신의 Decision Region 밖으로 이동하면 Receiver는 다른 Symbol로 판단한다.



```text
Phase Rotation
      ↓
Decision Boundary 침범
      ↓
Symbol Error
      ↓
Bit Error
```



가 된다.

---



## 52. Noise + Fading

Noise와 Fading이 동시에 존재하면 상황은 더 나빠질 수 있다.



```text
Ideal Constellation
       ↓
Fading
       ↓
Scaling / Rotation
       ↓
Noise
       ↓
Point Spreading
```



즉



```text
Fading
→ Constellation 중심 자체를 이동/회전

Noise
→ 그 중심 주변으로 Random하게 퍼뜨림
```



으로 생각할 수 있다.

이 때문에 Fading Channel에서는 Noise-only Channel보다 BER Performance가 크게 악화될 수 있다.

---



## 53. CFO는 무엇이 다른가?

Constellation Rotation의 또 다른 원인은 **Carrier Frequency Offset(CFO)**이다.

송신기와 수신기의 Oscillator Frequency가 미세하게 다르면



$$
\Delta f \neq0
$$



이 되고 Received Signal에는



$$
e^{j2\pi\Delta ft}
$$



와 같은 Term이 나타날 수 있다.

따라서 Constellation이 한 Angle로 고정되어 있는 것이 아니라



```text
시간이 지나면서
계속 회전
```



할 수 있다.

---



## 54. Phase Noise

실제 Oscillator는 완벽한 Sinusoid를 생성하지 못한다.

미세한 Phase Fluctuation이 존재할 수 있다.

이를

**Phase Noise**

라고 한다.

Phase Noise가 존재하면 Constellation Point가



```text
점 형태
→ 원호 / 부채꼴 형태
```



로 퍼질 수 있다.

---



## 55. Constellation 왜곡 원인 비교

| 원인 | 대표적인 Constellation 영향 |
| --- | --- |
| AWGN | Point 주변 Random Spread |
| Flat Fading | Scaling + Phase Rotation |
| Multipath / Frequency-selective Fading | Frequency-dependent Distortion |
| Constant Phase Offset | 일정 Angle 회전 |
| CFO | 시간에 따라 계속 회전 |
| Phase Noise | Angular Direction으로 퍼짐 |
| I/Q Imbalance | 비대칭 / 타원형 왜곡 가능 |

---



## 56. Receiver는 어떻게 보상하는가?

Receiver는 Channel Effect를 그대로 두지 않는다.

대표적으로



```text
Channel Estimation
Equalization
Carrier Recovery
Phase Tracking
Timing Synchronization
```



등을 수행한다.

예를 들어 Flat Fading Channel에서



$$
y=hx+n
$$



이고 $h$를 알고 있다면 단순화하여



$$
\hat{x} = \frac{y}{h}
$$



와 같은 방식으로 Channel Effect를 보상할 수 있다.

이를 **Equalization**의 기본적인 직관으로 볼 수 있다.

---



## 57. Carrier Recovery

Receiver가 Carrier Phase와 Frequency를 정확하게 맞추지 못하면 PSK와 QAM Detection이 어려워진다.

따라서



```text
PLL
Carrier Recovery
Phase Tracking
```



등을 이용하여 Carrier Reference를 복원한다.

특히 QAM처럼 Phase와 Amplitude 모두 중요한 Modulation에서는 정확한 Carrier Recovery가 매우 중요하다.

---



## 58. Modulation 선택은 Trade-off 문제이다

좋은 Modulation 방식 하나가 모든 System에 항상 최적인 것은 아니다.

예를 들어 M을 증가시키면



```text
Bits/Symbol ↑
Transmission Rate ↑
Spectral Efficiency ↑
```



를 얻을 수 있다.

하지만 동시에



```text
Constellation Density ↑
Noise Margin ↓
Required SNR ↑
Receiver Complexity ↑
```



가 될 수 있다.

따라서 실제 System에서는 여러 조건을 동시에 고려해야 한다.

---



## 59. Digital Communication System의 설계 목표

강의에서는 Digital Communication System의 주요 설계 목표를 다음과 같이 정리하였다.

#### 최대화하고 싶은 것



```text
Transmission Rate R ↑

System Utilization U ↑
```



#### 최소화하고 싶은 것



```text
Bit Error Rate Pe ↓

Required Bandwidth W ↓

System Complexity Cx ↓

Required Power / Eb/N0 ↓
```



이다.

---



## 60. 모든 목표를 동시에 만족할 수 있는가?

현실적으로는 어렵다.

대부분 서로 **Trade-off** 관계에 있기 때문이다.

예를 들어



```text
M ↑
→ Transmission Rate ↑

하지만

Required SNR ↑
Complexity ↑
```



가 될 수 있다.

또한



```text
Bandwidth ↓
```



만 추구하면 원하는 Data Rate를 얻기 어려울 수 있다.

Error Correction을 강하게 사용하면



```text
BER ↓
```



를 얻을 수 있지만



```text
Redundancy ↑
Complexity ↑
Latency ↑
```



가 될 수 있다.

---



## 61. Power와 Data Rate Trade-off

오늘 강의의 Modulation 표가 보여주는 대표적인 Trade-off가 바로 이것이다.



```text
M ↑
        ↓
Bits/Symbol ↑
        ↓
Data Rate ↑

하지만

Constellation Distance ↓
        ↓
Required SNR ↑
        ↓
Required Transmit Power ↑
```



이다.

따라서 Battery-powered Device에서는 무조건 가장 높은 M을 선택할 수 없다.

---



## 62. Complexity와 Power Trade-off

예를 들어 고차 QAM은 높은 Spectral Efficiency를 제공할 수 있지만

- 정확한 RF Linearity
- ADC Resolution
- Channel Estimation
- Equalization
- Phase Tracking

등을 요구할 수 있다.

따라서



```text
Modulation Efficiency ↑
        ↓
Hardware / DSP Complexity ↑
        ↓
Power Consumption ↑
        ↓
Cost ↑
```



가 될 수 있다.

---



## 63. Adaptive Modulation

실제 Wireless Communication에서는 Channel Condition에 따라 Modulation 방식 자체를 바꾸기도 한다.

예를 들어



```text
SNR 낮음
→ BPSK / QPSK

SNR 중간
→ 16-QAM

SNR 높음
→ 64-QAM / Higher-order QAM
```



처럼 선택할 수 있다.

Channel이 좋을 때는 높은 M을 사용하여 많은 Data를 보내고, Channel이 나쁠 때는 낮은 M을 이용해 Reliability를 높이는 것이다.

이를 **Adaptive Modulation** 관점으로 이해할 수 있다.

---



## 64. 전체 흐름으로 다시 보기

이번 글의 내용을 하나의 흐름으로 연결하면 다음과 같다.



```text
Bit Stream
     ↓
Bit Grouping
     ↓
k = log2(M)
     ↓
Symbol
     ↓
Constellation Mapping
     ↓
BPSK / QPSK / M-PSK / M-QAM
     ↓
Bandpass Signal
     ↓
Channel
     ↓
Fading + Noise
     ↓
Constellation Distortion
     ↓
Decision
     ↓
Symbol Error
     ↓
Bit Error
```



---



## 65. M 증가의 전체 Trade-off



```text
M ↑
│
├─ Bits/Symbol ↑
│
├─ Bit Rate ↑     (fixed Symbol Rate 조건)
│
├─ Spectral Efficiency ↑ 가능
│
├─ Constellation Point 간 거리 ↓
│
├─ Noise Margin ↓
│
├─ Required SNR ↑
│
├─ Required Power ↑ 가능
│
└─ Receiver Complexity ↑
```



즉

> **더 많은 정보를 한 Symbol에 담는 대신 Signal을 더 정확하게 구분해야 한다.**

라는 것이 고차 Modulation의 핵심이다.

---



## 66. PSK와 QAM 비교

| 항목 | M-PSK | M-QAM |
| --- | --- | --- |
| 주요 정보 표현 | Phase | I/Q Amplitude → Amplitude + Phase |
| Constellation | 주로 원 위 | 2차원 Grid 형태 |
| 고차 M에서 Point 간격 | 빠르게 감소 | 공간을 비교적 효율적으로 사용 |
| Hardware | 비교적 단순한 경우 많음 | 더 높은 Linearity 요구 가능 |
| 고차 변조 효율 | 제한적 | 우수 |
| 대표 활용 | BPSK, QPSK 등 | 16/64/256-QAM 등 |

---



## 67. 시험에서 가장 중요한 관계

#### M과 Bit



$$
\boxed{ M=2^k }
$$





$$
\boxed{ k=\log_2M }
$$



---



#### Bit Rate와 Symbol Rate



$$
\boxed{ R_b=kR_s }
$$



---



#### Symbol Energy와 Bit Energy



$$
\boxed{ E_s=kE_b }
$$



---



#### SNR



$$
\boxed{ SNR=\frac{P_s}{P_n} }
$$



---



#### Time Delay



$$
\boxed{ x(t-\tau) \longleftrightarrow X(f)e^{-j2\pi f\tau} }
$$



---



#### Flat Fading



$$
\boxed{ y=hx+n }
$$





$$
\boxed{ h=|h|e^{j\theta} }
$$



따라서



```text
|h|
→ Amplitude Scaling

θ
→ Phase Rotation
```



이다.

---



## 68. 핵심 비교

| Modulation | $M$ | Bits/Symbol | 특징 |
| --- | --- | --- | --- |
| BPSK | 2 | 1 | 단순하고 Robust |
| QPSK | 4 | 2 | BPSK보다 높은 Bit Efficiency |
| 8-PSK | 8 | 3 | 더 높은 Rate, Noise Margin 감소 |
| 16-PSK | 16 | 4 | 높은 SNR 요구 |
| 4-QAM | 4 | 2 | QPSK와 동등한 Signal Set으로 볼 수 있음 |
| 16-QAM | 16 | 4 | 고차 PSK보다 효율적인 경우 많음 |
| 32-QAM | 32 | 5 | 더 높은 Spectral Efficiency |
| 64-QAM | 64 | 6 | 높은 SNR 및 정밀한 Receiver 요구 |

---



## 69. Gray Coding 핵심



```text
Adjacent Constellation Point
        ↓
1 Bit만 다르게 Mapping
        ↓
Nearest-neighbor Symbol Error 발생
        ↓
Bit Error 수 최소화
```



즉

> **Gray Coding은 Symbol Error 자체보다 Symbol Error가 Bit Error로 확장되는 피해를 줄이기 위한 Mapping 방법**이다.

---



## 70. Constellation 왜곡 핵심



```text
Noise
→ Point Spread

Flat Fading
→ Scaling + Rotation

Delay
→ Phase Shift

CFO
→ Continuous Rotation

Phase Noise
→ Angular Spread
```



이다.

---



## 71. DCS 설계의 핵심

좋은 Digital Communication System은 단순히 Data Rate가 가장 높은 System이 아니다.

궁극적으로는



```text
Transmission Rate ↑
System Utilization ↑

BER ↓
Bandwidth ↓
Complexity ↓
Required Power ↓
```



를 동시에 고려해야 한다.

하지만 이 조건들은 서로 충돌하는 경우가 많기 때문에 실제 Communication System Design은 결국

> **Performance, Bandwidth, Power, Complexity, Cost 사이의 Trade-off를 결정하는 과정**

이라고 볼 수 있다.

---



## 핵심 정리

Digital Modulation에서는 여러 Bit를 하나의 Symbol로 묶어 실제 전송 가능한 Waveform으로 변환한다.

M-ary System에서



$$
M=2^k
$$



이고



$$
k=\log_2M
$$



이다.

따라서 M을 증가시키면



```text
Bits/Symbol ↑
→ Data Rate ↑
```



의 장점을 얻을 수 있다.

하지만 평균 Signal Power가 제한된 환경에서는



```text
M ↑
→ Constellation Point 간 거리 ↓
→ Decision Margin ↓
→ Required SNR ↑
```



가 된다.

PSK에서는 Phase를 이용해 정보를 표현하며, QAM은 I와 Q 두 축을 활용하여 고차 Modulation에서 Constellation Space를 보다 효율적으로 사용할 수 있다.

Gray Coding은 인접 Symbol의 Bit Pattern을 한 Bit만 다르게 배치하여 Symbol Error가 발생했을 때 Bit Error 수를 줄인다.

Channel을 통과하면서



```text
Noise
Fading
Delay
Carrier Offset
```



등이 발생하면 Constellation이



```text
퍼지고
회전하고
크기가 변하고
왜곡
```



될 수 있다.

특히



$$
x(t-\tau) \longleftrightarrow X(f)e^{-j2\pi f\tau}
$$



이므로 Time Delay는 Frequency Domain에서 Phase Shift로 나타난다.

결국 Digital Modulation의 핵심은



```text
Data Rate
Reliability
Bandwidth
Power
Complexity
```



사이에서 적절한 Modulation Scheme을 선택하는 것이다.

---



## 한 문장으로 정리

Digital Modulation은 **Bit를 Symbol과 Constellation Point로 Mapping하여 실제 전송 Waveform을 만드는 과정이며, 높은 M을 사용하면 한 Symbol에 더 많은 Bit를 담을 수 있지만 Point 간 거리가 가까워져 동일한 BER을 유지하기 위해 더 높은 SNR과 정밀한 Receiver가 필요하므로 PSK·QAM·Gray Coding·Channel Compensation을 포함한 전체 System Trade-off를 함께 고려해야 한다.**

---

블록도 기준: [07. 대역통과 변조](../../blocks/bandpass-modulation.md) · [전체 로드맵](../../roadmap.md)
