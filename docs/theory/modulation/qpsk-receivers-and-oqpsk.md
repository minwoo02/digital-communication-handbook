# QPSK 수신기 완전 정리: Correlator·Matched Filter부터 Bandpass/Baseband 복조와 OQPSK까지

[TOC]

앞선 글에서는 QPSK(Quadrature Phase Shift Keying)가 두 개의 직교 반송파를 이용하여 하나의 Symbol에 2bit를 실어 전송하는 원리를 살펴보았다.

QPSK의 송신 신호는 다음과 같이 표현할 수 있다.



$$
\boxed{ s(t)=I(t)\cos(\omega_ct)-Q(t)\sin(\omega_ct) }
$$



여기서 $I(t)$와 $Q(t)$는 각각 In-phase와 Quadrature Component이다.

송신기(Transmitter)는 이 두 성분을 서로 직교하는 Cosine과 Sine Carrier에 실어 하나의 Bandpass Signal로 전송한다.

그렇다면 **수신기(Receiver)는 하나로 합쳐진 신호에서 어떻게 I와 Q를 다시 분리하고 원래의 Binary Data를 복원할 수 있을까?**

이 질문에 답하기 위해서는 Orthogonality(직교성), Correlator(상관기), Matched Filter(정합 필터)를 이해해야 한다.

이번 글에서는 수신기의 복조 방식을 다음 네 가지로 구분한다.

1. Bandpass Demodulation with Correlator
2. Baseband Demodulation with Correlator
3. Bandpass Demodulation with Matched Filter
4. Baseband Demodulation with Matched Filter

이후 BPSK와 QPSK의 대역폭 효율을 서로 다른 조건에서 비교하고, 일반적인 QPSK의 위상 전이 문제를 완화하기 위한 OQPSK의 기본 개념까지 살펴본다.

---



### 1. QPSK 송신 신호 복습

QPSK 송신 신호는 다음과 같다.



$$
\boxed{ s(t)=I(t)\cos(2\pi f_ct)-Q(t)\sin(2\pi f_ct) }
$$



여기서

- $f_c$: Carrier Frequency
- $\omega_c=2\pi f_c$: Angular Carrier Frequency
- $I(t)$: In-phase Signal
- $Q(t)$: Quadrature Signal

이다.

송신기에서는 Bit Stream을 2bit씩 분리하여 I/Q Symbol로 Mapping한다.

일반적인 Polar NRZ Mapping에서는 다음과 같이 표현할 수 있다.



$$
0\rightarrow-1,\qquad 1\rightarrow+1
$$



이번 수신기 유도에서는 두 Branch의 Symbol을 각각 다음과 같이 정의한다.



$$
\boxed{ I_n,Q_n\in\{-1,+1\} }
$$



Pulse Shaping Function을 $p(t)$라고 하면



$$
\boxed{ I(t)=I_np(t) }
$$





$$
\boxed{ Q(t)=Q_np(t) }
$$



이다.

이번 강의에서는 수식 유도를 간단하게 하기 위해 Rectangular Pulse를 사용한다.



$$
\boxed{ p(t)= \begin{cases} 1,&0\le t<T_s\\ 0,&\text{otherwise} \end{cases} }
$$



따라서 하나의 Symbol Interval에서는



$$
I(t)=I_n,\qquad Q(t)=Q_n
$$



으로 생각할 수 있다.

결과적으로 송신 신호는



$$
\boxed{ s(t)=I_n\cos(\omega_ct)-Q_n\sin(\omega_ct) }
$$



로 단순화된다.

이때 $0\le t<T_s$이다.

**참고:** 앞선 글의 Gray Constellation과 이번 수신기 유도의 Bit Labeling은 다를 수 있다. QPSK는 Bit-to-Symbol Mapping을 여러 방식으로 정의할 수 있으며, 이번 글의 Threshold 예제는 각 Branch에 직접 `0 → -1`, `1 → +1`을 적용한 경우이다. 실제 복원에서는 송신기의 Gray Mapping에 맞는 역변환도 수행해야 한다.

---



### 2. QPSK 수신기 구조의 네 가지 분류

QPSK Receiver는 다음 두 기준으로 분류할 수 있다.

첫 번째는 **어느 주파수 영역에서 검출하는가**이다.

- Bandpass: Carrier Frequency 영역에서 직접 처리
- Baseband: Downconversion 후 낮은 주파수 영역에서 처리

두 번째는 **어떤 방법으로 원하는 신호 성분을 추출하는가**이다.

- Correlator: Reference Signal과 곱한 뒤 적분
- Matched Filter: Reference Waveform에 정합된 Filter를 사용한 뒤 Sampling

이를 조합하면 다음 네 가지 방식이 만들어진다.

|  | Correlator | Matched Filter |
| --- | --- | --- |
| Bandpass | Bandpass Correlator | Bandpass Matched Filter |
| Baseband | Baseband Correlator | Baseband Matched Filter |

전체 구조는 다음과 같다.



```text
QPSK Receiver
       │
       ├── Correlator
       │      ├── Bandpass
       │      └── Baseband
       │
       └── Matched Filter
              ├── Bandpass
              └── Baseband
```



네 방식은 구조와 연산 순서가 다르지만, 강의에서 가정한 이상적인 조건에서는 모두 송신한 I/Q Symbol을 복원할 수 있다.

---



### 3. QPSK 복조의 핵심: Orthogonality

수신기에서 I와 Q를 구분할 수 있는 이유는 Cosine과 Sine의 Orthogonality 때문이다.

다음 두 Carrier를 생각하자.



$$
c_I(t)=\cos(\omega_ct)
$$





$$
c_Q(t)=-\sin(\omega_ct)
$$



이번 글에서는 Q Branch의 Reference를 $-\sin(\omega_ct)$로 정의한다.

이는 송신 신호를



$$
s(t)=I(t)\cos(\omega_ct)-Q(t)\sin(\omega_ct)
$$



로 표현했기 때문이다.

두 Carrier가 적절한 Symbol Interval에서 직교하면 다음 관계가 성립한다.



$$
\boxed{ \int_0^{T_s} \cos(\omega_ct)\sin(\omega_ct)\,dt=0 }
$$



또한



$$
\boxed{ \int_0^{T_s} \cos^2(\omega_ct)\,dt=\frac{T_s}{2} }
$$





$$
\boxed{ \int_0^{T_s} \sin^2(\omega_ct)\,dt=\frac{T_s}{2} }
$$



가 된다.

이번 강의에서는 이를 만족하는 간단한 조건으로 Symbol Duration이 Carrier Period의 정수배인 경우를 사용한다.



$$
\boxed{ T_s=nT_c }
$$



여기서



$$
T_c=\frac1{f_c}
$$



