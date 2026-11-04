# Spread Spectrum: DSSS, FHSS, THSS와 대역확산

[TOC]

일반적인 통신에서는 가능한 한 주어진 Bandwidth를 효율적으로 사용하는 것이 중요하다.

그런데 **Spread Spectrum(대역확산)**은 이름 그대로 원래 정보 신호가 필요로 하는 대역폭보다 **훨씬 넓은 주파수 대역으로 Signal을 일부러 확산하여 전송**한다.

처음 보면

> "대역폭은 아껴야 하는데 왜 일부러 넓게 사용하는가?"

라는 의문이 생긴다.

Spread Spectrum의 목적은 단순한 Bandwidth 절약이 아니라

- Interference에 대한 강인성
- Jamming에 대한 저항성
- Frequency-selective Fading 영향 완화
- 여러 사용자의 주파수 자원 공유
- 신호의 낮은 Power Spectral Density

등을 얻는 데 있다.

전체적인 개념은 다음과 같다.



```text
Original Data
      ↓
Spreading
      ↓
Wideband Signal
      ↓
Channel
+ Noise / Interference / Jamming
      ↓
Despreading
      ↓
Original Data Recovery
```



대표적인 Spread Spectrum 방식에는



```text
DSSS
FHSS
THSS
Hybrid Spread Spectrum
```



이 있다.

---

## 1. Spread Spectrum이란?

Spread Spectrum은 원래 정보 신호의 Bandwidth보다 훨씬 넓은 Bandwidth로 Signal을 확산하여 전송하는 방식이다.

예를 들어 원래 데이터가 비교적 좁은 대역을 필요로 한다고 하자.



```text
Original Signal

Frequency
    ↑
    │     ███
    │     ███
────┴────────────→
      Narrow Band
```



Spread Spectrum을 적용하면 Signal Energy가 더 넓은 Frequency Range에 퍼진다.



```text
Spread Signal

Frequency
    ↑
    │  ░░░░░░░░░░░░░
    │  ░░░░░░░░░░░░░
────┴────────────────→
        Wide Band
```



전체 Signal Power가 반드시 증가하는 것은 아니다.

오히려 동일한 Power가 넓은 Bandwidth에 분산되므로 **Power Spectral Density는 낮아질 수 있다.**

---



## 2. 왜 신호를 일부러 넓게 퍼뜨리는가?

Spread Spectrum의 핵심 아이디어는

> **특정한 시간이나 주파수 영역에 Signal Energy를 집중시키지 않고 넓게 분산시키는 것**

이다.

이 때문에 특정 주파수 일부가 Interference나 Jamming에 의해 손상되더라도 전체 정보가 한 번에 사라질 가능성을 줄일 수 있다.



```text
Narrowband Signal

        Interference
             ↓
         ███████
─────────███████────────
         Signal 전체에
         큰 영향 가능
```



반면 Spread Spectrum에서는



```text
Spread Signal

      Interference
           ↓
░░░░░░░░░████░░░░░░░░░
```



처럼 전체 넓은 Band 중 일부만 강한 영향을 받을 수 있다.

Receiver에서는 Spreading에 사용한 규칙을 알고 있으므로 원래 Signal을 다시 모아 복원한다.

---

## 3. PN Sequence

Spread Spectrum에서 매우 중요한 개념이 **PN Sequence(Pseudo-Noise Sequence)**이다.

PN Sequence는 Noise처럼 불규칙하게 보이지만 실제로는 송신기와 수신기가 알고 있는 **결정론적인 Binary Sequence**이다.

예를 들면



```text
0 1 1 0 1 0 1 1 0 0 1 0 ...
```



과 같은 형태이다.

Random Noise와 달리 일정한 생성 규칙을 가지고 있기 때문에 Receiver에서도 동일한 Sequence를 생성할 수 있다.



```text
Transmitter
PN Generator
     ↓
0 1 1 0 1 0 ...

Receiver
PN Generator
     ↓
0 1 1 0 1 0 ...
```



송신기와 수신기가 동일한 PN Sequence를 **정확한 Timing으로 동기화**하는 것이 중요하다.

---



## 4. Bit와 Chip

DSSS에서는 일반적인 Data Bit보다 훨씬 빠른 속도의 PN Sequence를 사용한다.

PN Sequence의 한 요소를 **Chip**이라고 한다.



