# Wireless Channel 완전 정리: Path Loss·Multipath·Fading·Delay Spread·Doppler부터 MIMO까지

[TOC]

Digital Communication에서는 Bit와 Symbol을 실제 전송 가능한 Waveform으로 Mapping하고, ASK·FSK·PSK·QAM과 같은 Modulation을 통해 Signal을 Channel로 전달한다.

그렇다면 실제 Channel을 통과한 Signal은 송신했을 때와 동일한 형태로 Receiver에 도착할까?

현실에서는 그렇지 않다.

Signal은 전달되는 과정에서 다음과 같은 영향을 받는다.

- Path Loss: 거리에 따른 평균 수신전력 감소
- Shadowing: 장애물에 의한 수신전력 변화
- Reflection: 반사
- Refraction: 굴절
- Diffraction: 회절
- Scattering: 산란
- Multipath: 다중경로 전파
- Doppler Effect: 상대적인 움직임에 의한 주파수 변화
- Noise: 수신 신호에 추가되는 잡음

특히 Wireless Communication에서는 하나의 Signal이 여러 경로를 거쳐 Receiver에 도착할 수 있다.

각 경로의 길이와 전달 특성이 다르기 때문에 Receiver에서 관측되는 Signal의 Amplitude와 Phase가 달라진다.

또한 Transmitter, Receiver 또는 주변 물체가 움직이면 Channel의 특성이 시간에 따라 변할 수 있다.

이러한 현상을 이해하려면 다음 개념들을 연결해서 살펴봐야 한다.



```text
Path Loss
    │
    ▼
Shadowing
    │
    ▼
Multipath
    │
    ▼
Fading
    │
    ├── Delay Spread
    │       │
    │       ▼
    │   Coherence Bandwidth
    │       │
    │       ▼
    │   Flat / Frequency-selective Fading
    │
    └── Doppler Spread
            │
            ▼
        Coherence Time
            │
            ▼
        Fast / Slow Fading
```



이 글에서는 Wireless Channel에서 발생하는 물리적 현상부터 Channel의 시간·주파수 특성, Fading Model, MIMO 및 Ray Tracing까지 하나의 흐름으로 정리한다.

---

## 1. Channel은 하나의 System이다

Communication System에서 Channel은 단순히 Signal이 지나가는 공간만을 의미하지 않는다.

System 관점에서 Channel은 입력 Signal을 받아 다른 Signal로 변환하는 하나의 System으로 볼 수 있다.



```text
Transmitted Signal
        │
        ▼
      Channel
        │
        ▼
Received Signal
```



가장 단순한 Narrowband Channel에서는 다음과 같이 모델링할 수 있다.



$$
\boxed{ y(t)=h(t)x(t)+n(t) }
$$



여기서

- $x(t)$: Transmitted Signal
- $h(t)$: Channel Coefficient
- $n(t)$: Noise
- $y(t)$: Received Signal

이다.

즉 Receiver가 받는 Signal은 원래의 $x(t)$가 아니라 **Channel Effect가 적용되고 Noise가 더해진 Signal**이다.

여기서 $h(t)$는 일반적으로 Complex-valued Coefficient로 표현할 수 있다.

따라서 Channel은 Signal의 Amplitude뿐 아니라 Phase에도 영향을 줄 수 있다.

---



### 1.1 Multipath Channel의 일반적인 표현

실제 Wireless Channel에서는 Signal이 여러 경로를 통해 서로 다른 시간에 도착한다.

따라서 단순히 하나의 Channel Coefficient를 곱하는 것으로는 모든 현상을 표현하기 어렵다.

시간에 따라 변화하는 Linear Channel은 다음과 같이 표현할 수 있다.



$$
\boxed{ y(t) = \int_{-\infty}^{\infty} h(t,\tau)x(t-\tau)\,d\tau+n(t) }
$$



여기서

- $h(t,\tau)$: Time-varying Channel Impulse Response
- $t$: Channel을 관찰하는 시간
- $\tau$: Propagation Delay

이다.

이 식은 서로 다른 Delay를 가진 Signal들이 Channel을 통해 전달되고 Receiver에서 합쳐지는 과정을 나타낸다.

따라서



$$
y(t)=h(t)x(t)+n(t)
$$



는 Flat-fading과 같은 조건에서 사용할 수 있는 단순화된 모델이고,



$$
y(t) = \int h(t,\tau)x(t-\tau)\,d\tau+n(t)
$$



는 Delay Spread까지 포함하는 보다 일반적인 Linear Channel Model이다.

---

## 2. Channel의 분류

Channel은 하나의 기준으로만 분류되는 것이 아니다.

전송 매체, 사용자 구조, Antenna 개수, 시간·주파수 특성 등에 따라 구분할 수 있다.



```text
Channel
│
├── Transmission Medium
│   ├── Guided Channel
│   └── Unguided Channel
│
├── User Structure
│   ├── Single-user
│   └── Multi-user
│
├── Antenna Configuration
│   ├── SISO
│   ├── SIMO
│   ├── MISO
│   └── MIMO
│
└── Channel Characteristics
    ├── Linear / Non-linear
    ├── Time-invariant / Time-varying
    ├── Flat / Frequency-selective
    └── Fast / Slow Fading
```



이 분류들은 서로 다른 기준이다.

예를 들어 하나의 Wireless MIMO Channel은 동시에 다음 특성을 가질 수 있다.



```text
Unguided Channel
      +
MIMO Channel
      +
Time-varying Channel
      +
Frequency-selective Channel
```



따라서 Channel을 분석할 때는 어떤 특성을 기준으로 분류하고 있는지 구분하는 것이 중요하다.

---



## 3. Guided Channel과 Unguided Channel

Transmission Medium에 따라 Channel은 Guided Channel과 Unguided Channel로 구분할 수 있다.

### 3.1 Guided Channel

Guided Channel은 Signal이 특정한 물리적 구조를 따라 전달되는 Channel이다.

대표적인 예시는 다음과 같다.

- Twisted Pair
- Coaxial Cable
- Optical Fiber
- Waveguide

예를 들어 Optical Fiber에서는 빛이 Fiber 내부의 구조에 의해 유도되며 전달된다.



```text
Transmitter
    │
    └──────── Optical Fiber ────────→ Receiver
```



즉 Signal이 전달되는 물리적인 경로가 존재한다.

---



### 3.2 Unguided Channel

Unguided Channel은 Signal이 Cable이나 Wire와 같은 물리적인 전송 경로에 구속되지 않고 공간을 통해 전달되는 Channel이다.

대표적인 예시는 다음과 같다.

- Wireless RF Channel
- Underwater Acoustic Channel
- Free-space Optical Channel

Wireless Communication에서는 Antenna에서 방사된 전자기파가 공간을 통해 전달된다.



```text
                 ↗
Transmitter ────→────→ Receiver
                 ↘
```



Signal이 전달되는 과정에서 주변의 건물, 지면, 장애물 등의 영향을 받는다.

따라서 Unguided Channel은 주변 Environment에 따라 특성이 크게 달라질 수 있다.

---



### 3.3 Indoor Channel의 Waveguide Effect

Wireless Channel은 일반적으로 Unguided Channel이지만 특정한 실내 구조에서는 Waveguide와 유사한 전파 특성이 나타날 수 있다.

예를 들어 긴 Corridor를 생각해 보자.



```text
┌──────────────────────────────┐
│                              │
│ Tx ──→ ↘ ──→ ↗ ──→ ↘ ──→ Rx │
│                              │
└──────────────────────────────┘
```



Signal이 벽이나 천장에서 반복적으로 Reflection되면서 복도 방향으로 전달될 수 있다.

따라서 Corridor의 구조에 따라 특정 방향의 Signal Propagation이 상대적으로 강해질 수 있다.

이러한 현상을 Corridor Waveguide Effect라고 부르기도 한다.

다만 일반적인 복도를 이상적인 전자기 Waveguide와 완전히 동일하게 모델링할 수 있다는 의미는 아니다.

실제 Indoor Channel은 다음 요소들의 영향을 받는다.

- Wall Material
- Ceiling Height
- Floor
- Furniture
- Obstacles
- Corridor Geometry
- Transmitter·Receiver Position

따라서 동일한 건물 내부에서도 위치에 따라 Channel Response가 달라질 수 있다.

---



## 4. Wireless Propagation에서 발생하는 물리적 현상

