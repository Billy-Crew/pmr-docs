# 가공·분배 함수

파이프라인 중단의 **가공함수(Process)**와 후단의 **분배함수(Distribute)**는 업무 로직이 실제로 사는 곳입니다. 모두 Moonscript 파일 단위로 관리·핫리로드됩니다.

## 가공함수 (Process)

수신 전문을 변환·집계하거나 새 전문을 생성합니다. 파일 접두어로 성격을 구분합니다.

| 접두어 | 성격 | 파일 예시 |
| --- | --- | --- |
| `lfn_` | 수신 전문 처리 (per-message) | `lfn_krx_fill.moon`, `lfn_krx_master.moon`, `lfn_krx_mkop.moon`, `lfn_event.moon`, `lfn_close.moon` |
| `lfn_bypass_` | 바이패스(원본 유지) 처리 | `lfn_bypass_issue_investor.moon`, `lfn_bypass_issue_stat.moon` |
| `gen_` | 파생 전문 생성 (집계·산출) | `gen_vi.moon`(VI), `gen_sum01.moon`, `gen_sector_investor.moon`, `gen_single_remain_quote.moon`, `gen_st_stock_quote.moon` |

## 공유메모리(SHM) 갱신과 process 액션

종목별 현재가·호가 등 상태는 **종목 공유메모리(SHM)**에 유지됩니다. `process` 액션이 가공 룰과 함께 SHM을 갱신하며, 후속 가공·분배는 이 상태를 참조합니다.

```lua
inbound: {
    act 'filter',  {offset:1,  length:2, filters:{'A3'}}
    act 'filter',  {offset:14, length:2, filters:{'G1'}}
    act 'modify',  {rule:'diff_a3_only'}    -- 직전가 대비 변동분만
    act 'process', {}                       -- SHM 업데이트 & 가공
    act 'emit'
}
```

*사용자 액션 예 — 필터 2단 → 변동분 가공 → 공유메모리 갱신 → 송출*

SHM의 이상 여부(통합/가공 이상, 메모리 이상)는 [PMR Control](control.md)의 종목 메모리 조회 화면에서 실시간 확인합니다.

## 분배함수 (Distribute)

어떤 목적지에 어떤 방식으로 내보낼지를 결정합니다. `destinate` 액션의 룰로 주입되며, 정적 라우트 맵과 업무 조건 룰을 겹쳐 쓸 수 있습니다.

```lua
act 'destinate', {rule:'rule_kskq_qf'}   -- 업무 조건 (예: 시장·보드별)
act 'destinate', {rule:'route_map'}      -- 정적 목적지 맵
```

!!! note "전문 재조립 패턴"
    "기간계가 필요로 하는 필드만 재조립해 새 전문으로 송신"과 같은 요구는 ① 송출용 전문 정의(spec) 작성 → ② 재조립 가공함수 작성 → ③ destinate/emit 연결로 해결합니다. 실사례 기준 공수: 채널 추가 1일 + 전문 정의·재조립 스크립트 3일.
