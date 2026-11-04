# QPSK 완전 정리: I/Q 직교 변조부터 복소 기저대역 신호·Gray Encoding·위상 성상도와 대역폭 효율까지

[TOC]

Digital Communication에서는 Binary Data를 실제 통신 채널을 통해 전송하기 위해 Carrier Signal에 정보를 실어야 한다.

이때 Carrier Signal의 Amplitude, Frequency, Phase 등을 변화시키는 과정을 **Digital Modulation(디지털 변조)** 이라고 한다.

대표적인 Digital Modulation 방식에는 ASK, FSK, PSK가 있다.

이 중 PSK(Phase Shift Keying)는 Carrier Signal의 Phase를 변화시켜 Digital Information을 전송하는 방식이다.

대표적인 PSK 방식은 다음과 같다.

- **BPSK(Binary Phase Shift Keying)**: 2개의 위상을 사용하여 Symbol당 1bit 전송
- **QPSK(Quadrature Phase Shift Keying)**: 4개의 위상을 사용하여 Symbol당 2bit 전송

QPSK의 중요한 특징은 동일한 Carrier Frequency를 사용하는 두 개의 직교 반송파를 통해 서로 다른 정보를 전송할 수 있다는 점이다.

두 반송파는 다음과 같다.



$$
\boxed{\cos(\omega_ct)}
$$





$$
\boxed{\sin(\omega_ct)}
$$



두 신호는 Orthogonal(직교) 관계를 가지므로, 하나의 전송 신호로 합쳐지더라도 수신기에서 각각의 정보를 분리할 수 있다.

이번 글에서는 QPSK의 수학적 원리와 실제 송신기 구조를 연결하여 다음 내용을 살펴본다.

- Orthogonal Carrier Multiplexing
- In-phase(I)와 Quadrature(Q)
- Complex Baseband Representation
- QPSK Transmitter Architecture
- Polar NRZ Encoding과 Pulse Shaping
- QPSK Constellation Diagram
- Gray Encoding
- Phase Representation
- Symbol Rate와 Bandwidth Efficiency

---



## 1. QPSK란?

QPSK는 Quadrature Phase Shift Keying의 약자이다.

Carrier Signal의 위상을 네 가지 상태 중 하나로 변화시켜 정보를 전송하는 Digital Modulation 방식이다.

일반적인 Carrier Signal은 다음과 같다.



$$
\boxed{s(t)=A\cos(\omega_ct+\theta)}
$$



여기서

- $A$: Carrier Amplitude
- $\omega_c=2\pi f_c$: Angular Carrier Frequency
- $f_c$: Carrier Frequency
- $\theta$: Phase

이다.

PSK에서는 Digital Information에 따라 $\theta$를 변화시킨다.

QPSK에서는 4개의 Phase State를 사용한다.

대표적인 위상 집합은 다음과 같다.



$$
\boxed{ \theta\in \left\{ \frac{\pi}{4}, \frac{3\pi}{4}, \frac{5\pi}{4}, \frac{7\pi}{4} \right\} }
$$



이를 Degree로 표현하면 다음과 같다.



$$
\boxed{ \theta\in \{45^\circ,135^\circ,225^\circ,315^\circ\} }
$$



네 위상은 원주상에서 서로 $90^\circ$ 간격으로 배치된다.

중요한 점은 QPSK에서 사용하는 네 신호의 **Carrier Frequency와 Amplitude는 같고 Phase만 다르다**는 것이다.

---



### 1.1 BPSK와 QPSK의 차이

BPSK는 두 개의 Phase State를 사용한다.



$$
\boxed{M=2}
$$



따라서 하나의 Symbol이 표현할 수 있는 Bit 수는 다음과 같다.



$$
k=\log_2M=\log_22=1
$$



반면 QPSK는 다음과 같다.



$$
\boxed{M=4}
$$



따라서



$$
\boxed{ k=\log_24=2 }
$$



이다.

| 구분 | BPSK | QPSK |
| --- | --- | --- |
| Modulation Order $M$ | 2 | 4 |
| Phase State | 2개 | 4개 |
| Bits per Symbol | 1bit | 2bit |
| 위상 간격 | $180^\circ$ | $90^\circ$ |
| Signal Dimension | 1차원 | 2차원 I/Q |

즉 QPSK는 하나의 Symbol에 2bit의 정보를 포함한다.

이를 가능하게 하는 핵심 개념이 **Orthogonal Carrier Multiplexing**이다.

---



## 2. 직교 반송파를 이용한 다중화

QPSK에서는 동일한 Frequency를 가지면서 서로 직교하는 두 Carrier를 사용한다.



$$
\boxed{ c_I(t)=\cos(\omega_ct) }
$$





$$
\boxed{ c_Q(t)=\sin(\omega_ct) }
$$



두 함수의 차이는 $90^\circ$의 Phase Shift이다.

삼각함수의 성질에 의해



$$
\boxed{ \cos\left(\omega_ct-\frac{\pi}{2}\right) = \sin(\omega_ct) }
$$



이다.

즉 Cosine Carrier를 $-90^\circ$만큼 Shift하면 Sine Carrier가 된다.

반대로



$$
\boxed{ \cos\left(\omega_ct+\frac{\pi}{2}\right) = -\sin(\omega_ct) }
$$



이다.

Cosine과 Sine은 위상이 $90^\circ$ 다르다.

그렇다면 왜 서로 직교한다고 하는 것일까?

---



### 2.1 Orthogonality의 수학적 의미

두 함수 $f_1(t)$와 $f_2(t)$의 직교성은 특정 구간에서 다음 Inner Product가 0이 되는 관계로 설명할 수 있다.



$$
\boxed{ \int_0^{T_s} f_1(t)f_2(t)\,dt=0 }
$$



여기서 $T_s$는 Symbol Duration이다.

Cosine과 Sine의 경우 다음 적분을 생각할 수 있다.



$$
\int_0^{T_s} \cos(\omega_ct)\sin(\omega_ct)\,dt
$$



삼각함수의 항등식을 이용하면



$$
\sin(2x)=2\sin x\cos x
$$



이므로