Wireless Signal은 공간을 이동하면서 여러 물리적 현상을 경험한다.

대표적인 현상은 다음과 같다.

| 현상 | 의미 |
| --- | --- |
| Transmission | 매질 또는 경계를 통과하는 현상 |
| Reflection | 표면에서 반사되는 현상 |
| Refraction | 매질 변화에 따라 진행 방향이 달라지는 현상 |
| Diffraction | 모서리나 장애물 주변으로 파동이 퍼지는 현상 |
| Scattering | 여러 방향으로 파동이 산란되는 현상 |
| Absorption | 매질이 전자기파 Energy의 일부를 흡수하는 현상 |
| Attenuation | Signal의 Amplitude 또는 Power가 감소하는 현상 |

이러한 현상들은 서로 독립적으로만 발생하는 것이 아니라 동시에 나타날 수 있다.

---



### 4.1 Transmission

Transmission은 Signal이 특정 매질 또는 경계를 통과하여 전달되는 현상이다.

예를 들어 Wireless Signal이 벽을 통과하는 경우를 생각할 수 있다.



```text
Incident Wave
      │
      ▼
──────────── Wall
      │
      ▼
Transmitted Wave
```



벽을 통과하는 동안 Signal Energy의 일부는 Reflection되거나 Absorption될 수 있다.

따라서 Transmission 이후의 Signal Strength가 감소할 수 있다.

---



### 4.2 Reflection

Reflection은 전자기파가 물체의 표면과 상호작용하여 방향이 바뀌는 현상이다.



```text
Incident Wave
       ↘
        ↘
─────────●────────── Surface
        ↗
       ↗
Reflected Wave
```



대표적으로 다음 구조물에서 발생할 수 있다.

- Building Wall
- Ground
- Large Metallic Surface

Reflection은 Multipath를 발생시키는 주요 원인 중 하나이다.

---



### 4.3 Refraction

Refraction은 서로 다른 전파 특성을 가진 매질 사이를 통과하면서 Wave의 진행 방향이 달라지는 현상이다.

이는 매질의 Refractive Index와 관련된다.



```text
Medium A
       \
        \
─────────●──────── Boundary
          \
           \
Medium B
```



매질에 따라 Wave의 Phase Velocity가 달라질 수 있으며, 이에 따라 진행 방향이 변한다.

---



### 4.4 Diffraction

Diffraction은 파동이 장애물의 Edge 또는 개구부 주변에서 휘어지거나 퍼지는 현상이다.



```text
Signal
   │
   ▼
   │
───●
    ↘
      ↘
        Receiver
```



이 현상 때문에 직접적인 Line-of-Sight(LOS)가 확보되지 않는 위치에도 Signal이 도달할 수 있다.

다만 Diffraction의 영향은 Frequency, Wavelength, 장애물 크기와 Geometry 등에 따라 달라진다.

---



### 4.5 Scattering

Scattering은 Signal이 작은 물체나 거친 Surface와 상호작용하면서 여러 방향으로 분산되는 현상이다.



```text
         ↑
      ↖  │  ↗
        \│/
←────────●────────→
        /│\
      ↙  │  ↘
         ↓
```



예를 들어 다음 환경에서 발생할 수 있다.

- Rough Surface
- Vegetation
- Small Obstacles
- Irregular Structures

Scattering 역시 다양한 Propagation Path를 생성한다.

---



### 4.6 Absorption과 Attenuation

Absorption은 매질이 Signal Energy의 일부를 흡수하는 현상이다.

일부 Energy가 열 등으로 변환되면서 전달되는 Signal Power가 감소할 수 있다.

한편 Attenuation은 Signal의 Amplitude 또는 Power가 감소하는 현상을 포괄적으로 나타낸다.

따라서 Absorption은 Attenuation을 발생시키는 원인 중 하나이다.



```text
Propagation
    │
    ├── Geometric Spreading
    ├── Absorption
    ├── Obstruction
    └── Other Loss Mechanisms
              │
              ▼
          Attenuation
```



---



## 5. Path Loss·Shadowing·Small-scale Fading

Wireless Signal의 수신전력이 변화하는 현상은 공간적인 변화 규모에 따라 구분할 수 있다.

대표적인 구분은 다음과 같다.



```text
Received Power Variation
│
├── Path Loss
│   └── Distance-dependent Average Loss
│
├── Shadowing
│   └── Obstacles / Environment
│
└── Small-scale Fading
    └── Multipath Interference
```



일반적인 Wireless Propagation Model에서는 Path Loss와 Shadowing을 함께 **Large-scale Propagation Effects**로 분류한다.

반면 Multipath에 의한 빠른 변화는 Small-scale Fading으로 분류한다.

설명 목적에 따라 Distance-dependent Path Loss, Shadowing, Small-scale Fading을 세 단계로 나누어 다루기도 한다.

| 구분 | 주된 원인 | 변화 특성 |
| --- | --- | --- |
| Path Loss | Distance, Propagation Geometry | 거리 증가에 따른 평균 수신전력 변화 |
| Shadowing | Buildings, Obstacles | 공간적으로 비교적 느린 변화 |
| Small-scale Fading | Multipath Interference | 짧은 거리·시간에서 빠른 변화 |

---



## 6. Path Loss

Path Loss는 Transmitter와 Receiver 사이에서 Signal이 전달되는 동안 발생하는 평균적인 Power Loss를 의미한다.

대표적인 원인은 Signal Energy가 공간으로 퍼지는 Geometric Spreading이다.



```text
Tx ●──────────────────● Rx

       Distance ↑
           │
           ▼
Average Received Power ↓
```



Free-space 환경에서는 거리 증가에 따라 수신전력이 감소한다.

이상적인 Free-space Propagation에서 Received Power는 다음 관계를 가진다.



$$
\boxed{ P_r\propto\frac{1}{d^2} }
$$



여기서

- $P_r$: Received Power
- $d$: Transmitter–Receiver Distance

이다.

즉 다른 조건이 같다면 거리가 증가할수록 평균 수신전력이 감소한다.

---



### 6.1 Free-space Path Loss

이상적인 Free-space 환경에서 Path Loss는 다음과 같이 표현할 수 있다.



$$
\boxed{ PL_{\mathrm{FS}} = \left( \frac{4\pi d}{\lambda} \right)^2 }
$$



여기서

- $d$: Distance
- $\lambda$: Wavelength

이다.

Decibel 단위로 표현하면 다음과 같다.



$$
\boxed{ PL_{\mathrm{FS,dB}} = 20\log_{10} \left( \frac{4\pi d}{\lambda} \right) }
$$



따라서 같은 거리에서는 Wavelength가 짧을수록 Free-space Path Loss가 커진다.

다만 실제 수신전력은 Antenna Gain, 전파환경 및 다른 손실요인에도 영향을 받는다.

---



## 7. Shadowing

Shadowing은 건물이나 지형 등의 큰 장애물에 의해 평균 수신전력이 변화하는 현상이다.

예를 들어 Transmitter와 Receiver 사이에 건물이 존재한다고 하자.



```text
Tx ● ───── █████ ───── ● Rx
            Building
```



건물에 의해 직접적인 전파 경로가 차단되거나 약화될 수 있다.

따라서 Receiver가 장애물의 뒤쪽으로 이동하면 수신전력이 감소할 수 있다.

Shadowing은 Multipath에 의한 빠른 변화와 비교했을 때 일반적으로 더 큰 공간적 Scale에서 변화한다.

---



### 7.1 Log-normal Shadowing

Shadowing은 흔히 Log-normal Model로 표현한다.

Decibel Domain에서는 다음과 같이 모델링할 수 있다.



$$
\boxed{ PL(d) = PL(d_0) + 10n\log_{10} \left( \frac{d}{d_0} \right) + X_\sigma }
$$



여기서

- $PL(d)$: 거리 $d$에서의 Path Loss
- $PL(d_0)$: Reference Distance에서의 Path Loss
- $n$: Path Loss Exponent
- $X_\sigma$: Shadowing Random Variable

이다.

일반적으로 $X_\sigma$는 다음과 같은 Gaussian Distribution으로 모델링한다.



$$
\boxed{ X_\sigma\sim\mathcal{N}(0,\sigma^2) }
$$



따라서 Linear Scale의 Shadowing Factor는 Log-normal 특성을 가진다.

---



## 8. Small-scale Fading