```text
Data Bit
──────────────
     Tb

Chip
─── ─── ─── ───
 Tc  Tc  Tc  Tc
```



일반적으로



$$
T_c \ll T_b
$$



이다.

여기서

- $T_b$ : Bit Duration
- $T_c$ : Chip Duration

이다.

따라서 Chip Rate는 Bit Rate보다 훨씬 높다.



$$
R_b=\frac{1}{T_b}
$$





$$
R_c=\frac{1}{T_c}
$$



그리고 일반적으로



$$
R_c \gg R_b
$$



이다.

---



## 5. Processing Gain

Spread Spectrum에서 중요한 성능 지표가 **Processing Gain(PG)**이다.

개념적으로



$$
PG \approx \frac{W_{\text{spread}}}{W_{\text{data}}}
$$



로 표현할 수 있다.

DSSS에서는 Chip Rate와 Bit Rate의 관계를 이용하여



$$
PG \approx \frac{R_c}{R_b}
$$



또는



$$
PG \approx \frac{T_b}{T_c}
$$



로 볼 수 있다.

예를 들어 Data Bit 하나가 4개의 Chip으로 나누어진다면



```text
1 Data Bit
     ↓
4 Chips
```



이므로



$$
PG=4
$$



이다.

dB 단위로 표현하면



$$
PG_{dB}=10\log_{10}(4) \approx6.02\,dB
$$



이다.

Processing Gain이 클수록 Signal을 더 넓게 확산시킨다는 의미이며, 적절한 Receiver Processing을 통해 협대역 간섭에 대한 저항성을 얻을 수 있다.

---



## 6. Spread Spectrum의 종류

대표적인 Spread Spectrum 방식은 다음과 같다.



```text
Spread Spectrum
│
├─ DSSS
│  Direct Sequence Spread Spectrum
│
├─ FHSS
│  Frequency Hopping Spread Spectrum
│
├─ THSS
│  Time Hopping Spread Spectrum
│
└─ Hybrid
   DSSS + FHSS + THSS 등의 조합
```



각 방식은 Signal을 확산시키는 **축**이 다르다.

| 방식 | 무엇을 변화시키는가? |
| --- | --- |
| DSSS | 고속 PN Chip Sequence |
| FHSS | Carrier Frequency |
| THSS | Transmission Time / Slot |
| Hybrid | 둘 이상의 방식 결합 |

---



## 7. DSSS란?

DSSS는 **Direct Sequence Spread Spectrum**의 약자이다.

우리말로는 **직접 시퀀스 대역확산**이라고 한다.

DSSS에서는 원래 Data에 고속의 PN Sequence를 직접 결합하여 Signal Bandwidth를 확산한다.



```text
User Data
    │
    ▼
   XOR ◀──── PN Sequence
    │
    ▼
Spread Data
```



Binary 표현에서는 주로 XOR 연산으로 설명할 수 있다.

---



## 8. DSSS의 XOR 연산

Data Bit를 $A$, PN Sequence를 $B$라고 하면 Spread Signal $C$는



$$
C=A\oplus B
$$



로 표현할 수 있다.

XOR의 기본 성질은 다음과 같다.



```text
0 XOR 0 = 0
0 XOR 1 = 1

1 XOR 0 = 1
1 XOR 1 = 0
```



따라서 Data가 `0`이면 PN Sequence가 그대로 나오고,



```text
0 XOR B = B
```



Data가 `1`이면 PN Sequence가 반전된다.



```text
1 XOR B = NOT B
```



---



## 9. DSSS 예제

PN Sequence가 다음과 같다고 하자.



```text
PN = 0 1 1 0 1 0 1
```



### Data = 0



```text
Data
0 0 0 0 0 0 0

PN
0 1 1 0 1 0 1
────────────────
XOR
0 1 1 0 1 0 1
```



PN Sequence가 그대로 나온다.

---



### Data = 1



```text
Data
1 1 1 1 1 1 1

PN
0 1 1 0 1 0 1
────────────────
XOR
1 0 0 1 0 1 0
```



PN Sequence가 반전된다.

따라서



```text
Data Bit
     ↓
여러 Chip으로 확장
     ↓
Chip Rate 증가
     ↓
Signal Bandwidth 증가
```



가 일어난다.

---



## 10. DSSS 송신기

송신기의 전체 과정은 다음과 같다.



```text
Original Data A
       │
       ▼
      XOR ◀──── PN Sequence B
       │
       ▼
Spread Signal C
```