$$
= \frac12 \int_0^{T_s} \sin(2\omega_ct)\,dt
$$



이다.

적분하면



$$
= \left[ -\frac{\cos(2\omega_ct)}{4\omega_c} \right]_0^{T_s}
$$



따라서



$$
\boxed{ \int_0^{T_s} \cos(\omega_ct)\sin(\omega_ct)\,dt = \frac{1-\cos(2\omega_cT_s)}{4\omega_c} }
$$



이다.

Symbol Duration이 적절하게 설정되어



$$
\omega_cT_s=n\pi
$$



를 만족하면



$$
\cos(2\omega_cT_s)=1
$$



이므로



$$
\boxed{ \int_0^{T_s} \cos(\omega_ct)\sin(\omega_ct)\,dt=0 }
$$



이 된다.

즉 적절한 Symbol Interval에서 두 Carrier는 서로 직교한다.

---



### 2.2 직교성은 왜 중요한가?

서로 다른 두 정보를 동일한 Carrier에 그대로 더한다고 가정하자.



$$
s(t) = I(t)\cos(\omega_ct) + Q(t)\cos(\omega_ct)
$$



공통항을 묶으면



$$
s(t) = [I(t)+Q(t)]\cos(\omega_ct)
$$



이다.

따라서 수신기에서는 일반적으로 $I(t)+Q(t)$라는 합성 정보만 관찰할 수 있다.

두 신호를 독립적으로 복원하기 어렵다.

그러나 서로 직교하는 두 Carrier를 사용하면 다음과 같이 표현할 수 있다.



$$
\boxed{ s(t) = I(t)\cos(\omega_ct) - Q(t)\sin(\omega_ct) }
$$



이 경우 수신기는 Cosine과 Sine의 Orthogonality를 이용하여 두 정보를 분리할 수 있다.



```text
Information I ── × cos(ωct) ──┐
                              │
                              ├── Σ ── Transmitted Signal
                              │
Information Q ── × -sin(ωct) ─┘
```



즉 **하나의 전송 신호 안에 서로 독립적인 두 정보 성분을 포함할 수 있다.**

단, 직교 반송파 두 개를 이용한다고 두 개의 서로 다른 주파수 대역이 필요한 것은 아니다.

두 반송파는 같은 Carrier Frequency를 사용하며, 위상 차이로 구분된다.

---



## 3. I(In-phase)와 Q(Quadrature)

직교 반송파를 이용한 Modulation에서는 두 가지 정보 성분을 사용한다.

**In-phase Component**



$$
I(t)
$$



Cosine Carrier와 결합하는 성분이다.

**Quadrature Component**



$$
Q(t)
$$



Sine Carrier와 결합하는 성분이다.

이 두 성분을 이용하면 Bandpass Signal을 다음과 같이 표현할 수 있다.



$$
\boxed{ s(t) = I(t)\cos(2\pi f_ct) - Q(t)\sin(2\pi f_ct) }
$$



이 식은 Digital Bandpass Modulation에서 매우 중요한 표현이다.

여기서 부호는 반드시 기억해야 한다.

이번 글에서는 다음 Convention을 사용한다.



$$
\boxed{ I\cos(\omega_ct)-Q\sin(\omega_ct) }
$$



다른 교재나 시스템에서는 $+Q\sin(\omega_ct)$ 형태를 사용할 수도 있다.

그 경우에는 Q축의 정의와 Phase Mapping 역시 이에 맞게 달라진다.

따라서 서로 다른 자료의 식을 비교할 때는 Q Component의 부호를 먼저 확인해야 한다.

---



## 4. Complex Baseband Representation

Bandpass Signal을 보다 간결하게 표현하기 위해 Complex Number를 이용할 수 있다.

이때 다음과 같은 Complex Baseband Signal을 정의한다.



$$
\boxed{ \tilde{s}(t)=I(t)+jQ(t) }
$$



여기서 $j$는 Imaginary Unit이다.



$$
j^2=-1
$$



Complex Baseband Signal은 Real Part와 Imaginary Part를 가진다.



$$
\operatorname{Re}\{\tilde{s}(t)\}=I(t)
$$





$$
\operatorname{Im}\{\tilde{s}(t)\}=Q(t)
$$



이를 Carrier Frequency로 Upconversion하면 실제 Bandpass Signal을 얻을 수 있다.

---



### 4.1 Complex Baseband에서 Bandpass Signal 유도

Complex Exponential을 이용하면 다음과 같이 표현할 수 있다.



$$
\boxed{ s(t) = \operatorname{Re} \left\{ \tilde{s}(t)e^{j\omega_ct} \right\} }
$$



여기에



$$
\tilde{s}(t)=I(t)+jQ(t)
$$



를 대입하면



$$
s(t) = \operatorname{Re} \left\{ [I(t)+jQ(t)]e^{j\omega_ct} \right\}
$$



이다.

Euler's Formula에 의해



$$
e^{j\omega_ct} = \cos(\omega_ct)+j\sin(\omega_ct)
$$



이다.

따라서



$$
s(t) = \operatorname{Re} \left\{ [I+jQ][\cos(\omega_ct)+j\sin(\omega_ct)] \right\}
$$



전개하면



$$
= \operatorname{Re} \left\{ I\cos(\omega_ct) +jI\sin(\omega_ct) +jQ\cos(\omega_ct) +j^2Q\sin(\omega_ct) \right\}
$$



여기서 $j^2=-1$이므로



$$
= \operatorname{Re} \left\{ I\cos(\omega_ct) -Q\sin(\omega_ct) +j[I\sin(\omega_ct)+Q\cos(\omega_ct)] \right\}
$$



Real Part만 취하면 최종적으로 다음 식을 얻는다.



$$
\boxed{ s(t) = I(t)\cos(\omega_ct) - Q(t)\sin(\omega_ct) }
$$



즉 Complex Baseband Representation으로부터 직교 반송파 기반 Bandpass Modulation Equation을 유도할 수 있다.

---



## 5. Cartesian Form과 Polar Form

Complex Number는 두 가지 형태로 표현할 수 있다.

#### Cartesian Form(직교 좌표 표현)