Small-scale Fading은 짧은 거리 또는 시간 동안 Received Signal의 Amplitude와 Phase가 빠르게 변화하는 현상이다.

대표적인 원인은 **Multipath Propagation**이다.

Wireless Signal은 하나의 경로로만 전달되지 않는다.

예를 들어 다음과 같은 경로가 존재할 수 있다.



```text
Tx
│
├──────── Direct Path ──────────→ Rx
│
├──── Reflection Path ─────────→ Rx
│
└──── Scattering Path ─────────→ Rx
```



Receiver에서는 여러 경로를 통해 전달된 Signal이 중첩된다.

각 Path가 서로 다른 Amplitude, Delay, Phase를 가지기 때문에 수신 Signal의 크기가 위치와 시간에 따라 변화할 수 있다.

---



## 9. Multipath Propagation

Multipath는 Transmitted Signal이 여러 Propagation Path를 거쳐 Receiver에 도착하는 현상이다.

각 Path는 다음과 같은 특성을 가질 수 있다.

- Different Path Length
- Different Propagation Delay
- Different Attenuation
- Different Phase
- Different Doppler Shift

이를 수학적으로 표현하면 다음과 같다.



$$
\boxed{ y(t) = \sum_{i=1}^{L} a_i(t)x(t-\tau_i(t)) + n(t) }
$$



여기서

- $L$: Multipath Component 수
- $a_i(t)$: $i$번째 Path의 Complex Gain
- $\tau_i(t)$: $i$번째 Path의 Delay

이다.

즉 Receiver에서는 서로 다른 Delay와 Gain을 가진 Signal들이 합쳐진다.

---



## 10. Delay Spread

Multipath 환경에서는 각 Path의 길이가 다르므로 Signal이 서로 다른 시간에 도착한다.

예를 들어 세 가지 Path가 존재한다고 하자.



```text
Path A → 0 μs
Path B → 2 μs
Path C → 5 μs
```



Receiver에서 관측되는 Multipath Component는 다음과 같이 표현할 수 있다.



```text
Received Power
     ↑
     │ █ A
     │      █ B
     │             █ C
     │
     └────────────────────→ Delay
       0      2       5 μs
```



이처럼 Multipath Component가 시간축으로 퍼지는 현상을 **Delay Spread**라고 한다.

Delay Spread가 클수록 서로 다른 경로로 전달된 Signal의 도착 시간 차이가 크다는 의미이다.

---



## 11. Maximum Excess Delay와 RMS Delay Spread

Delay Spread를 정량적으로 표현하는 방법에는 여러 가지가 있다.

대표적으로 다음 두 가지가 있다.

- Maximum Excess Delay
- RMS Delay Spread

### 11.1 Maximum Excess Delay

Maximum Excess Delay는 기준이 되는 첫 번째 도착 Path에 대해, 고려 대상인 마지막 Path가 얼마나 늦게 도착하는지 나타낸다.

단순화하면 다음과 같다.



$$
\boxed{ \tau_{\max} = \tau_{\mathrm{last}} - \tau_{\mathrm{first}} }
$$



이다.

실제 측정에서는 매우 약한 Path까지 모두 포함하면 Delay가 과도하게 커질 수 있으므로 Power Threshold 등을 설정하기도 한다.

---



### 11.2 RMS Delay Spread

RMS Delay Spread는 Multipath Component의 Delay뿐 아니라 각 Component의 Power까지 고려한다.

먼저 Mean Excess Delay를 정의한다.



$$
\boxed{ \bar{\tau} = \frac{ \sum_i P_i\tau_i }{ \sum_i P_i } }
$$



여기서

- $P_i$: $i$번째 Path의 Power
- $\tau_i$: $i$번째 Path의 Delay

이다.

RMS Delay Spread는 다음과 같다.



$$
\boxed{ \tau_{\mathrm{RMS}} = \sqrt{ \frac{ \sum_i P_i(\tau_i-\bar{\tau})^2 }{ \sum_i P_i } } }
$$



이다.

즉 Multipath Delay가 평균 도착 시간을 중심으로 얼마나 퍼져 있는지를 Power-weighted Standard Deviation으로 표현한다.

RMS Delay Spread는 Channel의 Frequency Selectivity를 분석할 때 중요한 지표이다.

---



## 12. Delay가 Phase를 변화시키는 이유

Time Domain에서 Signal이 $\tau$만큼 지연되었다고 하자.

원래 Signal은



$$
x(t)
$$



이고 지연된 Signal은



$$
x(t-\tau)
$$



이다.

Fourier Transform의 Time Shift Property는 다음과 같다.



$$
\boxed{ x(t-\tau) \longleftrightarrow X(f)e^{-j2\pi f\tau} }
$$



여기서



$$
e^{-j2\pi f\tau}
$$



의 Magnitude는 다음과 같다.



$$
\boxed{ |e^{-j2\pi f\tau}|=1 }
$$



즉 Time Delay는 Frequency Domain에서 Magnitude를 직접 변경하지 않고 Phase를 변화시킨다.

Phase 변화량은 다음과 같다.



$$
\boxed{ \Delta\phi=-2\pi f\tau }
$$



따라서 서로 다른 Delay를 가진 Multipath Component들은 Frequency Domain에서 서로 다른 Phase를 가진다.

---



## 13. Multipath와 Constructive·Destructive Interference

Receiver에서는 여러 Path를 통해 전달된 Signal이 더해진다.

각 Signal의 Phase가 비슷하면 Constructive Interference가 발생할 수 있다.



```text
Signal A: ↑

Signal B: ↑

Result  : ↑↑
```



즉 Signal들이 같은 방향으로 합쳐지면서 Received Amplitude가 증가한다.

반대로 Phase가 반대에 가까우면 Destructive Interference가 발생할 수 있다.



```text
Signal A: ↑

Signal B: ↓

Result  : 0에 가까움
```



이 경우 Received Amplitude가 크게 감소할 수 있다.

이처럼 수신 Signal이 심하게 약해지는 현상을 **Deep Fade**라고 한다.

따라서 Multipath는 단순히 여러 Signal이 도착하는 현상이 아니라, 서로 다른 Phase의 Signal이 합쳐지면서 수신전력이 크게 변화하는 원인이 된다.

---



## 14. Channel Frequency Response

Multipath Channel의 Frequency Response는 다음과 같이 표현할 수 있다.



$$
\boxed{ H(f) = \sum_{i=1}^{L} a_i e^{-j2\pi f\tau_i} }
$$



여기서는 Channel이 관찰 구간 동안 시간적으로 일정하다고 가정하였다.

이 식에서 중요한 부분은 다음과 같다.



$$
e^{-j2\pi f\tau_i}
$$



Frequency $f$가 변화하면 각 Path의 Phase도 변화한다.

그런데 Path마다 Delay $\tau_i$가 다르기 때문에 Phase의 변화 속도도 서로 다르다.

따라서 어떤 Frequency에서는 Constructive Interference가 발생하고, 다른 Frequency에서는 Destructive Interference가 발생할 수 있다.

결과적으로 Channel의 Frequency Response가 주파수에 따라 변화한다.



```text
Multipath
    │
    ▼
Different Delay
    │
    ▼
Different Phase
    │
    ▼
Frequency-dependent Interference
    │
    ▼
Frequency-selective Channel Response
```



이 관계가 Delay Spread와 Coherence Bandwidth를 연결하는 핵심 원리이다.

---



## 15. Coherence Bandwidth

Coherence Bandwidth $B_c$는 서로 다른 Frequency Component들이 **충분히 유사한 Channel Response를 경험한다고 볼 수 있는 주파수 범위**이다.

즉 Frequency가 조금 달라지더라도 Channel Gain과 Phase Response가 크게 변하지 않는 범위이다.



```text
Frequency Axis

|---------------- Bc ----------------|

        Similar Channel Response
```



Coherence Bandwidth는 Delay Spread와 밀접하게 관련된다.

일반적으로 다음 관계가 성립한다.



$$
\boxed{ B_c\propto\frac{1}{\tau_{\mathrm{RMS}}} }
$$



즉 Delay Spread가 증가하면 Coherence Bandwidth는 감소하는 경향이 있다.



```text
Delay Spread ↑
       │
       ▼
Frequency Response Variation ↑
       │
       ▼
Coherence Bandwidth ↓
```



다만 Coherence Bandwidth의 정확한 값은 Channel Correlation을 어느 수준 이상으로 유지할 것인지에 따라 달라진다.

