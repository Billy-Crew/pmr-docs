# 전문 정의와 식별자

수신 파이프라인의 첫 관문은 "이 전문이 무엇인지"를 아는 것입니다. **전문 정의(Specifications)**가 구조를 선언하고, **식별자(Identifiers)**가 판별합니다.

## 식별자 (Identifiers)

수신 바이트 스트림에서 전문 종류를 판별하는 규칙입니다. 통상 전문 선두의 TR 코드 (예: `A301S` = KOSPI 체결, `A301Q` = KOSDAQ 체결)를 사용합니다. 식별 위치는 `identify: {offset, length}` 형식으로 지정합니다.

```lua
identify: {1, 5}                    -- 1번째 바이트부터 5바이트
identify: {offset:1, length:5}      -- 위와 완전히 동일
```

## 전문 정의 (Specifications)

식별된 전문의 필드 구조(레이아웃)를 선언합니다. 시장·전문 유형별로 하나의 `.moon` 파일로 관리하며, 신규 시장 추가는 대부분 spec 파일 추가로 끝납니다.

```text
specs/
 ├─ dst_fill_kospi.moon              -- KOSPI 체결 전문
 ├─ dst_fill_kosdaq.moon             -- KOSDAQ 체결 전문
 ├─ dst_event_info_kospi.moon
 ├─ dst_event_nextday_kospi.moon
 ├─ dst_event_nextday_kosdaq.moon
 ├─ dst_i6a2s_elw_event.moon         -- ELW
 ├─ dst_i6a3s_etf_event.moon         -- ETF
 ├─ dst_i6a4s_etn_event.moon         -- ETN
 ├─ dst_i6a5s_lc_event.moon
 └─ dst_i8a3s_etf_shortsell_info.moon
```

*전문 정의 파일 구성 예 — 파일명이 곧 전문의 정체성*

## 정의되지 않은 전문 (Not Defined)

어떤 식별자에도 매칭되지 않은 Raw 메시지는 유실되지 않고 별도로 기록됩니다. [PMR Control](control.md)의 증적로그 화면에서 **Not Defined 조회**로 확인하고, 신규 전문이면 spec을 추가해 즉시 대응합니다.

!!! note "신규 시장 추가 절차 = 설정 3종"

    ① 송수신 채널 추가(`dict_channels`) ② 전문 정의(spec) 추가 ③ 식별자(identify)·가공(process) 추가. NXT ETF 시장 추가가 이 절차로 단 수일에 완료된 사례는 [확장 시나리오와 공수](estimate.md)에 있습니다.