수식으로는



$$
C=A\oplus B
$$



이다.

---



## 11. DSSS 수신기와 Despreading

Receiver에서는 Transmitter와 **동일한 PN Sequence**를 다시 사용한다.



```text
Received Spread Signal C
          │
          ▼
         XOR ◀──── Same PN Sequence B
          │
          ▼
    Original Data A
```



수식으로 보면



$$
C=A\oplus B
$$



이고 Receiver에서 다시 $B$를 XOR하면



$$
C\oplus B
$$



이다.

따라서



$$
(A\oplus B)\oplus B
$$



가 되고 XOR의 결합법칙을 이용하면



$$
A\oplus(B\oplus B)
$$



이다.

같은 Bit끼리 XOR하면



$$
B\oplus B=0
$$



이므로



$$
A\oplus0=A
$$



가 된다.

결국



$$
\boxed{A=(A\oplus B)\oplus B}
$$



가 되어 원래 Data가 복원된다.

---



## 12. DSSS에서 Synchronization이 중요한 이유

Receiver가 아무 PN Sequence나 사용해서는 원래 Signal을 복원할 수 없다.

다음 두 조건이 필요하다.



```text
① 동일한 PN Sequence
② 정확한 Chip Timing
```



즉 송수신기의 PN Code가 같더라도 Timing이 어긋나면 올바른 Correlation Peak를 얻기 어렵다.

따라서 DSSS에서는 **Code Synchronization**이 매우 중요하다.

---



## 13. DSSS는 왜 Interference에 강한가?

DSSS의 중요한 특징은 **원래 Signal은 Despreading되고, 외부에서 들어온 협대역 Interference는 오히려 Spread된다는 것**이다.

송신단에서는



```text
Original Signal
      ↓
PN Sequence
      ↓
Spreading
      ↓
Wideband Signal
```



이 된다.

Channel 중간에서 Narrowband Jamming이 들어왔다고 하자.



```text
Spread Signal
     +
Narrowband Jamming
```



Receiver에서 동일한 PN Sequence를 적용하면



```text
Desired Spread Signal
        ↓
Despreading
        ↓
Original Narrowband Signal
```



로 복원된다.

하지만 Channel 중간에 들어온 Jamming Signal은 송신 PN Code와 상관관계가 없기 때문에 Receiver의 PN Sequence와 결합되면서 오히려 넓게 퍼진다.



```text
Narrowband Jamming
       ↓
Receiver PN Sequence
       ↓
Wideband Interference
```



이후 원하는 Signal Band만 통과시키는 Filter를 사용하면 Spread된 Interference Energy의 상당 부분을 제거할 수 있다.

---



## 14. 시간 영역과 주파수 영역에서 보는 DSSS

DSSS를 시간 영역과 주파수 영역으로 동시에 보면 이해하기 쉽다.

#### 시간 영역



```text
Slow Data
   ↓
Fast PN Chips
   ↓
Fast-changing Spread Signal
```



Bit보다 Chip이 훨씬 빠르게 변화한다.

#### 주파수 영역

빠른 시간 변화는 넓은 Frequency Content를 의미한다.



```text
Before Spreading
Narrow Bandwidth

        ↓

After Spreading
Wide Bandwidth
```



즉



$$
T_c \downarrow \quad\Longrightarrow\quad R_c \uparrow \quad\Longrightarrow\quad Bandwidth \uparrow
$$



라고 이해할 수 있다.

---



## 15. DSSS와 Frequency-Selective Fading

DSSS는 Signal Energy를 넓은 Frequency Range에 분산시키기 때문에 특정 주파수 일부가 Deep Fade를 겪더라도 전체 Signal Energy가 모두 사라질 가능성을 줄일 수 있다.



```text
Wideband DSSS Signal

f1 f2 f3 f4 f5 f6 f7
██ ██ XX ██ ██ ██ ██
      ↑
   Deep Fade
```



특정 Frequency 영역이 손상되더라도 다른 Frequency Component가 남아 있을 수 있다.

다만 DSSS가 Frequency-selective Fading을 자동으로 완전히 제거하는 것은 아니다.

실제 CDMA Receiver에서는 Multipath Component를 이용하는 **RAKE Receiver** 등의 기술과 함께 Fading에 대응할 수 있다.

---



## 16. DSSS와 CDMA