따라서



$$
B_c\approx\frac{1}{\tau_{\mathrm{RMS}}}
$$



와 같은 식은 정성적인 관계를 이해하기 위한 근사식이다.

---



## 16. Flat Fading과 Frequency-selective Fading

Channel의 Frequency Selectivity는 Signal Bandwidth와 Coherence Bandwidth의 상대적인 크기로 구분할 수 있다.

Signal Bandwidth를 다음과 같이 정의하자.



$$
\boxed{ B_s }
$$



Channel의 Coherence Bandwidth는 다음과 같다.



$$
\boxed{ B_c }
$$



두 값의 상대적인 크기에 따라 Flat Fading과 Frequency-selective Fading을 구분한다.

---



### 16.1 Flat Fading

Flat Fading은 Signal Bandwidth가 Coherence Bandwidth보다 충분히 작은 경우에 나타난다.



$$
\boxed{ B_s\ll B_c }
$$



즉 Signal 전체 Bandwidth가 Channel Response가 거의 일정한 주파수 범위 안에 포함된다.



```text
|-------------------- Bc --------------------|

              |---- Bs ----|
```



따라서 Signal을 구성하는 Frequency Component들이 거의 동일한 Channel Gain과 Phase 특성을 경험한다.

이 경우 Narrowband Signal은 다음과 같이 단순화할 수 있다.



$$
\boxed{ y(t)\approx h(t)x(t)+n(t) }
$$



즉 Signal 전체에 하나의 Complex Channel Coefficient가 적용되는 형태로 생각할 수 있다.

다만 Flat Fading이라고 해서 Channel이 시간에 따라 변하지 않는 것은 아니다.

Flat Fading Channel도 Time-varying Channel일 수 있다.

---



### 16.2 Frequency-selective Fading

Frequency-selective Fading은 Signal Bandwidth가 Coherence Bandwidth보다 크거나 비슷하여, Signal 내부의 Frequency Component들이 서로 다른 Channel Response를 경험하는 경우이다.

대표적으로 다음과 같이 표현할 수 있다.



$$
\boxed{ B_s>B_c }
$$





```text
|------ Bc ------|

|------------------------- Bs -------------------------|
```



이 경우 각 Frequency Component는 서로 다른 Gain과 Phase Response를 경험할 수 있다.



```text
f1 → H(f1)

f2 → H(f2)

f3 → H(f3)
```



따라서 Signal Spectrum의 일부는 강하게 Attenuation되고 다른 부분은 상대적으로 잘 전달될 수 있다.

결과적으로 Received Waveform이 왜곡된다.

---



## 17. Delay Spread와 Symbol Duration

Time Domain에서는 Delay Spread를 Symbol Duration과 비교하여 Multipath의 영향을 이해할 수 있다.

Symbol Duration을 다음과 같이 정의하자.



$$
\boxed{ T_s }
$$



Delay Spread가 Symbol Duration에 비해 충분히 작다면 다음과 같다.



$$
\boxed{ \tau_{\mathrm{RMS}}\ll T_s }
$$



이 경우 Multipath에 의한 시간적 퍼짐이 Symbol Duration보다 작으므로 상대적으로 Flat Channel에 가까운 특성을 보일 수 있다.

반면 Delay Spread가 Symbol Duration과 비슷하거나 커지면 Multipath Component가 여러 Symbol 구간에 영향을 줄 수 있다.

이러한 현상은 Inter-Symbol Interference(ISI)와 연결된다.

다만 RMS Delay Spread는 Power-weighted Spread를 나타내므로 실제 ISI의 발생 여부와 크기는 전체 Channel Impulse Response 및 수신 Signal Processing에 따라 달라진다.

---



## 18. Inter-Symbol Interference(ISI)

Inter-Symbol Interference는 이전 또는 이후 Symbol의 Signal Component가 현재 Symbol의 검출에 영향을 주는 현상이다.

Multipath Channel에서는 Delay된 Signal이 다른 Symbol 구간까지 남을 수 있다.

예를 들어 원래 Signal이 다음과 같이 전송되었다고 하자.



```text
Original Symbols

| S1 | S2 | S3 | S4 |
```



Multipath Component가 Delay되어 도착하면 다음과 같은 상황이 발생할 수 있다.



```text
Direct Path

| S1 | S2 | S3 | S4 |


Delayed Path

    | S1 | S2 | S3 | S4 |
```



Receiver에서는 서로 다른 Symbol에 해당하는 Signal이 중첩된다.



```text
Delay Spread
      │
      ▼
Symbol Overlap
      │
      ▼
ISI
      │
      ▼
Detection Performance Degradation
```



따라서 Frequency-selective Channel에서는 Equalization 등의 Signal Processing이 중요해진다.

---



## 19. Delay Spread와 Symbol Rate

Symbol Rate는 다음과 같이 정의한다.



$$
\boxed{ R_s=\frac{1}{T_s} }
$$



여기서

- $R_s$: Symbol Rate
- $T_s$: Symbol Duration

이다.

Symbol Rate를 높이면 Symbol Duration은 짧아진다.



```text
Symbol Rate ↑
      │
      ▼
Symbol Duration ↓
```



그런데 Channel의 Delay Spread가 그대로라면 Symbol Duration이 짧아질수록 Multipath Delay가 상대적으로 큰 비중을 차지한다.



```text
Symbol Duration ↓
       │
       ▼
Relative Delay Spread ↑
       │
       ▼
ISI 발생 가능성 ↑
```



따라서 Multipath Delay Spread는 Equalization이나 Guard Interval 등을 사용하지 않는 단순한 통신 시스템에서 Symbol Rate를 제한하는 중요한 요인이다.

다만 Delay Spread가 존재한다고 해서 Data Rate에 절대적인 상한이 결정되는 것은 아니다.

Equalization, OFDM, Coding 등의 기법을 통해 Frequency-selective Channel의 영향을 완화할 수 있다.

---



## 20. Doppler Effect

지금까지는 Multipath Component의 Delay에 따른 Frequency Response 변화를 살펴보았다.

이번에는 **Channel이 시간에 따라 변화하는 원인**을 살펴보자.

Transmitter와 Receiver 사이에 상대적인 움직임이 존재하면 수신 Frequency가 달라질 수 있다.

이러한 현상을 Doppler Effect라고 한다.

예를 들어 Receiver가 Transmitter 방향으로 이동하거나 멀어지는 경우를 생각할 수 있다.



```text
Tx ● ───────────────→ ● Rx
                      Movement
```



상대적인 움직임에 따라 Received Frequency가 변할 수 있다.

---



### 20.1 Doppler Shift

Doppler Shift는 다음과 같이 표현할 수 있다.



$$
\boxed{ f_D = \frac{v}{\lambda}\cos\theta }
$$



여기서

- $v$: 상대적인 이동 속도
- $\lambda$: Carrier Wavelength
- $\theta$: 이동 방향과 Wave Propagation Direction 사이의 각도

이다.

각도와 부호는 정의하는 방향에 따라 달라질 수 있다.

Maximum Doppler Frequency의 크기는 다음과 같다.



$$
\boxed{ f_{D,\max} = \frac{v}{\lambda} }
$$



따라서



```text
Velocity ↑
     │
     ▼
Maximum Doppler Shift ↑
```



의 관계를 가진다.

또한 동일한 Velocity라면 Carrier Frequency가 높아질수록 Wavelength가 짧아지므로 Maximum Doppler Shift가 증가한다.

---



## 21. Doppler Spread

Multipath 환경에서는 각 Path가 서로 다른 방향에서 Receiver에 도착할 수 있다.

따라서 각 Path의 Doppler Shift가 달라질 수 있다.



```text
Path A → Doppler Shift fD1

Path B → Doppler Shift fD2

Path C → Doppler Shift fD3
```



이러한 Path들이 합쳐지면서 Received Signal의 주파수 성분이 퍼질 수 있다.

이를 **Doppler Spread**라고 한다.

일반적으로 다음 관계를 생각할 수 있다.



```text
Relative Movement
        │
        ▼
Path-dependent Doppler Shift
        │
        ▼
Doppler Spread
        │
        ▼
Time-varying Channel
```



Doppler Spread는 Channel이 시간에 따라 얼마나 빠르게 변하는지와 관련된다.

따라서 Doppler Spread가 크면 Channel Response가 빠르게 변화할 수 있다.