이며 $n$은 양의 정수이다.

즉



$$
\boxed{ f_cT_s=n }
$$



이다.

이 조건에서는 Symbol Interval에 Carrier Cycle이 정수 개 포함된다.

따라서 원하는 I 또는 Q 성분에 대응하는 적분값은 남고, 서로 직교하는 Cross Term은 0이 된다.

이것이 오늘 다룬 네 가지 Receiver의 공통적인 수학적 기반이다.

---



## Part 1. Correlator를 이용한 QPSK 복조

### 4. Bandpass Demodulation with Correlator

Bandpass Correlator는 수신된 Bandpass Signal을 Carrier Frequency 영역에서 직접 처리하는 방법이다.

전체 구조는 다음과 같다.



```text
                    Received Signal s(t)
                             │
                  ┌──────────┴──────────┐
                  │                     │
                  ▼                     ▼
              × cos(ωct)            × -sin(ωct)
                  │                     │
                  ▼                     ▼
              Integrator            Integrator
               0 → Ts                0 → Ts
                  │                     │
                  ▼                     ▼
               zI                  zQ
                  │                     │
                  ▼                     ▼
           Decision(0)           Decision(0)
                  │                     │
                  ▼                     ▼
              Recovered I         Recovered Q
                  │                     │
                  └──────────┬──────────┘
                             │
                             ▼
                    Parallel-to-Serial
                             │
                             ▼
                      Recovered Bits
```



Correlator는 수신 신호와 Reference Signal을 곱하고 적분하여, 두 신호의 관련성을 나타내는 값을 구한다.

이번 강의에서는 Noise와 Synchronization Error가 없는 이상적인 동기 수신을 가정한다.

---



### 4.1 I Component 검출

수신 신호를 다음과 같이 정의한다.



$$
s(t)=I_n\cos(\omega_ct)-Q_n\sin(\omega_ct)
$$



I Branch에서는 수신 신호에 Cosine을 곱한다.



$$
\boxed{ y_I(t)=s(t)\cos(\omega_ct) }
$$



이를 전개하면



$$
y_I(t) = I_n\cos^2(\omega_ct) - Q_n\sin(\omega_ct)\cos(\omega_ct)
$$



이다.

이제 $0$부터 $T_s$까지 적분한다.



$$
z_I= \int_0^{T_s}y_I(t)\,dt
$$



따라서



$$
z_I= I_n\int_0^{T_s}\cos^2(\omega_ct)\,dt - Q_n\int_0^{T_s}\sin(\omega_ct)\cos(\omega_ct)\,dt
$$



이다.

여기서 삼각함수 항등식을 이용한다.



$$
\boxed{ \cos^2x=\frac{1+\cos(2x)}2 }
$$





$$
\boxed{ \sin x\cos x=\frac{\sin(2x)}2 }
$$



대입하면



$$
z_I= \frac{I_n}{2} \int_0^{T_s}[1+\cos(2\omega_ct)]\,dt - \frac{Q_n}{2} \int_0^{T_s}\sin(2\omega_ct)\,dt
$$



이다.

이제 각각의 적분을 계산한다.

먼저



$$
\int_0^{T_s}1\,dt=T_s
$$



이다.

두 번째 항은



$$
\int_0^{T_s}\cos(2\omega_ct)\,dt = \left[ \frac{\sin(2\omega_ct)}{2\omega_c} \right]_0^{T_s}
$$



이다.

$T_s=nT_c$이고 $\omega_c=2\pi/T_c$이므로



$$
2\omega_cT_s=4\pi n
$$



이다.

따라서



$$
\sin(4\pi n)=0
$$



이므로 해당 적분은 0이 된다.

마찬가지로



$$
\int_0^{T_s}\sin(2\omega_ct)\,dt=0
$$



이다.

결과적으로 남는 것은



$$
z_I=\frac{I_n}{2}T_s
$$



이다.

따라서



$$
\boxed{ z_I=\frac{I_nT_s}{2} }
$$



를 얻는다.

**I Correlator의 출력은 Q Component와 무관하며, I Symbol에 비례한다.**

---



### 4.2 Q Component 검출

Q Branch에서는 $-\sin(\omega_ct)$를 곱한다.



$$
\boxed{ y_Q(t)=-s(t)\sin(\omega_ct) }
$$



수신 신호를 대입하면



$$
y_Q(t) = -I_n\cos(\omega_ct)\sin(\omega_ct) + Q_n\sin^2(\omega_ct)
$$



이다.

이번에는 다음 항등식을 이용한다.



$$
\boxed{ \sin^2x=\frac{1-\cos(2x)}2 }
$$



따라서



$$
z_Q= \int_0^{T_s}y_Q(t)\,dt
$$





$$
= -\frac{I_n}{2} \int_0^{T_s}\sin(2\omega_ct)\,dt + \frac{Q_n}{2} \int_0^{T_s}[1-\cos(2\omega_ct)]\,dt
$$



이다.

앞서 설명한 Orthogonality에 의해 두 배 주파수 항의 적분은 0이 된다.

결과적으로



$$
z_Q=\frac{Q_n}{2}T_s
$$



이다.

따라서



$$
\boxed{ z_Q=\frac{Q_nT_s}{2} }
$$



를 얻는다.

**Q Correlator에서는 I Component가 제거되고 Q Symbol에 비례하는 값만 남는다.**

---



### 4.3 Threshold Decision

Correlator를 통과한 후에는 I와 Q 각각에 대해 Decision을 수행한다.

이번 강의에서는 Threshold를 0으로 설정한다.



$$
\boxed{ \hat b= \begin{cases} 1,&z>0\\ 0,&z<0 \end{cases} }
$$



정확히 0인 경우에는 별도의 Tie-breaking Rule이 필요하지만, 이상적인 $\pm1$ Symbol에서는 발생하지 않는다.

앞에서 구한 식은



$$
z_I=\frac{I_nT_s}{2}
$$





$$
z_Q=\frac{Q_nT_s}{2}
$$



이다.

$T_s>0$이므로 출력의 부호는 각각 $I_n$, $Q_n$의 부호와 같다.

| 전송 Bit | Polar Symbol | Correlator Output | Decision |
| --- | --- | --- | --- |
| 0 | -1 | $-T_s/2$ | 0 |
| 1 | +1 | $+T_s/2$ | 1 |

예를 들어 I/Q Branch에 다음 Bit가 입력되었다고 하자.



$$
(b_I,b_Q)=(0,1)
$$



Polar Encoding을 거치면



$$
I_n=-1,\qquad Q_n=+1
$$



이다.

따라서



$$
z_I=-\frac{T_s}{2}<0
$$





$$
z_Q=+\frac{T_s}{2}>0
$$