DSSS의 Code 개념은 **CDMA(Code Division Multiple Access)**와도 연결된다.

여러 사용자가 같은 Frequency Band를 동시에 사용하더라도 서로 다른 Spreading Code를 사용하도록 할 수 있다.



```text
User A
Data × Code A ──┐
                │
User B           ├── Same Frequency Band
Data × Code B ──┤
                │
User C           │
Data × Code C ──┘
```



Receiver는 원하는 User의 Code와 Correlation하여 해당 Signal을 구분한다.



```text
Received Signals
      ↓
Code A
      ↓
User A Detection
```



즉 Frequency나 Time만으로 사용자를 나누는 대신 **Code를 이용하여 사용자를 구분하는 방식**이다.

---



## 17. DSSS의 장점과 단점

### 장점

- Narrowband Interference에 강함
- Jamming 저항성 향상
- Multipath / Frequency-selective Fading의 영향 완화 가능
- Code를 이용한 Multiple Access 가능
- Signal Power를 넓은 Band에 분산 가능

### 단점

- 넓은 Bandwidth가 필요함
- PN Code 생성 및 동기화가 필요함
- Receiver 구조가 복잡해질 수 있음
- Code 간 상호상관 특성이 성능에 영향을 줌

---



## 18. FHSS란?

FHSS는 **Frequency Hopping Spread Spectrum**의 약자이다.

우리말로는 **주파수 호핑 대역확산**이라고 한다.

FHSS에서는 한 Frequency를 계속 사용하는 것이 아니라 Carrier Frequency를 시간에 따라 계속 바꾼다.



```text
Time →
────────────────────────

f4        █       █

f3    █       █

f2            █       █

f1 █      █
```



즉



```text
f1
 ↓
f3
 ↓
f2
 ↓
f4
 ↓
...
```



처럼 Frequency가 계속 Hop한다.

---



## 19. FHSS와 PN Code

Frequency를 아무렇게나 바꾸는 것은 아니다.

송수신기가 공유하는 Pseudo-random Code에 따라 다음 Frequency가 결정된다.



```text
PN Code
   ↓
Frequency Table
   ↓
Frequency Synthesizer
   ↓
Carrier Frequency
```



예를 들어 Frequency Table이 다음과 같다고 하자.

| Code | Frequency |
| --- | --- |
| 000 | $f_0$ |
| 001 | $f_1$ |
| 010 | $f_2$ |
| 011 | $f_3$ |
| 100 | $f_4$ |
| 101 | $f_5$ |
| 110 | $f_6$ |
| 111 | $f_7$ |

PN Sequence가



```text
010
101
001
111
...
```



이면 Carrier는



```text
f2
→ f5
→ f1
→ f7
→ ...
```



처럼 이동한다.

---



## 20. FHSS의 송신 구조

FHSS Transmitter를 단순화하면 다음과 같다.



```text
Original Signal
        │
        ▼
     Modulator ◀──── Frequency Synthesizer
                         ▲
                         │
                     Frequency Table
                         ▲
                         │
                   PN Code Generator
```



PN Code Generator가 Frequency Selection Pattern을 만들고 Frequency Synthesizer가 해당 Carrier를 생성한다.

---



## 21. FHSS가 Interference에 강한 이유

특정 Frequency에 강한 Interference가 존재한다고 하자.



```text
f1 → 정상
f2 → 정상
f3 → Jamming
f4 → 정상
```



FHSS가 계속 $f_3$에 머문다면 큰 문제가 발생한다.

하지만 실제로는



```text
f1 → f3 → f4 → f2 → ...
```



와 같이 이동하기 때문에 $f_3$에 머무는 시간은 제한적이다.

따라서



```text
Narrowband Interference
        ↓
일부 Hop에만 큰 영향
        ↓
전체 Transmission은 계속 가능
```



한 특성을 얻는다.

---



## 22. FHSS에서 Synchronization

FHSS에서도 송수신기의 Synchronization이 매우 중요하다.

Transmitter가



```text
현재 f3
```



를 사용하고 있는데 Receiver가



```text
현재 f1
```



을 듣고 있다면 Communication이 불가능하다.

따라서 송신기와 수신기는



```text
같은 Hopping Sequence
+
같은 Hopping Timing
```



을 알고 있어야 한다.

---



## 23. FHSS의 장점

#### Interference에 강함

특정 Frequency가 손상되어도 다음 Hop에서 다른 Frequency로 이동한다.