---



## 22. Coherence Time

Coherence Time $T_c$는 **Channel Response가 시간적으로 높은 상관성을 유지하는 구간**을 의미한다.

즉 Channel이 해당 시간 동안 거의 동일한 상태라고 근사할 수 있는 시간 범위이다.



```text
Time Axis

|---------- Tc ----------|

    Highly Correlated
    Channel Response
```



정확하게는 Channel의 Temporal Correlation을 기준으로 정의한다.

따라서 Coherence Time을 단순히 평균이나 분산이 일정하게 유지되는 시간으로 이해하는 것보다는, 순간적인 Channel Coefficient가 얼마나 오랫동안 유사하게 유지되는지로 이해하는 것이 적절하다.

Coherence Time은 Doppler Spread와 반비례하는 관계를 가진다.



$$
\boxed{ T_c\propto\frac{1}{B_D} }
$$



여기서 $B_D$는 Doppler Spread를 나타낸다.

Maximum Doppler Frequency를 이용하면 개념적으로 다음과 같이 표현할 수도 있다.



$$
\boxed{ T_c\sim\frac{1}{f_{D,\max}} }
$$



다만 정확한 계수는 Channel Model과 Correlation Threshold에 따라 달라진다.

---



## 23. Doppler Spread와 Coherence Time의 관계

Doppler Spread가 증가하면 Channel이 시간에 따라 더 빠르게 변화한다.

따라서 Coherence Time은 감소한다.



```text
Doppler Spread ↑
       │
       ▼
Channel Variation Rate ↑
       │
       ▼
Coherence Time ↓
```



반대로 Doppler Spread가 작으면 Channel이 시간적으로 천천히 변한다.



```text
Doppler Spread ↓
       │
       ▼
Channel Variation Rate ↓
       │
       ▼
Coherence Time ↑
```



이 관계는 Fast Fading과 Slow Fading을 구분하는 데 사용된다.

---



## 24. Fast Fading과 Slow Fading

Fast Fading과 Slow Fading은 Channel의 시간 변화 속도를 기준으로 구분한다.

여기서는 Symbol Duration $T_s$와 Coherence Time $T_c$를 비교한다.

---



### 24.1 Fast Fading

Fast Fading은 Channel이 Symbol Duration에 비해 빠르게 변화하는 경우이다.

대표적으로 다음과 같이 표현할 수 있다.



$$
\boxed{ T_c<T_s }
$$



즉 하나의 Symbol이 전송되는 동안 Channel Response가 상당히 변할 수 있다.



```text
Channel State

| h1 | h2 | h3 | h4 |


Symbol Duration

|------------------ Ts ------------------|
```



따라서 Receiver에서는 Symbol 내부에서도 Channel Variation의 영향을 받을 수 있다.

Fast Fading은 일반적으로 높은 Doppler Spread와 관련된다.



```text
Doppler Spread ↑
      │
      ▼
Coherence Time ↓
      │
      ▼
Fast Fading
```



---



### 24.2 Slow Fading

Slow Fading은 Channel이 Symbol Duration보다 훨씬 천천히 변화하는 경우이다.

대표적으로 다음과 같이 표현할 수 있다.



$$
\boxed{ T_c\gg T_s }
$$



이 경우 하나의 Symbol이 전송되는 동안 Channel Response가 거의 일정하다고 볼 수 있다.



```text
Channel State

|------------------- h1 -------------------|


Symbols

| S1 | S2 | S3 | S4 | S5 |
```



따라서 여러 Symbol이 거의 동일한 Channel Condition을 경험할 수 있다.



```text
Doppler Spread ↓
      │
      ▼
Coherence Time ↑
      │
      ▼
Slow Fading
```



---



### 24.3 Shadowing의 느린 변화와 Slow Fading의 구분

Shadowing도 수신전력이 비교적 천천히 변화하는 현상이다.

그러나 Shadowing과 Doppler 기준의 Slow Fading은 서로 다른 관점이다.

**Shadowing**

주로 장애물과 공간적인 차폐에 의해 발생하는 Large-scale Effect이다.

**Slow Fading**

Channel의 시간 변화 속도가 Symbol Duration에 비해 느린 경우를 의미한다.

따라서 두 현상을 동일한 개념으로 취급해서는 안 된다.



```text
Spatial Variation
      │
      ▼
Path Loss / Shadowing


Temporal Variation
      │
      ▼
Fast / Slow Fading
```



---



## 25. Flat·Frequency-selective와 Fast·Slow의 차이

Wireless Channel에서 가장 혼동하기 쉬운 부분은 네 가지 Fading을 하나의 기준으로 구분하려는 것이다.

하지만 실제로는 **서로 독립적인 두 가지 분류축**이 존재한다.

첫 번째는 Frequency Selectivity이다.



```text
Delay Spread
     │
     ▼
Coherence Bandwidth
     │
     ▼
Flat / Frequency-selective
```



두 번째는 Time Variation이다.



```text
Doppler Spread
     │
     ▼
Coherence Time
     │
     ▼
Fast / Slow
```



따라서 하나의 Channel은 다음과 같은 특성을 동시에 가질 수 있다.

| Frequency 특성 | Time 특성 | 가능한 Channel |
| --- | --- | --- |
| Flat | Slow | Slow Flat Fading |
| Flat | Fast | Fast Flat Fading |
| Frequency-selective | Slow | Slow Frequency-selective Fading |
| Frequency-selective | Fast | Fast Frequency-selective Fading |

즉 Flat Fading이라고 해서 반드시 Slow Fading인 것은 아니다.

마찬가지로 Frequency-selective Fading이라고 해서 반드시 Fast Fading인 것도 아니다.

---



## 26. Delay Spread와 Doppler Spread의 핵심 관계

두 현상을 수학적으로 비교하면 다음과 같다.

### Delay Spread

Delay Spread는 Multipath Component의 시간적인 퍼짐을 나타낸다.



$$
\boxed{ \tau_{\mathrm{RMS}} }
$$



이는 Coherence Bandwidth와 연결된다.



$$
\boxed{ B_c\propto\frac{1}{\tau_{\mathrm{RMS}}} }
$$



따라서



```text
Delay Spread ↑
      │
      ▼
Coherence Bandwidth ↓
      │
      ▼
Frequency Selectivity ↑
```



이다.

---



### Doppler Spread

Doppler Spread는 상대적인 움직임 등에 의해 발생하는 Frequency Spread를 나타낸다.



$$
\boxed{ B_D }
$$



이는 Coherence Time과 연결된다.



$$
\boxed{ T_c\propto\frac{1}{B_D} }
$$



따라서



```text
Doppler Spread ↑
      │
      ▼
Coherence Time ↓
      │
      ▼
Channel Time Variation ↑
```



이다.

---



### 두 관계의 비교

| 구분 | Delay Spread | Doppler Spread |
| --- | --- | --- |
| 주요 원인 | Multipath Propagation | Relative Movement |
| 직접적인 물리량 | Delay | Doppler Frequency |
| 연결되는 특성 | Coherence Bandwidth | Coherence Time |
| 주요 분류 | Flat / Frequency-selective | Fast / Slow |
| 대표적인 영향 | Frequency Distortion, ISI | Rapid Channel Variation |

여기서 중요한 점은 **Delay Spread는 시간 단위의 현상이지만 Frequency Selectivity와 연결되고, Doppler Spread는 주파수 단위의 현상이지만 Channel의 시간 변화와 연결된다**는 것이다.

이는 Time Domain과 Frequency Domain이 Fourier Transform으로 연결되어 있기 때문이다.

---



## 27. Fading이 Communication Performance에 미치는 영향

Fading은 수신 Signal의 품질을 저하시킬 수 있다.

대표적인 영향은 다음과 같다.

1. Received Signal Power 감소
2. SNR 저하
3. BER 증가
4. ISI 발생
5. Channel Estimation Error 증가

---



### 27.1 Deep Fade와 BER

Multipath Component들이 Destructive Interference를 일으키면 Received Signal Power가 크게 감소할 수 있다.



```text
Received Power

───────╲____╱────────
           │
           ▼
       Deep Fade
```



이 구간에서는 Effective SNR이 감소할 수 있다.



$$
\boxed{ \mathrm{SNR} = \frac{P_{\mathrm{signal}}}{P_{\mathrm{noise}}} }
$$



