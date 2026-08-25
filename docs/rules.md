# 룰 (Rules)

룰은 액션에 주입하는 **이름 있는 로직 단위**입니다. 액션이 "무엇을 한다"라면 룰은 "어떻게 한다"를 담당하며, 여러 액션·토픽에서 재사용됩니다.

## 선언형 룰 참조

```lua
act 'modify',    {rule:'wrsec1'}            -- 미리 정의된 가공 룰
act 'destinate', {rule:'route_map'}         -- 라우트 맵 룰
act 'qos',       {rule:'rule_qos_fill_issue_1', ms:5}
```

## 함수형 룰 작성

룰은 Moonscript 함수로 직접 작성할 수 있습니다. `fn`은 메시지와 옵션을 받아 경로 지정·가공 등을 수행합니다. 프로그래머블 라우팅 룰 예:

```lua
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

*rules/rule_prog_routing.moon — 전문 바이트를 검사해 SET_ROUTE로 동적 경로 지정*

## 룰의 특징

- **런타임 교체** — 룰 파일 변경은 핫-리로딩으로 즉시 반영됩니다.
- **JIT 컴파일** — 자주 실행되는 룰은 LuaJIT Trace JIT에 의해 네이티브 코드로 변환됩니다.
- **C 데이터 직접 접근** — 룰 안에서 FFI로 전문 구조체 필드에 바로 접근합니다.

!!! note "Actions + Rules = 서비스"

    파이프라인의 뼈대(액션 순서)는 레이아웃이, 살(개별 로직)은 룰이 담당합니다. 요구사항 변경의 대부분은 룰 추가·수정만으로 끝납니다. 실제 공수 사례는 [확장 시나리오와 공수](estimate.md)를 보세요.