#### Frequency-selective Fading 대응

특정 Frequency에 Deep Fade가 발생하더라도 계속 다른 Frequency를 사용하므로 영향을 줄일 수 있다.

#### 자원 공유 가능

적절한 Hopping Pattern을 이용하면 여러 사용자가 Frequency Resource를 시간에 따라 공유할 수 있다.

#### 통신 패턴을 모르면 추적이 어려워질 수 있음

Hopping Sequence를 모르는 Receiver 입장에서는 어느 Frequency를 어느 시점에 수신해야 하는지 알기 어렵다.

다만 **FHSS 자체가 암호화(Encryption)를 대체하는 것은 아니다.**

---



## 24. FHSS의 단점

FHSS는 다음과 같은 추가적인 Hardware 및 Synchronization이 필요하다.

- Frequency Synthesizer
- 빠른 Frequency Switching
- Hopping Sequence Generator
- 정확한 Timing Synchronization

특히 Hop Interval을 지나치게 짧게 하면 Frequency Synthesizer가 Frequency를 안정화하기 전에 다시 다음 Frequency로 이동해야 할 수도 있다.

따라서 Hopping Speed는 Hardware Capability와 함께 설계해야 한다.

---



## 25. FHSS의 활용

FHSS의 대표적인 응용으로는

- Bluetooth 계열의 Frequency Hopping
- Military Communication
- 일부 저전력 Wireless System

등을 생각할 수 있다.

특히 Classic Bluetooth에서는 빠르게 Frequency Channel을 이동하는 Hopping 방식이 사용된다.

---



## 26. FDM과 FHSS의 차이

FDM과 FHSS는 모두 여러 Frequency를 사용하므로 처음에는 비슷하게 보일 수 있다.

하지만 목적과 Frequency 사용 방법이 완전히 다르다.

---



### FDM

FDM은 전체 Bandwidth를 여러 개의 **고정된 Frequency Band**로 나눈다.



```text
Frequency
   ↑
f4 │████████ User D
f3 │████████ User C
f2 │████████ User B
f1 │████████ User A
   └────────────────→ Time
```



각 User는 자신에게 할당된 Frequency Band를 계속 사용한다.

---



### FHSS

FHSS에서는 User가 사용하는 Frequency가 시간에 따라 바뀐다.



```text
Frequency
   ↑
f4 │    █       █
f3 │        █
f2 │  █         █
f1 │█     █
   └────────────────→ Time
```



즉 Frequency-Time Plane에서 보면 Signal이 여러 Frequency를 Hop하며 이동한다.

---



## 27. FDM vs FHSS

| 구분 | FDM | FHSS |
| --- | --- | --- |
| 목적 | 여러 Signal을 주파수로 분리 | Spread Spectrum / Interference 회피 |
| Frequency | 사용자별 고정 | 시간에 따라 변경 |
| 사용 방식 | 여러 Band를 동시에 사용 | 한 Signal이 여러 Frequency를 순차 사용 |
| Guard Band | 일반적으로 필요 | Hop Channel 설계에 따라 다름 |
| Interference | 특정 Channel 간섭이 지속될 수 있음 | 특정 간섭을 다음 Hop에서 벗어날 수 있음 |
| Synchronization | 비교적 단순 | Hopping Timing 동기화 중요 |
| 대표 예 | Radio Channel Division | Bluetooth, Military Communication |

한 문장으로 비교하면



```text
FDM
→ Frequency 공간을 고정적으로 나누어 사용

FHSS
→ Frequency 공간을 시간에 따라 이동하며 사용
```



이다.

---



## 28. FDM에서 Guard Band가 필요한 이유

실제 Filter는 이상적인 Brick-wall Filter가 아니다.

따라서 인접 Frequency Channel을 완전히 붙여 놓으면 서로의 Signal Energy가 침범할 수 있다.



```text
Channel A  Guard  Channel B
████████     │    ████████
             │
        Guard Band
```



Guard Band는 Data를 직접 싣지 않는 Frequency 영역이므로 Spectral Efficiency 관점에서는 손해이다.

하지만 Adjacent Channel Interference를 줄이기 위해 필요한 안전 공간이라고 볼 수 있다.

---



## 29. THSS란?

THSS는 **Time Hopping Spread Spectrum**의 약자이다.

FHSS가 Frequency를 바꾸는 방식이라면 THSS는 **Signal이 나타나는 시간을 변화시키는 방식**이다.