이므로 최종 출력은



$$
\boxed{ (\hat b_I,\hat b_Q)=(0,1) }
$$



이다.

이처럼 Correlator는 Orthogonal Carrier의 특성을 이용하여 두 정보를 분리하고 복원한다.

---



### 5. Baseband Demodulation with Correlator

Bandpass Correlator는 Carrier Frequency 영역에서 직접 적분한다.

반면 Baseband Correlator는 먼저 수신 신호를 낮은 주파수 영역으로 Downconversion한다.

이를 위해 Local Oscillator와 Low-Pass Filter(LPF)를 사용한다.



```text
                  Received Signal s(t)
                           │
                  ┌────────┴────────┐
                  │                 │
                  ▼                 ▼
              × cos(ωct)        × -sin(ωct)
                  │                 │
                  ▼                 ▼
                 LPF               LPF
                  │                 │
                  ▼                 ▼
               I(t)/2            Q(t)/2
                  │                 │
                  ▼                 ▼
                × p(t)           × p(t)
                  │                 │
                  ▼                 ▼
              Integrator        Integrator
                  │                 │
                  ▼                 ▼
                zI                zQ
                  │                 │
                  ▼                 ▼
             Decision           Decision
                  │                 │
                  └────────┬────────┘
                           │
                           ▼
                   Recovered Bits
```



이 구조에서 가장 중요한 요소는 LPF이다.

Carrier를 곱한 뒤 발생하는 고주파 성분을 제거하고 Baseband Component만 남긴다.

---



### 5.1 I Branch의 Downconversion

수신 신호는



$$
s(t)=I(t)\cos(\omega_ct)-Q(t)\sin(\omega_ct)
$$



이다.

I Branch에서 Cosine을 곱하면



$$
y_I(t)=s(t)\cos(\omega_ct)
$$



이다.

전개하면



$$
y_I(t) = I(t)\cos^2(\omega_ct) - Q(t)\sin(\omega_ct)\cos(\omega_ct)
$$



이다.

삼각함수 항등식을 이용하면



$$
\boxed{ y_I(t) = \frac{I(t)}2 + \frac{I(t)}2\cos(2\omega_ct) - \frac{Q(t)}2\sin(2\omega_ct) }
$$



를 얻는다.

이 식에는 세 가지 성분이 있다.

| 성분 | 의미 |
| --- | --- |
| $I(t)/2$ | Baseband Component |
| $I(t)\cos(2\omega_ct)/2$ | 고주파 성분 |
| $-Q(t)\sin(2\omega_ct)/2$ | 고주파 성분 |

LPF를 적용하면 이상적인 주파수 분리 조건에서 두 고주파 성분이 제거된다.

따라서



$$
\boxed{ y_{I,\mathrm{LPF}}(t)=\frac{I(t)}2 }
$$



가 된다.

이번 강의의 Pulse Model을 적용하면



$$
I(t)=I_np(t)
$$



이므로



$$
\boxed{ y_{I,\mathrm{LPF}}(t)=\frac{I_n}{2}p(t) }
$$



이다.

---



### 5.2 I Branch의 Correlator 출력

LPF에서 얻은 Baseband Signal에 Pulse Shaping Function $p(t)$를 곱한 후 적분한다.



$$
z_I= \int_0^{T_s} y_{I,\mathrm{LPF}}(t)p(t)\,dt
$$



여기에



$$
y_{I,\mathrm{LPF}}(t)=\frac{I_n}{2}p(t)
$$



를 대입하면



$$
z_I= \frac{I_n}{2} \int_0^{T_s}p^2(t)\,dt
$$



이다.

Rectangular Pulse에서는



$$
p(t)=1,\quad0\le t<T_s
$$



이므로



$$
\int_0^{T_s}p^2(t)\,dt=T_s
$$



이다.

따라서



$$
\boxed{ z_I=\frac{I_nT_s}{2} }
$$



이다.

이는 앞서 구한 Bandpass Correlator의 출력과 동일하다.

---



### 5.3 Q Branch의 Downconversion

Q Branch에서는 $-\sin(\omega_ct)$를 곱한다.



$$
y_Q(t)=-s(t)\sin(\omega_ct)
$$



전개하면



$$
y_Q(t) = -I(t)\cos(\omega_ct)\sin(\omega_ct) + Q(t)\sin^2(\omega_ct)
$$



이다.

항등식을 적용하면



$$
\boxed{ y_Q(t) = -\frac{I(t)}2\sin(2\omega_ct) + \frac{Q(t)}2 - \frac{Q(t)}2\cos(2\omega_ct) }
$$



가 된다.

LPF를 적용하면 두 배 Carrier Frequency 주변의 성분이 제거된다.

따라서



$$
\boxed{ y_{Q,\mathrm{LPF}}(t)=\frac{Q(t)}2 }
$$



이다.

Pulse Model에 따라



$$
Q(t)=Q_np(t)
$$



이므로



$$
y_{Q,\mathrm{LPF}}(t)=\frac{Q_n}{2}p(t)
$$



이다.

Correlator를 적용하면



$$
z_Q= \int_0^{T_s} y_{Q,\mathrm{LPF}}(t)p(t)\,dt
$$





$$
= \frac{Q_n}{2} \int_0^{T_s}p^2(t)\,dt
$$



따라서



$$
\boxed{ z_Q=\frac{Q_nT_s}{2} }
$$



이다.

결과적으로 Baseband Correlator도 Bandpass Correlator와 동일한 판정값을 얻는다.

---



## Part 2. Matched Filter를 이용한 QPSK 복조

### 6. Matched Filter란?

Matched Filter(정합 필터)는 특정 Reference Signal의 형태에 맞게 설계된 Filter이다.

Correlator는 Reference Signal과 수신 신호를 직접 곱하고 적분한다.

Matched Filter는 Reference Signal을 시간 반전(Time Reversal) 및 지연시킨 Impulse Response를 사용하여 Filtering을 수행한다.

실수 신호 $g(t)$에 대해 일반적인 Matched Filter의 Impulse Response를 다음과 같이 나타낼 수 있다.



$$
\boxed{ h(t)=g(T_s-t) }
$$



여기서 $T_s$는 원하는 Sampling Time이다.

실수 신호를 기준으로 설명하고 있으며, 복소 신호에서는 Complex Conjugate도 고려해야 한다.

Matched Filter의 출력은 Convolution으로 표현된다.



$$
\boxed{ y(t)=s(t)*h(t) }
$$



즉



$$
y(t)= \int_{-\infty}^{\infty} s(\tau)h(t-\tau)\,d\tau
$$



이다.

이후 특정 Sampling Time에서 출력을 추출한다.