따라서 동일한 Noise Power에서 Signal Power가 감소하면 SNR도 감소한다.

일반적으로 SNR 감소는 Bit Error Rate(BER) 증가로 이어질 수 있다.

---



### 27.2 Frequency-selective Distortion과 ISI

Frequency-selective Channel에서는 Frequency Component마다 서로 다른 Channel Response가 적용된다.

따라서 Received Waveform이 왜곡될 수 있다.

특히 Multipath Delay가 Symbol Duration에 비해 크면 ISI가 발생할 수 있다.



```text
Frequency-selective Channel
            │
            ▼
Signal Distortion
            │
            ▼
ISI
            │
            ▼
Detection Performance Degradation
```



이러한 문제를 해결하기 위해 Equalization 등의 방법을 사용한다.

---



## 28. Fading에 대응하는 방법

Fading Channel의 영향을 완화하기 위한 대표적인 방법은 다음과 같다.

- Channel Estimation
- Equalization
- Diversity
- OFDM
- Channel Coding
- MIMO

각 방법은 서로 다른 방식으로 Channel의 영향을 보상한다.

---



### 28.1 Channel Estimation

Channel Estimation은 Receiver가 현재 Channel Response를 추정하는 과정이다.

예를 들어 Transmitter가 Receiver에 알려진 Pilot Signal을 전송한다고 하자.



```text
Known Pilot
     │
     ▼
   Channel
     │
     ▼
Received Pilot
     │
     ▼
Channel Estimation
     │
     ▼
Estimated Channel
```



Receiver는 송신된 Pilot과 수신된 Pilot을 비교하여 Channel을 추정할 수 있다.

이렇게 추정한 Channel Information은 Detection, Equalization, Beamforming 등에 활용할 수 있다.

---



### 28.2 Equalization

Equalization은 Channel에 의해 발생한 Distortion이나 ISI를 보상하는 방법이다.



```text
Transmitted Signal
        │
        ▼
Frequency-selective Channel
        │
        ▼
Distorted Signal
        │
        ▼
Equalizer
        │
        ▼
Recovered Signal
```



대표적으로 Linear Equalizer와 Decision Feedback Equalizer 등이 있다.

Equalization은 Channel Effect를 완화할 수 있지만 Noise Enhancement 등의 문제가 발생할 수도 있다.

따라서 실제 Receiver에서는 Channel 특성과 Noise Level을 함께 고려해야 한다.

---



### 28.3 Diversity

Diversity는 동일한 Information을 서로 다른 전송 자원을 통해 전달하거나 관측하는 방법이다.

대표적으로 다음과 같다.

- Time Diversity
- Frequency Diversity
- Spatial Diversity

예를 들어 하나의 Path가 Deep Fade를 경험하더라도 다른 Path 또는 다른 Antenna에서 상대적으로 좋은 Signal을 수신할 수 있다.



```text
Path A → Deep Fade

Path B → Good Reception

Path C → Moderate Reception
```



Receiver는 여러 관측값을 결합하여 통신 신뢰성을 높일 수 있다.

---



### 28.4 OFDM

OFDM(Orthogonal Frequency Division Multiplexing)은 Wideband Signal을 여러 개의 Orthogonal Subcarrier로 나누어 전송하는 방식이다.

Frequency-selective Channel에서도 각 Subcarrier의 Bandwidth를 충분히 좁게 설정하면 Subcarrier별 Channel을 Flat Fading에 가깝게 다룰 수 있다.



```text
Wideband Channel
       │
       ▼
Multiple Subcarriers
       │
       ▼
Subcarrier-wise Channel Processing
```



또한 Cyclic Prefix(CP)를 적절하게 사용하면 Multipath Delay에 의한 ISI를 완화할 수 있다.

---



### 28.5 Channel Coding

Channel Coding은 전송 데이터에 Redundancy를 추가하여 Receiver가 오류를 검출하거나 정정할 수 있도록 하는 기술이다.

Fading으로 인해 일부 Bit가 잘못 수신되더라도 Error-correcting Code를 이용하여 오류를 복구할 수 있다.

대표적인 예시는 다음과 같다.

- Convolutional Code
- Turbo Code
- LDPC Code
- Polar Code

---



## 29. AWGN Channel

AWGN은 다음의 약자이다.

**Additive White Gaussian Noise**

가장 기본적인 Channel Model 중 하나이다.

AWGN Channel은 다음과 같이 표현할 수 있다.



$$
\boxed{ y(t)=x(t)+n(t) }
$$



즉 Transmitted Signal에 Noise가 더해지는 형태이다.

---



### 29.1 Additive

Noise가 Signal에 더해진다는 의미이다.



$$
\boxed{ y=x+n }
$$



---



### 29.2 White

Noise가 관심 있는 Frequency Range에서 일정한 Power Spectral Density를 가진다고 가정한다는 의미이다.

---



### 29.3 Gaussian

Noise의 Amplitude가 Gaussian Distribution을 따른다고 가정한다.



$$
\boxed{ n\sim\mathcal{N}(0,\sigma^2) }
$$



실제 Complex Baseband Model에서는 Complex Gaussian Noise를 사용할 수 있다.

AWGN Channel은 Multipath Fading이 없는 상황에서 Noise의 영향을 분석하기 위한 기본적인 모델이다.

---



## 30. Rayleigh Fading

Rayleigh Fading은 강한 Dominant Specular Component가 존재하지 않고, 많은 Multipath Component가 합쳐지는 Channel을 모델링할 때 자주 사용된다.

대표적으로 NLOS(Non-Line-of-Sight) 환경을 생각할 수 있다.



```text
Tx

 ↘ Reflection
   ↘
     Rx
   ↗
 ↗ Scattering

Dominant LOS 없음
```



충분히 많은 독립적인 Multipath Component가 합쳐진다고 가정하면 Complex Channel Coefficient의 실수부와 허수부를 Gaussian Random Variable로 모델링할 수 있다.

이때 Channel Envelope는 Rayleigh Distribution을 따른다.

따라서 Rayleigh Fading은 다음과 같은 환경에서 활용할 수 있다.



```text
Many Multipath Components
           +
No Dominant Component
           │
           ▼
      Rayleigh Fading
```



다만 모든 NLOS 환경이 반드시 Rayleigh Distribution을 따르는 것은 아니다.

실제 Channel Model은 환경과 측정 결과에 따라 결정해야 한다.

---



## 31. Rician Fading

Rician Fading은 여러 Multipath Component와 함께 강한 Dominant Component가 존재하는 Channel을 모델링할 때 사용한다.

대표적인 경우는 LOS Path가 존재하는 환경이다.



```text
Tx ─────────────────────→ Rx
         Strong LOS

  \──── Reflection ─────→ Rx
   \─── Scattering ─────→ Rx
```



즉 다음과 같이 표현할 수 있다.



```text
Dominant Component
        +
Scattered Multipath
        │
        ▼
   Rician Fading
```



Rayleigh와 Rician의 주요 차이는 Dominant Component의 존재 여부이다.

---



## 32. Rician K-factor

Rician Fading에서는 Dominant Component와 Scattered Component의 상대적인 Power를 나타내기 위해 K-factor를 사용한다.



$$
\boxed{ K = \frac{ P_{\mathrm{dominant}} }{ P_{\mathrm{scattered}} } }
$$



여기서

- $P_{\mathrm{dominant}}$: Dominant Component Power
- $P_{\mathrm{scattered}}$: Scattered Multipath Power

이다.

K-factor가 클수록 Dominant Component의 영향이 강하다.

반대로 K-factor가 작아지면 Scattered Component의 상대적인 영향이 커진다.

특히 Dominant Component가 없는 경우



$$
\boxed{ K=0 }
$$



이므로 Rayleigh Model에 해당한다.

---



## 33. 주요 Channel Model 비교

| Model | 주요 특징 |
| --- | --- |
| AWGN | Noise의 영향을 분석하는 기본 모델 |
| Rayleigh | Dominant Component가 없는 Multipath |
| Rician | Dominant Component와 Scattered Multipath |
| Spatial Channel Model | Antenna와 Propagation Direction을 함께 고려 |
| Ray Tracing | 실제 Geometry를 기반으로 Propagation Path 계산 |

실제 Channel을 분석할 때는 다음과 같은 요소를 고려해야 한다.

- LOS / NLOS
- Number of Multipath Components
- Delay Spread
- Doppler Spread
- Antenna Configuration
- Propagation Environment