```text
Time Slot

1  2  3  4  5  6  7  8  9 ...
█        █     █           █
```



PN Code가

> 어느 Time Slot에서 Signal을 전송할 것인가?

를 결정한다.

---



## 30. THSS의 기본 구조

전체 Time Axis를 여러 Slot으로 나눈다.



```text
|1|2|3|4|5|6|7|8|9|10|...
```



그중 PN Code가 선택한 Slot에서만 Pulse를 전송한다.



```text
|█| | |█| |█| | | |█|...
```



다른 User는 다른 Pattern을 사용할 수 있다.



```text
User 1
█     █        █

User 2
   █      █       █

User 3
      █       █
```



따라서 Time Domain에서 충돌 가능성을 줄이며 여러 User가 Resource를 공유할 수 있다.

---



## 31. THSS의 활용

THSS는 짧은 Pulse를 사용하는 시스템과 잘 결합된다.

대표적으로 **Impulse Radio UWB(Ultra-Wideband)**와 같은 시스템에서 사용될 수 있다.

특징은 다음과 같다.

- Time Domain Hopping
- Short Pulse Transmission
- Low Duty Cycle 구현 가능
- Multiple Access에 활용 가능

---



## 32. DSSS · FHSS · THSS 비교

| 구분 | DSSS | FHSS | THSS |
| --- | --- | --- | --- |
| 핵심 변화 | PN Chip Sequence | Carrier Frequency | Transmission Time |
| 확산 영역 | 주로 Code/주파수 | Frequency | Time |
| 핵심 요소 | Chip | Hop | Time Slot |
| Synchronization | Code/Chip Timing | Hop Timing | Slot Timing |
| Interference 대응 | Despreading | Frequency 회피 | Time 회피 |
| 대표 응용 | CDMA, GPS, 초기 Wi-Fi | Bluetooth, 군사 통신 | UWB 등 |

---



## 33. Hybrid Spread Spectrum

실제 시스템에서는 DSSS, FHSS, THSS 중 하나만 사용해야 하는 것은 아니다.

두 가지 이상의 방식을 결합할 수도 있다.

이를 **Hybrid Spread Spectrum**이라고 한다.

예를 들어



```text
FHSS
+
DSSS
```



를 결합하면



```text
Frequency Hopping
       ↓
각 Hop 내부에서 DSSS
       ↓
Frequency + Code 두 영역 활용
```



이 가능하다.

---



## 34. FH/DS

FH/DS는



```text
Frequency Hopping
+
Direct Sequence
```



를 결합한다.

먼저 넓은 Frequency Range 안에서 Carrier가 Hop한다.



```text
f1 → f4 → f2 → f3
```



그리고 각 Frequency 안에서는 DSSS Code를 이용해 Signal을 다시 Spread한다.



```text
Frequency Hopping
      +
PN Code Spreading
```



이 방식은 Interference와 Jamming에 대한 Robustness를 더욱 강화할 수 있다.

---



## 35. FH/TH

FH/TH는



```text
Frequency Hopping
+
Time Hopping
```



을 결합한 방식이다.

즉 Signal이



```text
Frequency
+
Time
```



두 차원에서 모두 이동한다.



```text
Frequency
   ↑
f4 │      ■
f3 │ ■
f2 │          ■
f1 │    ■
   └────────────→ Time
```



따라서 여러 User나 강한 Interference가 존재하는 환경에서 Resource Collision을 줄이는 데 활용할 수 있다.

---



## 36. Spread Spectrum과 Multiple Access의 관계

Spread Spectrum과 Multiple Access는 같은 개념은 아니다.

Spread Spectrum은 **Signal을 넓은 Resource 영역으로 확산하는 기술**이고,

Multiple Access는 **여러 User가 하나의 Communication Resource를 공유하는 방법**이다.

하지만 둘은 서로 결합될 수 있다.

대표적인 예가 CDMA이다.



```text
DSSS
+
Different Spreading Codes
        ↓
CDMA
```



또한 FHSS나 THSS에서도 서로 다른 Hopping Pattern을 이용하여 여러 User를 구분할 수 있다.

---



## 37. Spread Spectrum에서 Synchronization이 특히 중요한 이유

Spread Spectrum 방식은 공통적으로 Sender와 Receiver가 같은 확산 규칙을 알고 있어야 한다.