중요한 점은 **적절하게 설계한 Matched Filter의 Sampling Output이 Correlator의 출력과 동일한 형태를 가진다**는 것이다.

---



### 7. Correlator와 Matched Filter의 수학적 관계

Matched Filter의 Impulse Response를



$$
h(t)=g(T_s-t)
$$



로 설정하자.

출력은 다음과 같다.



$$
y(t)= \int_{-\infty}^{\infty} s(\tau)h(t-\tau)\,d\tau
$$



이제 Sampling Time을 $T_s$로 설정한다.



$$
y(T_s)= \int_{-\infty}^{\infty} s(\tau)h(T_s-\tau)\,d\tau
$$



여기에서



$$
h(T_s-\tau) = g[T_s-(T_s-\tau)]
$$



이므로



$$
\boxed{ h(T_s-\tau)=g(\tau) }
$$



이다.

따라서



$$
y(T_s)= \int_{-\infty}^{\infty} s(\tau)g(\tau)\,d\tau
$$



가 된다.

Reference Signal이 $0\le t<T_s$에서만 존재한다면



$$
\boxed{ y(T_s)= \int_0^{T_s}s(\tau)g(\tau)\,d\tau }
$$



이다.

이 식은 바로 Correlator에서 사용한 적분과 같은 형태이다.

즉



$$
\boxed{ \text{Matched Filter Output at }T_s = \text{Correlator Output} }
$$



이다.

여기서는 동일한 Reference Waveform과 Normalization을 사용한 이상적인 경우를 가정하였다.

---



### 8. Bandpass Demodulation with Matched Filter

Bandpass Matched Filter는 Bandpass Signal을 Baseband로 Downconversion하기 전에 Carrier Frequency 영역에서 직접 Filtering한다.

전체 구조는 다음과 같다.



```text
                 Received Signal s(t)
                          │
                 ┌────────┴────────┐
                 │                 │
                 ▼                 ▼
          Matched Filter I   Matched Filter Q
              hI(t)              hQ(t)
                 │                 │
                 ▼                 ▼
          Sample at Ts       Sample at Ts
                 │                 │
                 ▼                 ▼
                zI                zQ
                 │                 │
                 ▼                 ▼
             Decision           Decision
                 │                 │
                 └────────┬────────┘
                          │
                          ▼
                  Recovered Bits
```



이번 강의에서는 I와 Q의 Reference Signal을 각각 다음과 같이 사용한다.



$$
g_I(t)=p(t)\cos(\omega_ct)
$$





$$
g_Q(t)=-p(t)\sin(\omega_ct)
$$



따라서 Matched Filter의 Impulse Response는



$$
\boxed{ h_I(t)=g_I(T_s-t) }
$$





$$
\boxed{ h_Q(t)=g_Q(T_s-t) }
$$



이다.

Rectangular Pulse를 가정하면 유효 구간에서 다음과 같이 나타낼 수 있다.



$$
\boxed{ h_I(t)=\cos[\omega_c(T_s-t)] }
$$





$$
\boxed{ h_Q(t)=-\sin[\omega_c(T_s-t)] }
$$



단, 두 Filter 모두 해당 Symbol에 대응하는 시간 구간으로 제한된 Waveform을 사용한다.

---



### 8.1 I Branch의 Matched Filter 출력

I Branch의 출력을 다음과 같이 정의한다.



$$
y_I(t)=s(t)*h_I(t)
$$



Convolution을 적분으로 표현하면



$$
y_I(t)= \int_{-\infty}^{\infty} s(\tau)h_I(t-\tau)\,d\tau
$$



이다.

이제 $t=T_s$에서 Sampling한다.



$$
z_I=y_I(T_s)
$$



따라서



$$
z_I= \int_{-\infty}^{\infty} s(\tau)h_I(T_s-\tau)\,d\tau
$$



이다.

Matched Filter의 정의에 의해



$$
h_I(T_s-\tau)=g_I(\tau)
$$



이므로



$$
z_I= \int_0^{T_s} s(\tau)\cos(\omega_c\tau)\,d\tau
$$



이다.

이는 Bandpass Correlator에서 계산한 식과 동일하다.

따라서



$$
\boxed{ z_I=\frac{I_nT_s}{2} }
$$



이다.

---



### 8.2 Q Branch의 Matched Filter 출력

Q Branch도 같은 방식으로 계산할 수 있다.



$$
y_Q(t)=s(t)*h_Q(t)
$$



Sampling Time에서



$$
z_Q=y_Q(T_s)
$$



이다.

Convolution을 이용하면



$$
z_Q= \int_{-\infty}^{\infty} s(\tau)h_Q(T_s-\tau)\,d\tau
$$



이다.

여기서



$$
h_Q(T_s-\tau)=g_Q(\tau)
$$



이며



$$
g_Q(\tau)=-p(\tau)\sin(\omega_c\tau)
$$



이다.

따라서



$$
z_Q= -\int_0^{T_s} s(\tau)\sin(\omega_c\tau)\,d\tau
$$



이다.

이는 Q Correlator의 적분과 같다.

결과적으로



$$
\boxed{ z_Q=\frac{Q_nT_s}{2} }
$$



이다.

따라서 Bandpass Matched Filter 역시 I/Q 정보를 복원할 수 있다.

---



### 9. Baseband Demodulation with Matched Filter

마지막 방식은 Baseband Matched Filter이다.

먼저 수신 신호를 Cosine과 Sine Reference Signal을 이용하여 Downconversion한다.

이후 LPF를 이용하여 고주파 성분을 제거하고, Baseband에서 Matched Filtering을 수행한다.



```text
                 Received Signal s(t)
                          │
                 ┌────────┴────────┐
                 │                 │
                 ▼                 ▼
             × cos(ωct)        × -sin(ωct)
                 │                 │
                 ▼                 ▼
                LPF               LPF
                 │                 │
                 ▼                 ▼
               I(t)/2            Q(t)/2
                 │                 │
                 ▼                 ▼
          Matched Filter     Matched Filter
           hBB(t)             hBB(t)
                 │                 │
                 ▼                 ▼
            Sample at Ts      Sample at Ts
                 │                 │
                 ▼                 ▼
                zI                zQ
                 │                 │
                 ▼                 ▼
             Decision           Decision
                 │                 │
                 └────────┬────────┘
                          │
                          ▼
                  Recovered Bits
```



이 방식에서는 Carrier Waveform 대신 **Baseband Pulse Shape에 정합된 Filter**를 사용한다.

---



### 9.1 Baseband Matched Filter의 Impulse Response

송신기에서 사용한 Pulse Shaping Function이 $p(t)$라면, 실수 Pulse에 대한 Matched Filter는 다음과 같이 정의할 수 있다.



