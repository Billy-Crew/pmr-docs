# PROUTE 상세

**PROUTE(Programmable ROUTE)**는 PMR 콘텐츠 기반 라우팅(CBR)의 핵심 액션입니다. 메시지 내용을 식별해 목적지를 동적으로 결정하며, 4가지 모드를 제공합니다.

## 모드 1 — 다이나믹 라우팅 (routes 맵)

전문 선두 5바이트(TR 코드)를 식별키로 삼아 목적지를 맵으로 지정합니다.

```lua
act 'proute', {
    identify: {1, 5}
    routes:
        A301S: {to:'KS_f'}          -- KOSPI 체결 → KS_f
        A301Q: {to:'KQ_f'}          -- KOSDAQ 체결 → KQ_f
        A302S: {ignore: true}       -- 무시
        A302Q: {ignore: true}
        else:  {to:'kskq_etc'}      -- 그 외 전부
}
```

### 필터로 응용

`else`를 `ignore`로 두면 화이트리스트 필터가 됩니다.

```lua
act 'proute', {
    identify: {1, 5}
    routes:
        A301S: {to:'KS_f'}
        A301Q: {to:'KQ_f'}
        else:  {ignore: true}       -- 지정 전문 외 모두 차단
}
```

### identify 표기 동치

```lua
identify: {1, 5}
identify: {offset:1, length:5}      -- 둘은 동일하다
```

## 모드 2 — 해시 분산 라우팅

식별 구간(주로 종목코드)을 해싱해 목적지 개수로 mod 분산합니다. 파티셔닝·수평 분산에 사용합니다.

```lua
act 'proute', {
    identify: {1, 5}
    hash:     {method:'gstrhash'}   -- mod by #routes
    routes: {
        {to:'KS_f1'}
        {to:'KS_f2'}
        {to:'KS_f3'}
        {to:'KS_f4'}
    }
}
```

*종목코드에 대해 해싱하는 경우에 주로 사용 — Kafka의 key 기반 파티셔닝에 대응*

## 모드 3 — 프로그래머블 라우팅 (rule 함수)

라우팅 규칙 전체를 함수로 작성합니다. 임의 조건·복수 필드 조합이 가능합니다.

```lua
act 'proute', { rule: 'rule_prog_routing' }

-- rule: rule_prog_routing
{
    desc: '프로그래머블 라우팅, 임의 샘플'
    fn: (msg, opt) ->
        if (msg.data\sub 1,1) == 'A'
            SET_ROUTE msg, 'KS_f'
        elseif (msg.data\sub 3,5) == '01S'
            SET_ROUTE msg, 'KS_f_emit'
        else
            -- do nothing. ignored
        true
}
```

## 모드 4 — 혼합 (routes 값에 rule 지정)

맵 라우팅과 함수 라우팅을 키별로 섞을 수 있습니다.

```lua
act 'proute', {
    identify: {1, 5}
    routes:
        A301S: {to:'KS_f'}              -- 정적 라우팅
        A301Q: {rule:'custom_kq_f'}     -- 이 키만 별도 함수 적용
        A302S: {ignore: true}
        A302Q: {ignore: true}
        else:  {to:'kskq_etc'}
}
```

## 모드 선택 가이드

| 상황 | 권장 모드 |
| --- | --- |
| TR 코드별 고정 분기 | 모드 1 (routes 맵) |
| 화이트리스트 필터링 | 모드 1 + `else: ignore` |
| 종목코드 기준 수평 분산 | 모드 2 (hash) |
| 복수 필드·임의 조건 분기 | 모드 3 (rule 함수) |
| 대부분 고정 + 일부 특수 처리 | 모드 4 (혼합) |