$$
\boxed{ \tilde{s}(t)=I(t)+jQ(t) }
$$



#### Polar Form(극좌표 표현)



$$
\boxed{ \tilde{s}(t)=R(t)e^{j\theta(t)} }
$$



여기서



$$
\boxed{ R(t)=\sqrt{I^2(t)+Q^2(t)} }
$$



이며



$$
\boxed{ \theta(t)=\operatorname{atan2}(Q(t),I(t)) }
$$



이다.

`atan2`를 사용하는 이유는 I와 Q의 부호를 모두 고려하여 Phase의 Quadrant를 결정하기 위해서이다.

즉 하나의 Complex Signal을 다음과 같이 표현할 수 있다.



$$
\boxed{ I(t)+jQ(t) = R(t)e^{j\theta(t)} }
$$



---



### 5.1 Polar Form으로 Bandpass Signal 유도

앞에서 다음 관계를 정의하였다.



$$
s(t) = \operatorname{Re} \left\{ \tilde{s}(t)e^{j\omega_ct} \right\}
$$



여기에



$$
\tilde{s}(t)=R(t)e^{j\theta(t)}
$$



를 대입하면



$$
s(t) = \operatorname{Re} \left\{ R(t)e^{j\theta(t)}e^{j\omega_ct} \right\}
$$



지수법칙에 의해



$$
e^{j\theta(t)}e^{j\omega_ct} = e^{j[\omega_ct+\theta(t)]}
$$



이므로



$$
s(t) = \operatorname{Re} \left\{ R(t)e^{j[\omega_ct+\theta(t)]} \right\}
$$



이다.

따라서



$$
\boxed{ s(t) = R(t)\cos[\omega_ct+\theta(t)] }
$$



를 얻는다.

이 식에서 중요한 사실을 확인할 수 있다.

**I와 Q의 값이 달라지면 Complex Signal의 Magnitude와 Phase가 달라지고, 결과적으로 Carrier Signal의 Amplitude와 Phase를 변화시킬 수 있다.**

QPSK에서는 I와 Q의 크기를 적절하게 제한하여 Magnitude를 일정하게 유지하고, Phase만 네 가지 상태로 변화시킨다.

---



## 6. QPSK 송신기 구조

이제 QPSK가 실제로 어떻게 Digital Bit Stream을 Bandpass Signal로 변환하는지 살펴보자.

QPSK Transmitter는 개념적으로 다음 과정으로 구성된다.



```text
                 Input Bit Stream
                        │
                        ▼
                Polar NRZ Encoding
                        │
                        ▼
                Serial-to-Parallel
                        │
                 ┌──────┴──────┐
                 │             │
                 ▼             ▼
              I Bits        Q Bits
                 │             │
                 ▼             ▼
           Pulse Shaping  Pulse Shaping
                 │             │
                 ▼             ▼
                I(t)          Q(t)
                 │             │
                 ▼             ▼
            × cos(ωct)    × -sin(ωct)
                 │             │
                 └──────┬──────┘
                        │
                        ▼
                     Addition
                        │
                        ▼
                 QPSK Signal s(t)
                        │
                        ▼
                    RF Transmission
```



최종적인 Transmitted Signal은 다음과 같다.



$$
\boxed{ s_{QPSK}(t) = I(t)\cos(\omega_ct) - Q(t)\sin(\omega_ct) }
$$



각 단계가 수행하는 역할을 살펴보자.

---



## 7. Polar NRZ Encoding

QPSK에서는 Digital Bit를 I/Q Component로 Mapping해야 한다.

이를 위해 Binary Data를 Polar 형태로 변환하는 방법을 사용할 수 있다.

대표적인 Polar NRZ Encoding은 다음과 같다.



$$
\boxed{ 0\rightarrow-1 }
$$





$$
\boxed{ 1\rightarrow+1 }
$$



예를 들어 다음 Bit Stream이 있다고 하자.



```text
Original Bits

1 0 1 1 0 0 1 0
```



Polar NRZ Encoding을 수행하면 다음과 같다.



```text
Polar NRZ

+1 -1 +1 +1 -1 -1 +1 -1
```



NRZ는 Non-Return-to-Zero의 약자이다.

해당 Symbol Value가 할당된 시간 구간 동안 신호 레벨을 유지하는 방식이다.

반면 RZ(Return-to-Zero)는 Symbol Interval 내부에서 신호가 0 Level로 돌아가는 구간을 가진다.

이번 QPSK 예제에서는 0과 1을 서로 다른 두 극성으로 표현하는 Polar NRZ Mapping을 이용한다.

---



## 8. Serial-to-Parallel Conversion

QPSK는 하나의 Symbol에 2bit를 포함한다.

따라서 Serial Bit Stream을 두 개씩 묶어 I Component와 Q Component로 분리한다.

예를 들어 다음 Bit Stream이 있다고 하자.



```text
1 0 1 1 0 0 1 0
```



QPSK에서는 다음과 같이 2bit씩 묶는다.



```text
(10) (11) (00) (10)
```



각 Bit Pair가 하나의 Symbol을 구성한다.



```text
Symbol 1 → 10
Symbol 2 → 11
Symbol 3 → 00
Symbol 4 → 10
```



이후 Bit Pair를 I/Q Symbol로 Mapping한다.

여기서 중요한 것은 **Mapping Rule은 송신기와 수신기가 동일하게 알고 있어야 한다**는 점이다.

Mapping Rule에 따라 Bit Pair와 I/Q Symbol의 대응 관계가 결정된다.

---



## 9. Pulse Shaping

Polar Encoding과 Serial-to-Parallel Conversion으로 얻은 I/Q Symbol은 이산적인 값이다.

이를 시간에 따른 Signal로 구성하기 위해 Pulse Shaping을 수행한다.

Pulse Shaping은 Symbol Sequence를 일정한 Pulse Shape로 변환하는 과정이다.

일반적으로 다음과 같이 나타낼 수 있다.



$$
\boxed{ I(t)=\sum_n I_np(t-nT_s) }
$$





$$
\boxed{ Q(t)=\sum_n Q_np(t-nT_s) }
$$



여기서