$$
\boxed{ h_{BB}(t)=p(T_s-t) }
$$



즉 Pulse를 시간 반전시킨 뒤 $T_s$만큼 이동한 형태이다.

Baseband Correlator에서는 직접 $p(t)$를 곱하고 적분했다.

Baseband Matched Filter에서는 $p(T_s-t)$를 Impulse Response로 가지는 Filter를 통과시킨 뒤 Sampling한다.

이 두 연산은 Sampling Time에서 동일한 적분 형태로 연결된다.

---



### 9.2 I Branch 출력 유도

앞에서 Downconversion과 LPF를 거친 I Component는 다음과 같았다.



$$
\boxed{ y_{I,\mathrm{LPF}}(t)=\frac{I_n}{2}p(t) }
$$



이를 Matched Filter에 입력한다.



$$
y_{I,\mathrm{MF}}(t) = y_{I,\mathrm{LPF}}(t)*h_{BB}(t)
$$



Convolution을 전개하면



$$
y_{I,\mathrm{MF}}(t) = \int_{-\infty}^{\infty} \frac{I_n}{2}p(\tau) h_{BB}(t-\tau)\,d\tau
$$



이다.

$t=T_s$에서 Sampling하면



$$
z_I= \frac{I_n}{2} \int_{-\infty}^{\infty} p(\tau)h_{BB}(T_s-\tau)\,d\tau
$$



이다.

Matched Filter의 정의에 의해



$$
h_{BB}(T_s-\tau)=p(\tau)
$$



이므로



$$
z_I= \frac{I_n}{2} \int_{-\infty}^{\infty}p^2(\tau)\,d\tau
$$



이다.

이번 강의에서 사용한 Rectangular Pulse는 $0\le t<T_s$에서만 존재하므로



$$
z_I= \frac{I_n}{2} \int_0^{T_s}p^2(\tau)\,d\tau
$$



이다.

또한 이 구간에서 $p(t)=1$이므로



$$
\boxed{ z_I=\frac{I_nT_s}{2} }
$$



이다.

이는 Baseband Correlator에서 얻은 값과 동일하다.

---



### 9.3 Q Branch 출력 유도

Q Branch의 LPF Output은 다음과 같다.



$$
\boxed{ y_{Q,\mathrm{LPF}}(t)=\frac{Q_n}{2}p(t) }
$$



이를 Matched Filter에 입력하면



$$
y_{Q,\mathrm{MF}}(t) = y_{Q,\mathrm{LPF}}(t)*h_{BB}(t)
$$



이다.

$t=T_s$에서 Sampling하면



$$
z_Q= \frac{Q_n}{2} \int_0^{T_s}p^2(\tau)\,d\tau
$$



이다.

Rectangular Pulse에서는



$$
\int_0^{T_s}p^2(\tau)\,d\tau=T_s
$$



이므로



$$
\boxed{ z_Q=\frac{Q_nT_s}{2} }
$$



이다.

따라서 Baseband Matched Filter에서도 동일한 I/Q Symbol을 복원할 수 있다.

---



## Part 3. 네 가지 QPSK Receiver 비교

### 10. 네 가지 복조 방식의 결과 비교

지금까지 살펴본 네 가지 수신기의 출력을 정리하면 다음과 같다.

| Receiver Structure | I Output | Q Output |
| --- | --- | --- |
| Bandpass Correlator | $I_nT_s/2$ | $Q_nT_s/2$ |
| Baseband Correlator | $I_nT_s/2$ | $Q_nT_s/2$ |
| Bandpass Matched Filter | $I_nT_s/2$ | $Q_nT_s/2$ |
| Baseband Matched Filter | $I_nT_s/2$ | $Q_nT_s/2$ |

따라서 이번 강의의 이상적인 Signal Model과 동일한 Reference Scaling을 사용하면



$$
\boxed{ z_I=\frac{I_nT_s}{2} }
$$





$$
\boxed{ z_Q=\frac{Q_nT_s}{2} }
$$



라는 동일한 결과를 얻는다.

네 가지 Receiver는 다음과 같은 공통점을 가진다.

- 동일한 QPSK 송신 신호를 입력받는다.
- I와 Q의 Orthogonality를 이용한다.
- I/Q Component에 대응하는 Decision Statistic을 추출한다.
- Threshold Decision을 이용하여 Symbol을 판정한다.
- 마지막으로 Parallel-to-Serial Conversion을 수행한다.

하지만 사용하는 구조는 다르다.

---



### 10.1 Correlator와 Matched Filter 비교

| 항목 | Correlator | Matched Filter |
| --- | --- | --- |
| 기본 연산 | 곱셈과 적분 | Convolution과 Sampling |
| Reference | 기준 Signal | 정합된 Impulse Response |
| 출력 추출 | 적분 종료 시점 | Sampling Time |
| 수학적 관계 | Inner Product | Sampling 시 동일한 Inner Product |
| 주요 목적 | Signal Component 검출 | Reference Waveform에 대한 검출 |

Matched Filter는 AWGN(Additive White Gaussian Noise) 환경에서 알려진 신호의 Sampling SNR을 최대화하는 수신 필터로도 설명할 수 있다.

이는 이번 강의의 Correlator와 Matched Filter 동등성을 이해하는 데 도움이 되는 보충 개념이다.

---



### 10.2 Bandpass와 Baseband 비교

| 항목 | Bandpass | Baseband |
| --- | --- | --- |
| 처리 위치 | Carrier Frequency 영역 | Downconversion 이후 |
| LPF | 구조에 따라 불필요 | Downconversion 후 사용 |
| Reference | Carrier Waveform | Baseband Pulse Waveform |
| 대표 연산 | Carrier와 직접 상관/정합 | I/Q 분리 후 상관/정합 |
| 최종 목적 | I/Q Symbol 검출 | I/Q Symbol 검출 |

이론적으로는 네 방식 모두 같은 정보를 추출할 수 있다.

실제 구현에서는 Analog Front-End의 구조, Frequency Conversion, Sampling Rate, Filter 설계 등의 조건을 고려해야 한다.

따라서 Bandpass 방식과 Baseband 방식 중 어느 하나가 항상 더 우수하다고 단정할 수는 없다.

---



### 11. 왜 출력에 $T_s/2$가 나타나는가?

이번 강의에서 네 수신기의 최종 출력에는 공통적으로 다음 값이 등장한다.



$$
\boxed{ \frac{T_s}{2} }
$$



이 값은 두 가지 요소와 관련이 있다.

첫째, Cosine과 Sine의 제곱을 적분하면 다음과 같다.



$$
\int_0^{T_s}\cos^2(\omega_ct)\,dt=\frac{T_s}{2}
$$