```text
DSSS
→ 같은 PN Code + Chip Timing

FHSS
→ 같은 Hop Pattern + Hop Timing

THSS
→ 같은 Time Hopping Pattern + Slot Timing
```



따라서 Synchronization이 틀어지면 Receiver가 원하는 Signal을 제대로 Despread하거나 추적하기 어렵다.

이 점은 앞서 전체 Digital Communication System에서 Synchronization이 중요한 이유와도 직접 연결된다.

---



## 38. Spread Spectrum과 Security

Spread Spectrum Signal은 넓은 Band에 Power가 분산되고 PN/Hopping Pattern을 사용하기 때문에 외부에서 Signal을 쉽게 탐지하거나 추적하기 어렵게 만들 수 있다.

하지만 여기서 중요한 점은

> **Spread Spectrum 자체는 Encryption이 아니다.**

즉



```text
Spread Spectrum
→ Interference/Jamming 저항성
→ Signal Detection을 어렵게 할 수 있음

Encryption
→ Message 내용의 기밀성 보호
```



로 목적이 다르다.

실제 Secure Communication에서는 두 기술을 함께 사용할 수 있다.

---



## 39. 전체 흐름

Spread Spectrum을 하나의 구조로 정리하면 다음과 같다.



```text
Original Data
      ↓
Spreading Rule
(PN / Frequency Hop / Time Hop)
      ↓
Spread Signal
      ↓
Physical Channel
      ↓
Noise
Interference
Jamming
Fading
      ↓
Synchronization
      ↓
Despreading
      ↓
Filtering / Detection
      ↓
Recovered Data
```



---



## 40. 세 방식의 핵심 직관

#### DSSS



```text
"Bit 하나를 여러 Chip으로 잘게 쪼개 넓게 펼친다."
```



#### FHSS



```text
"같은 Frequency에 계속 머물지 않고 계속 다른 Frequency로 이동한다."
```



#### THSS



```text
"언제 Signal을 보낼 것인지 계속 바꾼다."
```



이를 한 그림처럼 생각하면



```text
DSSS
→ Code 축에서 확산

FHSS
→ Frequency 축에서 이동

THSS
→ Time 축에서 이동
```



이다.

---



## 핵심 정리

| 개념 | 핵심 |
| --- | --- |
| Spread Spectrum | 원래보다 넓은 Bandwidth로 Signal을 확산 |
| PN Sequence | Noise처럼 보이지만 송수신기가 공유하는 결정론적 Sequence |
| Chip | PN Sequence의 최소 단위 |
| Chip Rate | 초당 Chip 수 |
| Processing Gain | 확산 전후 Bandwidth 또는 Chip/Bit Rate 비율 |
| DSSS | Data와 PN Sequence를 직접 결합하여 확산 |
| Spreading | Narrowband Data → Wideband Signal |
| Despreading | 동일 Code를 이용해 원래 Signal 복원 |
| FHSS | Carrier Frequency를 시간에 따라 Hopping |
| THSS | Transmission Time Slot을 Hopping |
| FDM | 사용자별 Frequency Band를 고정 할당 |
| CDMA | 서로 다른 Code를 사용하여 User 구분 |
| Hybrid SS | 여러 Spread Spectrum 방식 결합 |
| Synchronization | Code, Hop, Slot Timing을 맞추는 과정 |

---



## 중요한 관계식

DSSS에서



$$
R_b=\frac{1}{T_b}
$$





$$
R_c=\frac{1}{T_c}
$$



이고 일반적으로



$$
T_c\ll T_b
$$



이므로



$$
R_c\gg R_b
$$



이다.

Processing Gain은 개념적으로



$$
PG \approx \frac{W_{\text{spread}}}{W_{\text{data}}} \approx \frac{R_c}{R_b} \approx \frac{T_b}{T_c}
$$



로 이해할 수 있다.

---



## 한 문장으로 정리

Spread Spectrum은 **원래 정보 신호를 PN Code, Frequency Hopping 또는 Time Hopping을 이용하여 넓은 Resource 영역으로 확산해 전송하고, Receiver에서 동일한 확산 규칙과 Synchronization을 이용해 다시 복원함으로써 Interference, Jamming, Fading 등에 대한 강인성을 얻는 통신 기술**이다.

---

블록도 기준: [08. Frequency Spread](../../blocks/frequency-spreading.md) · [전체 로드맵](../../roadmap.md)