- $I_n$: n번째 In-phase Symbol
- $Q_n$: n번째 Quadrature Symbol
- $p(t)$: Pulse Shaping Function
- $T_s$: Symbol Duration

이다.

---



### 9.1 Rectangular Pulse

이번 강의에서는 QPSK의 기본 원리를 이해하기 위해 Rectangular Pulse를 가정하였다.



$$
\boxed{ p(t)= \begin{cases} 1,&0\le t<T_s\\ 0,&\text{otherwise} \end{cases} }
$$



즉 하나의 Symbol Interval 동안 Signal Value를 일정하게 유지한다.

예를 들어



$$
I_n=+1
$$



이라면 해당 Symbol Interval에서



$$
I(t)=+1
$$



이다.

반면



$$
Q_n=-1
$$



이라면



$$
Q(t)=-1
$$



이다.

따라서 하나의 Symbol Interval에서는 I와 Q가 일정한 값으로 유지된다.

실제 통신 시스템에서는 Bandwidth 및 Inter-Symbol Interference(ISI)를 고려하여 Raised Cosine 또는 Root Raised Cosine 계열의 Pulse Shaping을 사용할 수 있다.

다만 이번 글에서는 수식 전개를 단순화하기 위해 Rectangular Pulse를 기준으로 QPSK 신호를 분석한다.

---



## 10. QPSK Symbol과 I/Q Signal

QPSK에서는 I와 Q가 각각 두 가지 상태를 가진다.



$$
\boxed{ I_n\in\{-A,+A\} }
$$





$$
\boxed{ Q_n\in\{-A,+A\} }
$$



따라서 가능한 I/Q Combination은 다음 네 가지이다.



$$
\boxed{ (I,Q)\in \{ (-A,-A), (-A,+A), (+A,-A), (+A,+A) \} }
$$



각 Combination을 Complex Number로 표현하면 다음과 같다.



$$
\tilde{s}_1=-A-jA
$$





$$
\tilde{s}_2=-A+jA
$$





$$
\tilde{s}_3=+A-jA
$$





$$
\tilde{s}_4=+A+jA
$$



모든 Symbol의 Magnitude는 동일하다.

예를 들어



$$
|\tilde{s}_1| = \sqrt{(-A)^2+(-A)^2}
$$



이므로



$$
\boxed{ |\tilde{s}_1|=\sqrt2A }
$$



이다.

나머지 Symbol도 동일하게



$$
\boxed{ |\tilde{s}_n|=\sqrt2A }
$$



를 만족한다.

즉 Magnitude는 일정하고 Phase만 달라진다.

이러한 특징이 QPSK의 핵심이다.

---



## 11. QPSK Constellation Diagram

Constellation Diagram(성상도)은 Digital Modulation Symbol을 I/Q Plane에 표현한 그림이다.

Horizontal Axis는 In-phase Component를 나타낸다.

Vertical Axis는 Quadrature Component를 나타낸다.

이번 글에서는 강의의 Gray Encoding Constellation에 맞춰 다음 Mapping을 사용한다.



```text
                         Q
                         ↑
                         │
             00          │          10
           (-A,+A)       │        (+A,+A)
               ●         │         ●
               │         │         │
               │         │         │
─────────────────────────┼──────────────────→ I
               │         │         │
               │         │         │
               ●         │         ●
           (-A,-A)       │        (+A,-A)
             01          │          11
                         │
```



이 Diagram에서 네 점은 원점을 기준으로 동일한 거리에 위치한다.

또한 인접한 Symbol 사이의 Phase 차이는 $90^\circ$이다.

---



### 11.1 Constellation의 Phase 계산

Complex Symbol이 다음과 같다고 하자.



$$
\tilde{s}=I+jQ
$$



Polar Form으로 변환하면



$$
\boxed{ \tilde{s} = Re^{j\theta} }
$$



이다.

Magnitude는 다음과 같다.



$$
R=\sqrt{I^2+Q^2}
$$



Phase는 다음과 같다.



$$
\boxed{ \theta=\operatorname{atan2}(Q,I) }
$$



각 Quadrant에 대한 Phase는 다음과 같다.

| Quadrant | I | Q | Phase |
| --- | --- | --- | --- |
| I | $+A$ | $+A$ | $\pi/4$ |
| II | $-A$ | $+A$ | $3\pi/4$ |
| III | $-A$ | $-A$ | $-3\pi/4$ |
| IV | $+A$ | $-A$ | $-\pi/4$ |

III Quadrant의 Phase는 다음과 같이 나타낼 수도 있다.



$$
-\frac{3\pi}{4} \equiv \frac{5\pi}{4} \pmod{2\pi}
$$



IV Quadrant 역시 다음과 같이 표현할 수 있다.



$$
-\frac{\pi}{4} \equiv \frac{7\pi}{4} \pmod{2\pi}
$$



즉 동일한 Phase를 서로 다른 각도 범위로 표현할 수 있다.

---



## 12. Gray Encoding

QPSK에서 4개의 Constellation Point에 2bit를 Mapping하는 방법은 여러 가지가 가능하다.

그중 대표적으로 사용하는 방법이 **Gray Encoding(Gray Mapping)** 이다.

Gray Encoding은 인접한 Constellation Point들이 단 하나의 Bit만 다르도록 Bit Label을 배치하는 방법이다.

예를 들어 이번 강의의 Constellation은 다음과 같다.



```text
             00 ───── 10
              │         │
              │         │
             01 ───── 11
```



각 인접한 Symbol을 비교하면 다음과 같다.

- `00 ↔ 10`: 첫 번째 Bit만 다름
- `00 ↔ 01`: 두 번째 Bit만 다름
- `10 ↔ 11`: 두 번째 Bit만 다름
- `01 ↔ 11`: 첫 번째 Bit만 다름

따라서 인접한 두 Symbol의 Hamming Distance는 1이다.

---



### 12.1 Gray Encoding의 목적

무선 통신에서는 Noise에 의해 수신된 Constellation Point가 원래 위치에서 벗어날 수 있다.

예를 들어 `10` Symbol을 전송했다고 하자.