$$
\int_0^{T_s}\sin^2(\omega_ct)\,dt=\frac{T_s}{2}
$$



둘째, Baseband Demodulation에서는 Carrier Multiplication 이후 LPF를 통과하면 다음과 같이 절반 크기의 Component가 남는다.



$$
\boxed{ y_{I,\mathrm{LPF}}(t)=\frac{I(t)}2 }
$$





$$
\boxed{ y_{Q,\mathrm{LPF}}(t)=\frac{Q(t)}2 }
$$



이후 Rectangular Pulse의 적분 구간 길이가 $T_s$이므로 같은 결과를 얻는다.

다만 $T_s/2$는 **이번 강의에서 사용한 Carrier Amplitude와 Reference Scaling에 따른 값**이다.

Reference Signal을 정규화하거나 수신기의 Gain을 변경하면 출력 계수는 달라질 수 있다.

실제 Symbol Decision에서 중요한 것은 올바른 Scaling과 Threshold를 사용하여 I/Q Component를 판정하는 것이다.

---



## Part 4. BPSK와 QPSK의 대역폭 효율 비교

### 12. Bit Rate와 Symbol Rate

QPSK의 대역폭 효율을 이해하려면 먼저 Bit Rate와 Symbol Rate를 구분해야 한다.

**Bit Rate($R_b$)**

초당 전송하는 Bit의 개수이다.

**Symbol Rate($R_s$)**

초당 전송하는 Symbol의 개수이다.

하나의 Symbol에 $k$개의 Bit가 포함된다면



$$
\boxed{ R_b=kR_s }
$$



이다.

또한



$$
\boxed{ k=\log_2M }
$$



이다.

따라서



$$
\boxed{ R_s=\frac{R_b}{\log_2M} }
$$



이다.

BPSK는 $M=2$이므로



$$
\boxed{ R_{s,\mathrm{BPSK}}=R_{b,\mathrm{BPSK}} }
$$



이다.

QPSK는 $M=4$이므로



$$
\boxed{ R_{s,\mathrm{QPSK}}=\frac{R_{b,\mathrm{QPSK}}}{2} }
$$



이다.

이 두 식을 이용하면 BPSK와 QPSK의 대역폭 관계를 비교할 수 있다.

---



### 13. Symbol Rate와 Bandwidth의 관계

디지털 변조에서 필요한 Bandwidth는 Symbol Rate와 Pulse Shaping에 영향을 받는다.

동일한 Pulse Shaping과 Bandwidth 정의를 적용한다면 다음과 같은 비례관계로 비교할 수 있다.



$$
\boxed{ B\propto R_s }
$$



예를 들어 동일한 Raised Cosine Roll-off Factor $\alpha$를 사용한다면 이상화된 Passband Bandwidth는 다음과 같이 나타낼 수 있다.



$$
\boxed{ B=(1+\alpha)R_s }
$$



따라서 같은 Pulse Shaping 조건에서는 Symbol Rate가 감소하면 필요한 Bandwidth도 그에 비례하여 감소한다.

이제 두 가지 조건으로 비교해 보자.

---



### 14. Case 1: BPSK와 QPSK의 Symbol Rate가 같은 경우

첫 번째 조건은 다음과 같다.



$$
\boxed{ R_{s,\mathrm{BPSK}} = R_{s,\mathrm{QPSK}} }
$$



두 Modulation Scheme이 같은 Symbol Rate를 가진다고 하자.

BPSK는 Symbol당 1bit를 전송하므로



$$
R_{b,\mathrm{BPSK}} = R_s
$$



이다.

반면 QPSK는 Symbol당 2bit를 전송하므로



$$
R_{b,\mathrm{QPSK}} = 2R_s
$$



이다.

따라서



$$
\boxed{ R_{b,\mathrm{QPSK}} = 2R_{b,\mathrm{BPSK}} }
$$



이다.

두 방식의 Symbol Rate가 동일하고 동일한 Pulse Shaping을 사용한다면 필요한 Bandwidth도 동일하다.



$$
\boxed{ B_{\mathrm{BPSK}} = B_{\mathrm{QPSK}} }
$$



Spectral Efficiency는 다음과 같다.



$$
\boxed{ \eta=\frac{R_b}{B} }
$$



따라서



$$
\eta_{\mathrm{BPSK}} = \frac{R_{b,\mathrm{BPSK}}}{B}
$$



이고



$$
\eta_{\mathrm{QPSK}} = \frac{2R_{b,\mathrm{BPSK}}}{B}
$$



이다.

결과적으로



$$
\boxed{ \eta_{\mathrm{QPSK}} = 2\eta_{\mathrm{BPSK}} }
$$



이다.

#### Case 1 결론

> Symbol Rate와 Bandwidth가 동일한 조건이라면 QPSK는 BPSK보다 두 배의 Bit Rate를 제공한다.

---



### 15. Case 2: BPSK와 QPSK의 Bit Rate가 같은 경우

두 번째 조건은 다음과 같다.



$$
\boxed{ R_{b,\mathrm{BPSK}} = R_{b,\mathrm{QPSK}} }
$$



두 Modulation Scheme이 동일한 Bit Rate를 제공한다고 하자.

BPSK에서는



$$
R_{s,\mathrm{BPSK}} = R_b
$$



이다.

QPSK에서는



$$
R_{s,\mathrm{QPSK}} = \frac{R_b}{2}
$$



이다.

따라서



$$
\boxed{ R_{s,\mathrm{QPSK}} = \frac{R_{s,\mathrm{BPSK}}}{2} }
$$



이다.

즉 같은 Bit Rate를 전송할 때 QPSK는 BPSK보다 절반의 Symbol Rate를 필요로 한다.

동일한 Pulse Shaping 조건에서 Bandwidth는 Symbol Rate에 비례하므로



$$
B_{\mathrm{QPSK}} = \frac{B_{\mathrm{BPSK}}}{2}
$$



이다.

따라서



$$
\boxed{ B_{\mathrm{QPSK}} = \frac12B_{\mathrm{BPSK}} }
$$



이다.

이제 Spectral Efficiency를 비교하면



$$
\eta_{\mathrm{BPSK}} = \frac{R_b}{B_{\mathrm{BPSK}}}
$$



이고



$$
\eta_{\mathrm{QPSK}} = \frac{R_b}{B_{\mathrm{BPSK}}/2}
$$



이다.

따라서



$$
\boxed{ \eta_{\mathrm{QPSK}} = 2\eta_{\mathrm{BPSK}} }
$$



이다.

#### Case 2 결론

> Bit Rate가 동일한 조건이라면 QPSK는 BPSK에 비해 약 절반의 Bandwidth만 필요하다.

---



### 16. 두 가지 조건의 최종 비교

