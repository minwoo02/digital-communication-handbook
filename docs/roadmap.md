# Learning Roadmap

[TOC]

정보원 → 송신기(1~10) → 채널(11) → 수신기(12~21) → 목적지 순서로 읽는다. 목차의 번호도 아래 블록도와 동일하다.

![디지털 통신 시스템 전체 블록도](assets/images/communication-system-block-diagram.png)

**필수·선택 표시는 첨부 블록도의 구분을 따른다.** 실제 시스템에서는 목적과 환경에 따라 선택 과정을 사용하거나 일부 기능을 통합한다. 상세 글이 없는 블록은 전체 구조 글의 해당 설명으로 연결한다.

## 송신기 · Transmitter

| 블록 | 과정 | 구분 |
| --- | --- | --- |
| 01 | [포맷팅 · Format](blocks/format-input.md) | 필수 |
| 02 | [소스 부호화 · Source encode](blocks/source-encoding.md) | 선택 |
| 03 | [암호화 · Encrypt](blocks/encryption.md) | 선택 |
| 04 | [채널 부호화 · Channel encode](blocks/channel-encoding.md) | 선택 |
| 05 | [다중화 · Multiplex](blocks/multiplexing.md) | 선택 |
| 06 | [펄스 변조 · Pulse modulate](blocks/pulse-modulation.md) | 필수 |
| 07 | [대역통과 변조 · Bandpass modulate](blocks/bandpass-modulation.md) | 필수 |
| 08 | [주파수 확산 · Frequency spread](blocks/frequency-spreading.md) | 선택 |
| 09 | [다중 접속 · Multiple access](blocks/multiple-access-transmit.md) | 선택 |
| 10 | [송신기 · XMT](blocks/transmitter.md) | 필수 |

## 채널 · Channel

| 블록 | 과정 | 구분 |
| --- | --- | --- |
| 11 | [채널 · Channel](blocks/channel.md) | 필수 |

## 수신기 · Receiver

| 블록 | 과정 | 구분 |
| --- | --- | --- |
| 12 | [수신기 · RCV](blocks/receiver.md) | 필수 |
| 13 | [다중 접속 · Multiple access](blocks/multiple-access-receive.md) | 선택 |
| 14 | [주파수 역확산 · Frequency despread](blocks/frequency-despreading.md) | 선택 |
| 15 | [복조 및 샘플링 · Demodulate & Sample](blocks/demodulation-and-sampling.md) | 필수 |
| 16 | [검파 · Detect](blocks/detection.md) | 필수 |
| 17 | [역다중화 · Demultiplex](blocks/demultiplexing.md) | 선택 |
| 18 | [채널 복호화 · Channel decode](blocks/channel-decoding.md) | 선택 |
| 19 | [복호화 · Decrypt](blocks/decryption.md) | 선택 |
| 20 | [소스 복호화 · Source decode](blocks/source-decoding.md) | 선택 |
| 21 | [포맷팅·정보 복원 · Format](blocks/format-output.md) | 필수 |

## 공통 과정 · Synchronization

[동기화: 송수신 전반의 타이밍·주파수·위상 기준 맞추기](blocks/synchronization.md)

## 목적지와 응용

[Bio/Implant Communication으로 연결하기](applications/bio-implant/bio-and-implant-communication.md)

## 개념 지도

블록도의 순서를 가지별로 따라가며 개념을 누르면 해당 단계로 이동한다. 화면보다 크면 좌우로 스크롤할 수 있다.

<nav class="handbook-concept-map" aria-label="개념별 관련 문서" tabindex="0"><ul><li><a href="theory/foundations/communication-system-overview.md">Digital Communication System</a><ul><li><a href="blocks/format-input.md">송신기 · 1~10</a><ul><li><a href="blocks/format-input.md">01. 포맷팅</a></li><li><a href="blocks/source-encoding.md">02. 소스 부호화</a></li><li><a href="blocks/encryption.md">03. 암호화</a></li><li><a href="blocks/channel-encoding.md">04. 채널 부호화</a></li><li><a href="blocks/multiplexing.md">05. 다중화</a></li><li><a href="blocks/pulse-modulation.md">06. 펄스 변조</a></li><li><a href="blocks/bandpass-modulation.md">07. 대역통과 변조</a></li><li><a href="blocks/frequency-spreading.md">08. 주파수 확산</a></li><li><a href="blocks/multiple-access-transmit.md">09. 다중 접속</a></li><li><a href="blocks/transmitter.md">10. 송신기</a></li></ul></li><li><a href="blocks/channel.md">채널 → 수신기 → 목적지</a><ul><li><a href="blocks/channel.md">11. 채널</a></li><li><a href="blocks/receiver.md">수신기 · 12~21</a><ul><li><a href="blocks/receiver.md">12. 수신기</a></li><li><a href="blocks/multiple-access-receive.md">13. 다중 접속</a></li><li><a href="blocks/frequency-despreading.md">14. 주파수 역확산</a></li><li><a href="blocks/demodulation-and-sampling.md">15. 복조 및 샘플링</a></li><li><a href="blocks/detection.md">16. 검파</a></li><li><a href="blocks/demultiplexing.md">17. 역다중화</a></li><li><a href="blocks/channel-decoding.md">18. 채널 복호화</a></li><li><a href="blocks/decryption.md">19. 복호화</a></li><li><a href="blocks/source-decoding.md">20. 소스 복호화</a></li><li><a href="blocks/format-output.md">21. 포맷팅·정보 복원</a></li></ul></li><li><a href="applications/bio-implant/bio-and-implant-communication.md">목적지·응용</a></li><li><a href="blocks/synchronization.md">공통·동기화</a></li></ul></li></ul></li></ul></nav>