Noise의 영향으로 수신기가 인접한 `00` Symbol로 잘못 판정할 수 있다.

이 경우



```text
Transmitted : 10
Received    : 00
```



첫 번째 Bit 하나만 잘못된다.

반면 인접한 Symbol에 두 Bit가 모두 다른 Label을 부여했다면, 동일한 Symbol Error가 두 개의 Bit Error로 이어질 수 있다.

Gray Encoding은 일반적으로 가장 가까운 인접 Symbol로 잘못 검출되는 상황에서 Bit Error의 개수를 줄이는 데 유리하다.

다만 Gray Encoding 자체가 Noise를 제거하거나 Symbol Error를 방지하는 것은 아니다.

Noise가 커서 대각선 방향의 먼 Symbol로 잘못 판정되면 두 Bit가 모두 잘못될 수도 있다.

따라서 Gray Encoding은 **Error Correction Code가 아니라 Bit-to-Symbol Mapping 방법**이다.

---



## 13. QPSK의 Gray Mapping Table

| Bit Pair | I | Q | Phase |
| --- | --- | --- | --- |
| 00 | $-A$ | $+A$ | $3\pi/4$ |
| 01 | $-A$ | $-A$ | $-3\pi/4$ |
| 10 | $+A$ | $+A$ | $\pi/4$ |
| 11 | $+A$ | $-A$ | $-\pi/4$ |

이 표는 다음 Signal Convention과 일치한다.



$$
\boxed{ s(t)=I\cos(\omega_ct)-Q\sin(\omega_ct) }
$$



각 Bit Pair에 대응하는 Bandpass Signal은 다음과 같다.

#### Bit Pair = 00



$$
I=-A,\quad Q=+A
$$



따라서



$$
s_{00}(t) = -A\cos(\omega_ct)-A\sin(\omega_ct)
$$



이며



$$
\boxed{ s_{00}(t) = \sqrt2A \cos\left(\omega_ct+\frac{3\pi}{4}\right) }
$$



이다.

#### Bit Pair = 01



$$
I=-A,\quad Q=-A
$$



따라서



$$
s_{01}(t) = -A\cos(\omega_ct)+A\sin(\omega_ct)
$$



이며



$$
\boxed{ s_{01}(t) = \sqrt2A \cos\left(\omega_ct-\frac{3\pi}{4}\right) }
$$



이다.

#### Bit Pair = 10



$$
I=+A,\quad Q=+A
$$



따라서



$$
s_{10}(t) = A\cos(\omega_ct)-A\sin(\omega_ct)
$$



이며



$$
\boxed{ s_{10}(t) = \sqrt2A \cos\left(\omega_ct+\frac{\pi}{4}\right) }
$$



이다.

#### Bit Pair = 11



$$
I=+A,\quad Q=-A
$$



따라서



$$
s_{11}(t) = A\cos(\omega_ct)+A\sin(\omega_ct)
$$



이며



$$
\boxed{ s_{11}(t) = \sqrt2A \cos\left(\omega_ct-\frac{\pi}{4}\right) }
$$



이다.

결과적으로 모든 Signal의 Amplitude와 Carrier Frequency는 동일하다.



$$
\boxed{ \text{Amplitude}=\sqrt2A }
$$





$$
\boxed{ \text{Carrier Frequency}=f_c }
$$



변하는 것은 Phase이다.

---



## 14. QPSK Signal을 하나의 Cosine Function으로 유도하기

QPSK를 이해할 때 가장 중요한 부분 중 하나는 다음 식이다.



$$
\boxed{ s(t)=I\cos(\omega_ct)-Q\sin(\omega_ct) }
$$



이 식이 어떻게 Phase Shift를 나타내는지 직접 확인해 보자.

강의에서 다룬 예제와 같이 다음 Symbol을 생각하자.



$$
\boxed{ I=-A,\quad Q=-A }
$$



이때 Transmitted Signal은 다음과 같다.



$$
s(t) = -A\cos(\omega_ct) - (-A)\sin(\omega_ct)
$$



따라서



$$
\boxed{ s(t) = -A\cos(\omega_ct) + A\sin(\omega_ct) }
$$



이다.

이를 하나의 Cosine Function으로 변환한다.

---



### 14.1 Magnitude 계산

Amplitude를 먼저 계산한다.



$$
R=\sqrt{I^2+Q^2}
$$



여기에



$$
I=-A,\quad Q=-A
$$



를 대입하면



$$
R=\sqrt{A^2+A^2}
$$



따라서



$$
\boxed{ R=\sqrt2A }
$$



이다.

이제 원래 Signal을 다음과 같이 표현할 수 있다.



$$
s(t) = \sqrt2A \left[ -\frac1{\sqrt2}\cos(\omega_ct) + \frac1{\sqrt2}\sin(\omega_ct) \right]
$$



---



### 14.2 Phase 계산

다음 Trigonometric Identity를 이용한다.



$$
\boxed{ \cos(\alpha+\beta) = \cos\alpha\cos\beta - \sin\alpha\sin\beta }
$$



이를 원래 Signal과 비교하면



$$
\cos\theta=-\frac1{\sqrt2}
$$





$$
\sin\theta=-\frac1{\sqrt2}
$$



이어야 한다.

Cosine과 Sine이 모두 음수인 영역은 III Quadrant이다.

따라서



$$
\boxed{ \theta=\frac{5\pi}{4} }
$$



이다.

또는



$$
\boxed{ \theta=-\frac{3\pi}{4} }
$$



라고 표현할 수 있다.

결과적으로



$$
\boxed{ s(t) = \sqrt2A \cos\left(\omega_ct+\frac{5\pi}{4}\right) }
$$



이며, 이는 다음 식과 동일하다.



$$
\boxed{ s(t) = \sqrt2A \cos\left(\omega_ct-\frac{3\pi}{4}\right) }
$$



이렇게 I/Q Representation으로부터 Phase Modulation 형태의 Signal을 얻을 수 있다.

즉 **I와 Q의 부호 조합에 따라 Carrier Signal의 Phase가 결정된다.**

---



## 15. QPSK의 전체 Signal Equation