따라서 하나의 모델이 모든 Wireless Environment를 정확하게 표현하는 것은 아니다.

---



## 34. Antenna Configuration에 따른 Channel 분류

Transmitter와 Receiver에서 사용하는 Antenna 개수에 따라 Channel은 다음과 같이 분류할 수 있다.

| Channel | Transmit Antennas | Receive Antennas |
| --- | --- | --- |
| SISO | 1 | 1 |
| SIMO | 1 | Multiple |
| MISO | Multiple | 1 |
| MIMO | Multiple | Multiple |

---



### 34.1 SISO

SISO는 Single-Input Single-Output의 약자이다.



```text
Tx Antenna
     │
     ▼
   Channel
     │
     ▼
Rx Antenna
```



하나의 Transmit Antenna와 하나의 Receive Antenna를 사용한다.

가장 기본적인 Wireless Communication Link이다.

---



### 34.2 SIMO

SIMO는 Single-Input Multiple-Output의 약자이다.



```text
             ┌──→ Rx 1
Tx 1 ────────┼──→ Rx 2
             └──→ Rx 3
```



하나의 Transmit Antenna에서 Signal을 전송하고 여러 Receive Antenna에서 Signal을 수신한다.

Receive Diversity 등에 활용할 수 있다.

---



### 34.3 MISO

MISO는 Multiple-Input Single-Output의 약자이다.



```text
Tx 1 ──┐
Tx 2 ──┼────────→ Rx 1
Tx 3 ──┘
```



여러 Transmit Antenna와 하나의 Receive Antenna를 사용한다.

Transmit Diversity와 Beamforming 등에 활용할 수 있다.

---



### 34.4 MIMO

MIMO는 Multiple-Input Multiple-Output의 약자이다.



```text
Tx 1 ─────────→ Rx 1
    \          ↗
     \        /
      \      /
       \    /
Tx 2 ─────────→ Rx 2
```



여러 Transmit Antenna와 여러 Receive Antenna를 사용한다.

각 Transmit–Receive Antenna Pair마다 별도의 Channel이 형성된다.

따라서 SISO와 달리 Channel을 Matrix 형태로 표현할 수 있다.

---



## 35. MIMO Channel Matrix

Transmit Antenna가 $N_t$개이고 Receive Antenna가 $N_r$개라고 하자.

Flat-fading MIMO Channel에서는 다음과 같이 표현할 수 있다.



$$
\boxed{ \mathbf{y} = \mathbf{H}\mathbf{x} + \mathbf{n} }
$$



여기서



$$
\mathbf{x}\in\mathbb{C}^{N_t}
$$



는 Transmitted Signal Vector이고,



$$
\mathbf{y}\in\mathbb{C}^{N_r}
$$



는 Received Signal Vector이다.

Channel Matrix는 다음과 같다.



$$
\boxed{ \mathbf{H} \in \mathbb{C}^{N_r\times N_t} }
$$



예를 들어 2×2 MIMO에서는 다음과 같이 표현할 수 있다.



$$
\boxed{ \begin{bmatrix} y_1\\ y_2 \end{bmatrix} = \begin{bmatrix} h_{11}&h_{12}\\ h_{21}&h_{22} \end{bmatrix} \begin{bmatrix} x_1\\ x_2 \end{bmatrix} + \begin{bmatrix} n_1\\ n_2 \end{bmatrix} }
$$



여기서 $h_{ij}$는 $j$번째 Transmit Antenna에서 $i$번째 Receive Antenna까지의 Channel Coefficient이다.

즉 각 Receive Antenna는 여러 Transmit Antenna에서 전달된 Signal의 합을 수신한다.

---



## 36. MIMO와 Linear Algebra

SISO에서는 다음과 같은 Scalar Equation을 사용할 수 있다.



$$
\boxed{ y=hx+n }
$$



반면 MIMO에서는 여러 Transmit–Receive Pair가 존재한다.

따라서 다음과 같은 Matrix Equation을 사용한다.



$$
\boxed{ \mathbf{y} = \mathbf{H}\mathbf{x} + \mathbf{n} }
$$



이 과정에서는 다음 개념들이 중요하다.

- Vector
- Matrix
- Matrix Multiplication
- Matrix Inverse
- Eigenvalue Decomposition
- Singular Value Decomposition(SVD)

특히 MIMO Channel의 공간적인 특성을 분석하거나 여러 Data Stream을 분리하는 데 Linear Algebra가 중요한 역할을 한다.

---



## 37. Spatial Channel Model

MIMO에서는 Signal의 Amplitude와 Delay뿐 아니라 Signal이 어느 방향에서 도착하는지도 중요하다.

대표적인 공간 정보는 다음과 같다.

- Angle of Arrival(AoA)
- Angle of Departure(AoD)
- Antenna Array Geometry
- Spatial Correlation
- Multipath Cluster

이러한 요소를 고려하는 모델을 Spatial Channel Model이라고 한다.



```text
Transmit Antenna Array
          │
          ▼
Multiple Propagation Directions
          │
          ▼
Receive Antenna Array
          │
          ▼
Spatial Channel Response
```



Spatial Channel Model은 MIMO, Beamforming, Massive MIMO 등의 성능을 분석하는 데 활용할 수 있다.

---



## 38. Antenna Array와 Beamforming

여러 Antenna로 구성된 Antenna Array에서는 Signal이 각 Antenna에 도착하는 시간과 Phase가 다를 수 있다.

이러한 Phase Difference는 Signal의 Arrival Direction과 관련된다.

반대로 Transmitter에서 각 Antenna Signal의 Amplitude와 Phase를 적절하게 조절하면 특정 방향으로 Signal Energy를 집중시킬 수 있다.

이를 **Beamforming**이라고 한다.



```text
Antenna Array

●
●  )))))
●  )))))))))) → Target Direction
●  )))))
```



Beamforming의 핵심은 여러 Antenna Signal을 의도적으로 결합하여 원하는 방향에서 Constructive Interference가 발생하도록 하는 것이다.

즉 Multipath에서 수신전력 변화를 발생시키던 Wave Interference를 공간적으로 제어하여 통신 성능을 개선하는 기술이라고 이해할 수 있다.

---



## 39. Ray Tracing 기반 Channel Modeling

Wireless Channel은 주변 Environment의 Geometry에 크게 영향을 받는다.

특히 Indoor Environment에서는 다음 요소들이 중요하다.

- Wall Position
- Wall Material
- Ceiling
- Floor
- Furniture
- Corridor
- Transmitter Position
- Receiver Position

이러한 환경을 실제에 가깝게 Modeling하기 위해 Ray Tracing을 사용할 수 있다.

---



### 39.1 Ray Tracing의 기본 원리

Ray Tracing은 공간의 Geometry와 Material Properties를 바탕으로 Signal의 Propagation Path를 계산하는 방법이다.

먼저 3D Environment를 구성한다.



```text
3D Geometry
     +
Material Properties
     +
Tx / Rx Position
     │
     ▼
Ray Tracing
     │
     ▼
Propagation Paths
```



이후 Signal이 다음 현상을 거쳐 Receiver에 도달하는 경로를 계산한다.

- Reflection
- Transmission
- Diffraction
- Scattering

그 결과 특정 위치에서의 다음 정보를 예측할 수 있다.

- Received Power
- Propagation Delay
- Path Loss
- Multipath Structure
- Angle of Arrival
- Angle of Departure

다만 Ray Tracing의 정확도는 Geometry, Material Parameters 및 사용하는 Propagation Model의 정확도에 영향을 받는다.

---



## 40. 3D Map과 LiDAR 기반 Channel Prediction

실제 Indoor Environment를 모델링할 때는 LiDAR 등의 센서를 활용하여 3D Map을 생성할 수 있다.

LiDAR를 통해 Point Cloud를 획득한 뒤 공간의 Geometry를 구성할 수 있다.



```text
LiDAR Scanning
      │
      ▼
3D Point Cloud
      │
      ▼
Environment Reconstruction
      │
      ▼
Material / Geometry Modeling
      │
      ▼
Ray Tracing
      │
      ▼
Wireless Channel Prediction
```



이러한 접근은 실제 환경의 Digital Representation을 이용하여 Wireless Propagation을 분석하는 방법이다.

특히 Indoor Communication, Localization, Beamforming 및 Wireless Network Planning 등에 활용할 수 있다.