| 비교 항목 | Case 1 | Case 2 |
| --- | --- | --- |
| 고정 조건 | Symbol Rate | Bit Rate |
| QPSK Symbol Rate | BPSK와 같음 | BPSK의 1/2 |
| QPSK Bit Rate | BPSK의 2배 | BPSK와 같음 |
| QPSK Bandwidth | BPSK와 같음 | BPSK의 1/2 |
| Spectral Efficiency | BPSK의 2배 | BPSK의 2배 |

두 경우 모두 QPSK의 Spectral Efficiency는 BPSK보다 두 배이다.

하지만 해석은 다르다.

**Case 1**

같은 Bandwidth에서 더 많은 Bit를 전송한다.

**Case 2**

같은 양의 Bit를 전송하면서 필요한 Bandwidth를 줄인다.

따라서 QPSK의 장점은 단순히 전송 속도가 빠르다는 설명보다 **Symbol당 포함하는 Bit 수가 증가하여 Bandwidth를 효율적으로 이용할 수 있다는 것**으로 이해하는 편이 정확하다.

---



## Part 5. QPSK의 위상 전이와 OQPSK

### 17. QPSK에서 가능한 Phase Transition

QPSK는 네 가지 Phase State를 사용한다.



$$
\boxed{ \theta\in \left\{ \frac{\pi}{4}, \frac{3\pi}{4}, -\frac{3\pi}{4}, -\frac{\pi}{4} \right\} }
$$



Symbol이 변경되면 Constellation Diagram에서 한 점이 다른 점으로 이동하게 된다.

예를 들어 다음과 같은 Constellation을 생각하자.



```text
                    Q
                    ↑
                    │
             ●      │      ●
             │      │      │
             │      │      │
─────────────┼──────┼──────┼────────→ I
             │      │      │
             │      │      │
             ●      │      ●
                    │
```



QPSK에서는 연속된 두 Symbol 사이에서 I와 Q가 동시에 변경될 수 있다.

따라서 가능한 Phase Transition은 다음과 같다.

- $0^\circ$
- $\pm90^\circ$
- $180^\circ$

특히 $180^\circ$ Phase Transition이 중요하다.

---



### 18. 180° Phase Transition의 문제

예를 들어 다음 두 Symbol을 생각하자.



$$
(I,Q)=(+1,+1)
$$



에서



$$
(I,Q)=(-1,-1)
$$



로 변경되는 경우이다.

두 Component가 동시에 부호를 바꾼다.

Constellation Diagram에서는 서로 대각선 방향에 있는 Symbol로 이동한다.



```text
                    Q
                    ↑
                    │
           ●        │        ● Start
             ╲      │      ╱
               ╲    │    ╱
─────────────────╲──┼──╱────────────→ I
                   ╳
                 ╱   ╲
               ╱       ╲
           ● End         ●
                    │
```



실제 Signal Transition의 경로는 Pulse Shaping에 따라 달라진다.

다만 I와 Q가 동시에 반대 부호로 변하면서 선형적으로 변화하는 상황에서는 Complex Envelope의 크기가 0에 가까워지는 순간이 발생할 수 있다.

Complex Envelope의 크기는 다음과 같다.



$$
\boxed{ R(t)=\sqrt{I^2(t)+Q^2(t)} }
$$



I와 Q가 동시에 0에 가까워지면



$$
R(t)\rightarrow0
$$



가 된다.

이처럼 Envelope의 크기가 크게 변하는 신호는 비선형 특성을 가진 RF Power Amplifier를 사용할 때 왜곡이 증가할 수 있다.

따라서 중요한 문제는 **QPSK가 180° Phase Transition을 허용하고, Pulse Shaping에 따라 큰 Envelope Variation이 발생할 수 있다는 것**이다.

이는 증폭기의 비선형 특성과 결합하여 통신 품질에 영향을 줄 수 있다.

---



### 19. OQPSK(Offset QPSK)란?

이러한 문제를 완화하기 위한 방식 중 하나가 **OQPSK(Offset Quadrature Phase Shift Keying)** 이다.

OQPSK의 핵심 아이디어는 단순하다.

> I Component와 Q Component가 동시에 변경되지 않도록 두 Branch의 Symbol Transition Timing을 어긋나게 만든다.

일반적인 QPSK에서는 I와 Q가 동일한 Symbol Boundary에서 변경될 수 있다.

반면 OQPSK에서는 두 Branch 사이에 시간 Offset을 적용한다.

일반적으로 다음과 같이 설명할 수 있다.



$$
\boxed{ \text{Offset}=\frac{T_s}{2} }
$$



여기서 $T_s$는 QPSK Symbol Duration이며, 각각의 I/Q Branch Symbol Interval에 해당한다.

즉 한쪽 Branch의 Transition Timing을 다른 Branch보다 절반의 Symbol Duration만큼 지연시킨다.

---



### 19.1 QPSK와 OQPSK의 Transition 비교

#### 일반 QPSK

I와 Q가 동시에 변경될 수 있다.



```text
Time ──────────────────────────→

I : +1 ──────┐      ┌────────
             └──────┘

Q : +1 ──────┐      ┌────────
             └──────┘

          Simultaneous Transition
```



#### OQPSK

I와 Q의 Transition Timing을 분리한다.



```text
Time ──────────────────────────→

I : +1 ──────┐──────────────
             └──────────────

Q : +1 ────────────┐────────
                   └────────

           Time Offset = Ts/2
```



따라서 이상적인 Symbol Transition을 기준으로 두 Component가 동시에 반전되는 상황을 방지할 수 있다.

그 결과 일반적인 QPSK의 180° Phase Transition 대신, 한 번에 최대 90°의 Phase Transition이 발생하도록 구성할 수 있다.

---



### 19.2 OQPSK는 무엇을 개선하는가?

OQPSK는 다음과 같은 목적을 가진다.

- I/Q Component의 동시 Transition 방지
- 180° Phase Transition 억제
- 큰 Envelope Variation 완화
- 비선형 RF Power Amplifier에서 발생할 수 있는 왜곡 감소

다만 OQPSK가 모든 Envelope Variation을 제거하거나 Power Amplifier의 비선형성 자체를 없애는 것은 아니다.

정확한 Envelope Behavior는 Pulse Shaping 및 RF Hardware의 특성에 따라 달라진다.

따라서 OQPSK는 **QPSK의 Bandwidth Efficiency를 유지하면서 위상 전이 특성을 개선하기 위한 변조 방식**으로 이해할 수 있다.

이번 강의에서는 OQPSK가 필요한 배경인 Constellation Transition과 I/Q 동시 변경 문제를 소개하였다.