지금까지의 내용을 종합하면 QPSK는 다음 네 가지 Signal 중 하나를 전송한다.



$$
\boxed{ s_{QPSK}(t) = \begin{cases} \sqrt2A\cos\left(\omega_ct+\frac{3\pi}{4}\right), &00\\[6pt] \sqrt2A\cos\left(\omega_ct-\frac{3\pi}{4}\right), &01\\[6pt] \sqrt2A\cos\left(\omega_ct+\frac{\pi}{4}\right), &10\\[6pt] \sqrt2A\cos\left(\omega_ct-\frac{\pi}{4}\right), &11 \end{cases} }
$$



위 식은 앞에서 정의한 Gray Mapping을 기준으로 작성하였다.

하나의 Symbol Interval에서는



$$
0\le t<T_s
$$



로 생각할 수 있다.

이 식에서 볼 수 있듯이 네 Signal은 동일한 Amplitude와 Frequency를 사용한다.

반면 Phase는 Bit Pair에 따라 달라진다.

따라서 QPSK는 **4개의 Phase State를 이용하여 하나의 Symbol에 2bit를 Mapping하는 Phase Modulation 방식**이다.

---



## 16. QPSK 수신기의 기본 원리

QPSK Transmitter는 I와 Q에 서로 다른 직교 반송파를 곱한 뒤 이를 합쳐 전송한다.

그렇다면 Receiver는 어떻게 두 정보를 복원할 수 있을까?

핵심은 Orthogonality이다.

Transmitted Signal은 다음과 같다.



$$
s(t) = I\cos(\omega_ct) - Q\sin(\omega_ct)
$$



Receiver는 이 Signal에 Cosine 또는 Sine Reference Signal을 곱한 뒤 적분할 수 있다.

이 방법을 Correlator 기반 Detection의 기본 원리로 이해할 수 있다.

---



### 16.1 I Component 검출

수신 신호에 Cosine을 곱한다.



$$
s(t)\cos(\omega_ct)
$$



전개하면



$$
= I\cos^2(\omega_ct) - Q\sin(\omega_ct)\cos(\omega_ct)
$$



이다.

적절한 직교 조건에서 Symbol Interval 동안 적분하면 Cross Term은 0이 된다.

따라서



$$
\int_0^{T_s}s(t)\cos(\omega_ct)\,dt = I\int_0^{T_s}\cos^2(\omega_ct)\,dt
$$



이다.

Carrier가 충분히 적절한 적분 조건을 만족하여



$$
\int_0^{T_s}\cos^2(\omega_ct)\,dt = \frac{T_s}{2}
$$



라면



$$
\boxed{ I= \frac{2}{T_s} \int_0^{T_s} s(t)\cos(\omega_ct)\,dt }
$$



로 I Component를 복원할 수 있다.

---



### 16.2 Q Component 검출

같은 방법으로 Q Component를 검출한다.

단, 이번 글의 Signal Convention은 다음과 같다.



$$
s(t)=I\cos(\omega_ct)-Q\sin(\omega_ct)
$$



따라서 Sine Reference를 사용하는 경우 부호를 고려해야 한다.



$$
\boxed{ Q= -\frac{2}{T_s} \int_0^{T_s} s(t)\sin(\omega_ct)\,dt }
$$



이다.

이는 이상적인 동기 검파 조건에서의 기본 관계이다.

실제 Receiver에서는 Carrier Synchronization, Timing Synchronization, Noise, Filtering 등을 추가로 고려해야 한다.

이후 검출된 I/Q의 부호를 판정하면 Symbol에 대응하는 Bit Pair를 복원할 수 있다.

이번 강의에서는 주로 QPSK Transmitter와 Signal Representation을 다루었으며, Correlator 및 Matched Filter 기반 Receiver의 구체적인 구조는 다음 학습 내용으로 연결된다.

---



## 17. QPSK의 Symbol Rate와 Bit Rate

QPSK가 BPSK보다 높은 Spectral Efficiency를 가지는 이유를 이해하기 위해서는 Bit Rate와 Symbol Rate를 구분해야 한다.

**Bit Rate**

단위 시간당 전송하는 Bit의 개수이다.



$$
\boxed{ R_b=\frac{1}{T_b} }
$$



여기서 $T_b$는 Bit Duration이다.

**Symbol Rate**

단위 시간당 전송하는 Symbol의 개수이다.



$$
\boxed{ R_s=\frac{1}{T_s} }
$$



여기서 $T_s$는 Symbol Duration이다.

Symbol 하나에 $k$개의 Bit를 포함한다면 두 Rate는 다음 관계를 가진다.



$$
\boxed{ R_b=kR_s }
$$



따라서



$$
\boxed{ R_s=\frac{R_b}{k} }
$$



이다.

---



### 17.1 BPSK

BPSK는 Symbol 하나에 1bit를 포함한다.



$$
k=1
$$



따라서



$$
\boxed{ R_s=R_b }
$$



이다.

---



### 17.2 QPSK

QPSK는 Symbol 하나에 2bit를 포함한다.



$$
k=2
$$



따라서



$$
\boxed{ R_s=\frac{R_b}{2} }
$$



이다.

이 식은 반드시 기억해야 한다.

**QPSK는 하나의 Symbol에 2bit를 포함하므로 동일한 Bit Rate에서 BPSK보다 Symbol Rate가 절반이다.**

따라서 Bit Duration과 Symbol Duration의 관계도 다음과 같다.



$$
\boxed{ T_s=2T_b }
$$



이다.

---



## 18. QPSK의 Bandwidth Efficiency

QPSK의 중요한 장점 중 하나는 Spectral Efficiency이다.

Spectral Efficiency는 주어진 Bandwidth에서 얼마나 많은 Bit를 전송할 수 있는지를 나타낸다.

일반적으로 다음과 같이 정의한다.



$$
\boxed{ \eta=\frac{R_b}{B} }
$$



여기서

- $\eta$: Spectral Efficiency
- $R_b$: Bit Rate
- $B$: Occupied Bandwidth

이다.

단위는 다음과 같다.



$$
\boxed{ \mathrm{bit/s/Hz} }
$$



---