---



## 41. Wireless Channel의 전체 관계

지금까지 살펴본 개념을 하나의 흐름으로 연결하면 다음과 같다.



```text
                Physical Environment
                         │
          ┌──────────────┼───────────────┐
          │              │               │
          ▼              ▼               ▼
       Distance       Obstacles        Multipath
          │              │               │
          ▼              ▼               ▼
       Path Loss      Shadowing     Multiple Delays
                                         │
                              ┌──────────┴──────────┐
                              │                     │
                              ▼                     ▼
                         Delay Spread         Relative Motion
                              │                     │
                              ▼                     ▼
                      Coherence Bandwidth     Doppler Spread
                              │                     │
                              ▼                     ▼
                     Frequency Selectivity   Coherence Time
                              │                     │
                              ▼                     ▼
                     Flat / Frequency-       Fast / Slow
                     selective Fading            Fading
                              │                     │
                              └──────────┬──────────┘
                                         │
                                         ▼
                                Received Signal
                                         │
                                         ▼
                               Detection Performance
```



이러한 Channel Effect가 Noise와 결합하여 최종적인 Received Signal을 형성한다.

---



## 42. 핵심 관계 정리

### 42.1 Delay Spread와 Coherence Bandwidth



$$
\boxed{ \tau_{\mathrm{RMS}}\uparrow \Rightarrow B_c\downarrow }
$$



Delay Spread가 증가하면 Channel Frequency Response가 더 빠르게 변화할 수 있다.

따라서 Coherence Bandwidth가 감소한다.

---



### 42.2 Flat Fading



$$
\boxed{ B_s\ll B_c }
$$



Signal Bandwidth가 Coherence Bandwidth보다 충분히 작으면 Frequency-flat 특성을 가진다.

---



### 42.3 Frequency-selective Fading



$$
\boxed{ B_s>B_c }
$$



Signal Bandwidth가 Coherence Bandwidth보다 크면 Signal 내부의 Frequency Component들이 서로 다른 Channel Response를 경험할 수 있다.

---



### 42.4 Doppler Spread와 Coherence Time



$$
\boxed{ B_D\uparrow \Rightarrow T_c\downarrow }
$$



Doppler Spread가 증가하면 Channel Response가 더 빠르게 변화할 수 있다.

따라서 Coherence Time이 감소한다.

---



### 42.5 Fast Fading



$$
\boxed{ T_c<T_s }
$$



Channel이 Symbol Duration보다 빠르게 변화하는 경우이다.

---



### 42.6 Slow Fading



$$
\boxed{ T_c\gg T_s }
$$



Symbol Duration 동안 Channel Response가 거의 일정하게 유지되는 경우이다.

---



## 43. 주요 개념 비교

| 개념 | 정의 | 관련 현상 |
| --- | --- | --- |
| Path Loss | 전파 경로에서 발생하는 평균적인 Power Loss | Distance |
| Shadowing | 큰 장애물에 의한 평균 수신전력 변화 | Obstruction |
| Multipath | Signal이 여러 경로로 전달되는 현상 | Reflection, Scattering |
| Delay Spread | Multipath Component의 도착 시간 퍼짐 | Frequency Selectivity |
| Coherence Bandwidth | 유사한 Channel Response를 유지하는 주파수 범위 | Flat / Frequency-selective |
| Doppler Spread | 서로 다른 Doppler Shift에 의한 Frequency Spread | Time Variation |
| Coherence Time | Channel Response가 높은 상관성을 유지하는 시간 | Fast / Slow Fading |
| ISI | 서로 다른 Symbol 사이의 간섭 | Delay Spread |
| Deep Fade | 수신전력이 크게 감소하는 현상 | Destructive Interference |

---



## 44. Channel Model 비교

| Channel Model | 핵심 특징 | 주요 활용 |
| --- | --- | --- |
| AWGN | Additive Gaussian Noise | 기본 BER 분석 |
| Rayleigh | Dominant Component 없는 Multipath | 일부 NLOS 환경 |
| Rician | Dominant Component + Multipath | LOS 또는 Dominant Specular 환경 |
| Spatial Channel Model | 공간적 Propagation 특성 | MIMO, Beamforming |
| Ray Tracing | Geometry 기반 Propagation 계산 | Indoor·Outdoor Channel Prediction |

---



## 45. Communication System Design에서의 의미

Channel의 특성을 이해하는 것은 단순히 Propagation 현상을 설명하는 데 그치지 않는다.

실제 Communication System에서는 Channel의 특성에 따라 적절한 Transmission과 Reception 기술을 선택해야 한다.

예를 들어 다음과 같다.

**Delay Spread가 큰 Channel**

- ISI 가능성 증가
- Equalization 필요성 증가
- OFDM 및 Guard Interval 활용 고려

**Doppler Spread가 큰 Channel**

- Channel Response의 빠른 변화
- Channel Estimation 주기 조정 필요
- Time-varying Channel Tracking 중요

**Deep Fade가 자주 발생하는 Channel**

- Diversity 활용
- Channel Coding
- Antenna Configuration 개선

**MIMO Channel**

- Spatial Channel Estimation
- Beamforming
- Spatial Multiplexing
- Matrix-based Signal Processing

즉 Channel은 Communication System의 성능을 제한하는 요인이면서 동시에 Diversity와 Spatial Multiplexing처럼 새로운 전송 기회를 제공하는 요소이기도 하다.

---



## 최종 정리

Wireless Communication에서 실제 Received Signal은 전송된 Signal과 동일하지 않다.

Signal은 Channel을 통과하면서 Path Loss, Shadowing, Reflection, Diffraction, Scattering, Multipath, Doppler Effect 및 Noise의 영향을 받는다.

특히 Multipath는 서로 다른 Delay와 Phase를 가진 Signal들이 Receiver에서 중첩되는 현상이다.

이 과정에서 Constructive 또는 Destructive Interference가 발생하여 Signal의 Amplitude와 Phase가 변화한다.

Multipath Component의 Delay 차이는 Delay Spread를 발생시킨다.

Delay Spread가 증가하면 Channel Frequency Response가 주파수에 따라 빠르게 변화할 수 있으므로 Coherence Bandwidth가 감소한다.

따라서 다음 관계가 성립한다.



```text
Multipath
    │
    ▼
Delay Spread ↑
    │
    ▼
Coherence Bandwidth ↓
    │
    ▼
Frequency-selective Fading 가능성 ↑
    │
    ▼
ISI 가능성 ↑
```



반면 Transmitter와 Receiver 또는 주변 물체의 상대적인 움직임은 Doppler Effect를 발생시킬 수 있다.

Multipath Component마다 서로 다른 Doppler Shift가 존재하면 Doppler Spread가 발생한다.

Doppler Spread가 증가하면 Channel Response가 더 빠르게 변화하므로 Coherence Time이 감소한다.



```text
Relative Movement
       │
       ▼
Doppler Spread ↑
       │
       ▼
Coherence Time ↓
       │
       ▼
Fast Fading 가능성 ↑
```



따라서 Wireless Channel은 두 가지 관점에서 분석할 수 있다.

**Frequency Domain**



$$
\boxed{ \text{Delay Spread} \rightarrow \text{Coherence Bandwidth} \rightarrow \text{Flat / Frequency-selective} }
$$



**Time Domain**



$$
\boxed{ \text{Doppler Spread} \rightarrow \text{Coherence Time} \rightarrow \text{Fast / Slow} }
$$



또한 실제 Channel은 AWGN, Rayleigh, Rician 등의 Statistical Model로 표현하거나, Geometry를 이용하는 Ray Tracing 방식으로 분석할 수 있다.

여러 Antenna를 사용하는 MIMO 시스템에서는 Channel을 Matrix로 표현하며, Spatial Channel Information을 활용하여 Diversity, Beamforming 및 Spatial Multiplexing을 수행할 수 있다.

결국 **Wireless Channel은 거리·장애물·Multipath·상대적인 움직임에 따라 시간과 주파수 영역에서 변화하는 System이며, 이러한 특성을 분석하고 보상하는 것이 안정적인 Digital Communication System을 설계하는 핵심**이다.

---

이전: [디지털 변조와 신호자리표: Bit에서 QAM까지](../modulation/digital-modulation-and-constellations.md)

다음: [채널·잡음·BER와 Shannon Capacity](noise-ber-and-shannon-capacity.md)