구체적인 OQPSK Transmitter Structure와 Timing 관계는 다음 학습 내용에서 확장할 수 있다.

---



## 20. 핵심 공식 총정리

### QPSK 송신 신호



$$
\boxed{ s(t)=I(t)\cos(\omega_ct)-Q(t)\sin(\omega_ct) }
$$



### I/Q Pulse Representation



$$
\boxed{ I(t)=I_np(t),\qquad Q(t)=Q_np(t) }
$$



### Carrier Period



$$
\boxed{ T_c=\frac1{f_c} }
$$



### 강의에서 가정한 직교 조건



$$
\boxed{ T_s=nT_c,\qquad n\in\mathbb{N} }
$$



### I Correlator Output



$$
\boxed{ z_I= \int_0^{T_s}s(t)\cos(\omega_ct)\,dt = \frac{I_nT_s}{2} }
$$



### Q Correlator Output



$$
\boxed{ z_Q= -\int_0^{T_s}s(t)\sin(\omega_ct)\,dt = \frac{Q_nT_s}{2} }
$$



### Baseband LPF Output



$$
\boxed{ y_{I,\mathrm{LPF}}(t)=\frac{I(t)}2 }
$$





$$
\boxed{ y_{Q,\mathrm{LPF}}(t)=\frac{Q(t)}2 }
$$



### Matched Filter



$$
\boxed{ h(t)=g(T_s-t) }
$$



### Matched Filter Output



$$
\boxed{ y(t)=s(t)*h(t) }
$$



### Matched Filter Sampling



$$
\boxed{ y(T_s)=\int_0^{T_s}s(t)g(t)\,dt }
$$



### Bit Rate와 Symbol Rate



$$
\boxed{ R_b=kR_s }
$$



### Bits per Symbol



$$
\boxed{ k=\log_2M }
$$



### BPSK Symbol Rate



$$
\boxed{ R_{s,\mathrm{BPSK}}=R_{b,\mathrm{BPSK}} }
$$



### QPSK Symbol Rate



$$
\boxed{ R_{s,\mathrm{QPSK}}=\frac{R_{b,\mathrm{QPSK}}}{2} }
$$



### Spectral Efficiency



$$
\boxed{ \eta=\frac{R_b}{B} }
$$



### BPSK와 QPSK 효율 비교

동일한 Pulse Shaping 조건에서



$$
\boxed{ \eta_{\mathrm{QPSK}}=2\eta_{\mathrm{BPSK}} }
$$



---



## 21. 최종 정리

QPSK Receiver의 핵심 목적은 송신된 Bandpass Signal에서 In-phase(I)와 Quadrature(Q) 정보를 각각 분리하고 원래의 Bit Stream을 복원하는 것이다.

QPSK는 두 Orthogonal Carrier를 이용하여 두 정보를 하나의 신호로 합쳐 전송한다.



$$
s(t)=I(t)\cos(\omega_ct)-Q(t)\sin(\omega_ct)
$$



수신기에서는 이러한 Orthogonality를 이용하여 원하는 Component만 추출할 수 있다.

이번 강의에서 살펴본 Receiver는 총 네 가지이다.



```text
QPSK Receiver
       │
       ├── Bandpass Correlator
       │
       ├── Baseband Correlator
       │
       ├── Bandpass Matched Filter
       │
       └── Baseband Matched Filter
```



Correlator는 수신 신호와 Reference Signal을 곱하고 적분한다.

Matched Filter는 Reference Waveform을 시간 반전 및 지연시킨 Impulse Response를 이용하여 Filtering하고 특정 시점에서 Sampling한다.

적절한 정합 조건에서는 두 방식의 Decision Statistic이 동일한 형태로 나타난다.

이번 강의에서 가정한 Rectangular Pulse와 동기 수신 조건에서는 네 수신기 모두 다음 값을 얻는다.



$$
\boxed{ z_I=\frac{I_nT_s}{2},\qquad z_Q=\frac{Q_nT_s}{2} }
$$



따라서 Threshold Decision을 이용하여 I/Q Symbol의 부호를 판정하고 원래의 Binary Data를 복원할 수 있다.

또한 QPSK는 BPSK와 달리 Symbol 하나에 2bit를 포함한다.



$$
R_{s,\mathrm{QPSK}}=\frac{R_b}{2}
$$



따라서 동일한 Pulse Shaping 조건에서 QPSK는 BPSK보다 두 배의 Spectral Efficiency를 제공한다.

하지만 일반적인 QPSK에서는 I와 Q Component가 동시에 변경되면서 180° Phase Transition이 발생할 수 있다.

이에 따라 Envelope Variation이 커질 수 있으며, 이를 완화하기 위한 방법으로 OQPSK가 사용된다.

OQPSK는 I/Q Branch의 Transition Timing을 어긋나게 하여 두 Component가 동시에 변경되는 것을 방지한다.

결과적으로 이번 강의의 내용을 다음과 같이 연결할 수 있다.



```text
QPSK Modulation
        │
        ▼
Orthogonal I/Q Carrier
        │
        ▼
Bandpass Signal Transmission
        │
        ▼
QPSK Receiver
        │
        ├── Correlator
        │     ├── Bandpass
        │     └── Baseband
        │
        └── Matched Filter
              ├── Bandpass
              └── Baseband
        │
        ▼
I/Q Symbol Recovery
        │
        ▼
Threshold Decision
        │
        ▼
Recovered Bit Stream

        +

BPSK vs QPSK
        │
        ▼
Symbol Rate / Bit Rate
        │
        ▼
Bandwidth & Spectral Efficiency

        +

QPSK Phase Transition
        │
        ▼
180° Transition Problem
        │
        ▼
OQPSK
```



**QPSK의 수신 원리는 직교성을 이용한 I/Q Signal Detection에 있으며, Correlator와 Matched Filter는 이를 구현하는 서로 다른 수학적·시스템적 접근 방법이다.**

---



### 핵심 요약

> QPSK Receiver는 Orthogonal Carrier의 성질을 이용하여 I/Q 정보를 복원한다. Correlator는 곱셈과 적분으로, Matched Filter는 Convolution과 Sampling으로 원하는 Symbol을 검출한다. Bandpass와 Baseband 구조는 처리 위치가 다르지만, 이상적인 동기 수신에서는 동일한 Decision Statistic을 얻을 수 있다. 또한 QPSK는 Symbol당 2bit를 전송하므로 BPSK보다 높은 Spectral Efficiency를 제공하며, OQPSK는 I/Q Transition Timing을 분리하여 급격한 위상 및 Envelope 변화 문제를 완화한다.

---

블록도 기준: [15. Demodulate & Sample](../../blocks/demodulation-and-sampling.md) · [전체 로드맵](../../roadmap.md)