### 18.1 Symbol Rate와 Bandwidth

Digital Modulation에서 필요한 Bandwidth는 Symbol Rate 및 Pulse Shaping의 영향을 받는다.

특히 Raised Cosine 계열의 Nyquist Pulse Shaping을 사용하는 이상화된 모델에서는 Passband Bandwidth를 다음과 같이 나타낼 수 있다.



$$
\boxed{ B=(1+\alpha)R_s }
$$



여기서 $\alpha$는 Roll-off Factor이다.



$$
0\le\alpha\le1
$$



이다.

이상적인 경우



$$
\alpha=0
$$



이라면



$$
\boxed{ B=R_s }
$$



이다.

실제 통신 시스템에서는 Filter Characteristics와 Bandwidth Definition에 따라 필요한 Bandwidth가 달라질 수 있다.

또한 이번 강의에서 Signal 유도를 위해 사용한 Rectangular Pulse의 Spectrum은 Sinc 형태로 무한히 확장되므로, Rectangular Pulse 자체에 엄밀한 유한 대역폭 $B=R_s$를 적용하는 것은 적절하지 않다.

따라서 아래 비교는 **동일한 Pulse Shaping과 동일한 Bandwidth 기준을 적용한 BPSK·QPSK 비교**이다.

---



### 18.2 BPSK의 Bandwidth

BPSK에서는



$$
R_s=R_b
$$



이다.

따라서 동일한 Raised Cosine Pulse Shaping을 적용하면



$$
\boxed{ B_{BPSK}=(1+\alpha)R_b }
$$



이다.

---



### 18.3 QPSK의 Bandwidth

QPSK에서는



$$
R_s=\frac{R_b}{2}
$$



이다.

따라서



$$
B_{QPSK} = (1+\alpha)R_s
$$



에 대입하면



$$
\boxed{ B_{QPSK} = (1+\alpha)\frac{R_b}{2} }
$$



이다.

따라서 동일한 Bit Rate와 동일한 Pulse Shaping 조건에서



$$
\boxed{ B_{QPSK} = \frac{B_{BPSK}}{2} }
$$



가 된다.

즉 QPSK는 동일한 Bit Rate를 전송할 때 BPSK보다 필요한 Bandwidth를 약 절반으로 줄일 수 있다.

반대로 동일한 Bandwidth를 사용하는 경우 BPSK보다 약 두 배의 Bit Rate를 전송할 수 있다.

---



## 19. BPSK와 QPSK의 Spectral Efficiency 비교

Spectral Efficiency는 다음과 같다.



$$
\eta=\frac{R_b}{B}
$$



#### BPSK



$$
B_{BPSK} = (1+\alpha)R_b
$$



따라서



$$
\boxed{ \eta_{BPSK} = \frac{1}{1+\alpha} }
$$



이다.

#### QPSK



$$
B_{QPSK} = (1+\alpha)\frac{R_b}{2}
$$



따라서



$$
\eta_{QPSK} = \frac{R_b} {(1+\alpha)R_b/2}
$$



이를 정리하면



$$
\boxed{ \eta_{QPSK} = \frac{2}{1+\alpha} }
$$



이다.

따라서



$$
\boxed{ \eta_{QPSK} = 2\eta_{BPSK} }
$$



를 얻는다.

특히 이상적인 Nyquist Condition에서 $\alpha=0$이면 다음과 같다.

| Modulation | Spectral Efficiency |
| --- | --- |
| BPSK | $1\;\mathrm{bit/s/Hz}$ |
| QPSK | $2\;\mathrm{bit/s/Hz}$ |

즉 **동일한 Pulse Shaping과 Bandwidth 정의를 적용하면 QPSK는 BPSK보다 두 배의 Spectral Efficiency를 가진다.**

이것은 서로 다른 두 개의 Frequency Band를 이용하기 때문이 아니다.

동일한 Carrier Frequency를 사용하는 I/Q Orthogonal Components를 이용하여 Symbol당 2bit를 전송하기 때문이다.

---



## 20. QPSK에서 자주 혼동하는 개념

### 20.1 QPSK는 Carrier 두 개를 전송하므로 Bandwidth도 두 배인가?

아니다.

QPSK의 I Carrier와 Q Carrier는 동일한 Frequency를 사용한다.

단지 Phase가 $90^\circ$ 다른 Orthogonal Carrier를 이용한다.

따라서 두 정보를 동시에 전송하더라도 두 배의 Frequency Band를 별도로 확보해야 하는 것은 아니다.

---



### 20.2 QPSK는 BPSK보다 항상 전송 속도가 두 배 빠른가?

정확하게는 **동일한 Symbol Rate에서 Bit Rate가 두 배**이다.



$$
R_b=kR_s
$$



BPSK는 $k=1$, QPSK는 $k=2$이기 때문이다.

반대로 동일한 Bit Rate에서는 QPSK의 Symbol Rate가 절반이다.

따라서 어떤 조건을 동일하게 유지하는지 구분해야 한다.

---



### 20.3 QPSK는 Amplitude와 Frequency도 변화시키는가?

이상적인 QPSK의 각 Symbol은 동일한 Amplitude와 Carrier Frequency를 가진다.

Bit Pair에 따라 변화하는 것은 Phase이다.



$$
\boxed{ s(t)=\sqrt2A\cos(\omega_ct+\theta) }
$$



단, 실제 Pulse Shaping을 포함한 연속시간 파형에서는 Symbol Transition 구간의 순간 Envelope가 일정하지 않을 수 있다.

따라서 이상적인 Symbol의 Constant Magnitude 특성과 실제 Pulse-shaped Waveform의 Instantaneous Envelope는 구분해야 한다.

---



### 20.4 모든 QPSK Constellation의 Bit Mapping은 동일한가?

아니다.

같은 QPSK라도 Bit Pair와 I/Q Symbol의 Mapping은 달라질 수 있다.

Constellation 전체를 회전시키거나 Bit Label을 다른 Gray Mapping으로 배치할 수도 있다.

또한



$$
I\cos(\omega_ct)-Q\sin(\omega_ct)
$$



와



$$
I\cos(\omega_ct)+Q\sin(\omega_ct)
$$



는 Q Component의 부호 정의가 서로 다르다.

따라서 외부 자료와 비교할 때는 다음 세 가지를 반드시 확인해야 한다.

1. I/Q Axis Definition
2. Q Component의 부호
3. Bit-to-Symbol Mapping Rule

특히 $-\pi$에서 $\pi$ 사이의 Phase 표현과 $0$에서 $2\pi$ 사이의 Phase 표현은 같은 물리적 위상을 다르게 표기할 수 있다.

---



### 20.5 Gray Encoding은 Bit Error를 완전히 방지하는가?

아니다.

Gray Encoding은 인접 Symbol의 Label이 1bit만 다르도록 배치하는 방법이다.

따라서 가까운 Constellation Point로 잘못 판정되었을 때 일반적으로 Bit Error 개수를 줄이는 데 유리하다.

하지만 Noise에 의한 Symbol Error 자체를 방지하는 것은 아니다.

---



## 21. 핵심 공식 총정리

#### Carrier Angular Frequency



$$
\boxed{ \omega_c=2\pi f_c }
$$



#### Complex Baseband Signal



$$
\boxed{ \tilde{s}(t)=I(t)+jQ(t) }
$$



#### Bandpass Signal



$$
\boxed{ s(t) = \operatorname{Re} \left\{ \tilde{s}(t)e^{j\omega_ct} \right\} }
$$



#### I/Q Representation



$$
\boxed{ s(t) = I(t)\cos(\omega_ct) - Q(t)\sin(\omega_ct) }
$$



#### Polar Representation



$$
\boxed{ \tilde{s}(t)=R(t)e^{j\theta(t)} }
$$



#### Magnitude



$$
\boxed{ R(t)=\sqrt{I^2(t)+Q^2(t)} }
$$



#### Phase



$$
\boxed{ \theta(t)=\operatorname{atan2}(Q(t),I(t)) }
$$



#### Phase-modulated Bandpass Signal



$$
\boxed{ s(t)=R(t)\cos[\omega_ct+\theta(t)] }
$$



#### QPSK Magnitude



$$
\boxed{ R=\sqrt2A }
$$



#### Bits per Symbol



$$
\boxed{ k=\log_2M }
$$



#### Bit Rate와 Symbol Rate



$$
\boxed{ R_b=kR_s }
$$



#### QPSK Symbol Rate



$$
\boxed{ R_s=\frac{R_b}{2} }
$$



#### Spectral Efficiency



$$
\boxed{ \eta=\frac{R_b}{B} }
$$



#### QPSK Spectral Efficiency

동일한 Raised Cosine Pulse Shaping 조건에서



$$
\boxed{ \eta_{QPSK} = \frac{2}{1+\alpha} }
$$



이다.

---



## 22. 최종 정리

QPSK는 동일한 Carrier Frequency를 사용하는 두 개의 Orthogonal Carrier를 이용하여 Digital Information을 전송하는 Phase Modulation 방식이다.

Orthogonal Carrier는 Cosine과 Sine으로 표현할 수 있다.



$$
\cos(\omega_ct),\qquad\sin(\omega_ct)
$$



두 함수는 적절한 Symbol Interval에서 서로 직교하므로, 하나의 Transmitted Signal로 합쳐지더라도 Receiver에서 각 Component를 분리할 수 있다.

QPSK에서는 두 개의 Bit를 하나의 Symbol로 Mapping한다.

각 Bit는 I Component와 Q Component를 결정하며, 이 두 Component는 다음과 같은 Complex Baseband Signal로 표현된다.



$$
\boxed{ \tilde{s}(t)=I(t)+jQ(t) }
$$



이를 Carrier Frequency로 Upconversion하면 다음 Bandpass Signal을 얻는다.



$$
\boxed{ s(t) = I(t)\cos(\omega_ct) - Q(t)\sin(\omega_ct) }
$$



Complex Baseband Signal을 Polar Form으로 표현하면 다음과 같다.



$$
\boxed{ s(t) = R(t)\cos[\omega_ct+\theta(t)] }
$$



QPSK에서는 4개의 I/Q Combination이 동일한 Magnitude를 가지므로, 결과적으로 4개의 서로 다른 Phase State를 이용하여 정보를 전송한다.

이러한 Phase State는 Constellation Diagram에 표시할 수 있으며, Gray Encoding을 이용하면 인접한 Symbol들이 1bit만 다르도록 배치할 수 있다.

또한 QPSK는 Symbol 하나에 2bit를 포함한다.

따라서



$$
\boxed{ R_s=\frac{R_b}{2} }
$$



이다.

동일한 Pulse Shaping 조건에서는 BPSK에 비해 동일한 Bit Rate를 전송할 때 필요한 Bandwidth를 줄일 수 있다.

결과적으로 QPSK의 중요한 장점은 **I/Q Orthogonality를 이용하여 하나의 Symbol에 2bit를 포함하고, BPSK보다 높은 Spectral Efficiency를 달성할 수 있다는 것**이다.

---



### 핵심 요약

> QPSK(Quadrature Phase Shift Keying)는 서로 직교하는 I/Q Carrier를 이용하여 2bit를 하나의 Symbol에 Mapping하는 Digital Phase Modulation 방식이다. Complex Baseband Signal $I+jQ$를 통해 Bandpass Signal을 표현할 수 있으며, I/Q의 부호 조합에 따라 네 가지 Phase State가 결정된다. Gray Encoding은 인접 Symbol 간 Bit 차이를 1bit로 제한하여 Bit Error 측면에서 유리한 Mapping을 제공한다. QPSK는 동일한 Pulse Shaping 조건에서 BPSK 대비 두 배의 Spectral Efficiency를 달성할 수 있다.

---



## 참고 자료

- B. P. Lathi & Zhi Ding, *Modern Digital and Analog Communication Systems*
- John G. Proakis & Masoud Salehi, *Digital Communications*
- Simon Haykin, *Communication Systems*

---

블록도 기준: [07. Bandpass Modulate](../../blocks/bandpass-modulation.md) · [전체 로드맵](../../roadmap.md)
