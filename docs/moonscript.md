# Moonscript 기초

PMR의 레이아웃·룰·가공함수는 **Moonscript**로 작성합니다. Lua로 컴파일되는 간결한 문법의 언어로, CoffeeScript와 Lua의 중간쯤에 있습니다.

## 레퍼런스와 에디터 지원

- 공식 문서: [moonscript.org](https://moonscript.org/)
- VIM 확장: [leafo/moonscript-vim](https://github.com/leafo/moonscript-vim)
- VS Code 확장: Marketplace의 `vgalaktionov.moonscript`

## 1. 할당과 연산

```moon
hello = "world"
a, b, c = 1, 2, 3
hello = 123        -- 위의 hello를 덮어씀

x = 0
x += 10            -- x = x + 10
s = "hello "
s ..= "world"      -- 문자열 연결 후 대입
b = false
b and= true or false
```

## 2. 제어문

```moon
have_coins = false
if have_coins
  print "Got coins"
else
  print "No coins"

-- 한 줄 if 는 then 사용
if have_coins then "Got coins" else "No coins"

-- unless 는 if 의 반대
unless os.date("%A") == "Monday"
  print "It is not Monday!"

-- if / unless 는 표현식으로도 사용 가능
is_tall = (name) -> if name == "Rob" then true else false
message = "I am #{if is_tall "Rob" then "very tall" else "not so tall"}"

-- 라인 데코레이터: 문장 뒤에 조건 배치
print("It IS Monday!") if is_monday
print("It is not Monday..") unless is_monday
```

## 3. 루프

```moon
for i = 1, 10
  print i

for i = 10, 1, -1 do print i   -- 한 줄 루프는 do 사용

i = 0
while i < 10
  continue if i % 2 == 0       -- continue 로 건너뛰기
  print i

-- 루프를 표현식으로 쓰면 배열 테이블 생성
my_numbers = for i = 1, 6 do i          -- {1,2,3,4,5,6}

-- 리스트 컴프리헨션
filter_odds = (t) ->
  return for x in *t
    if x % 2 == 0 then x else continue
evens = filter_odds(my_numbers)          -- {2,4,6}
```

## 4. 스위치

```moon
name = "Dan"
switch name
  when "Dave"
    print "You are Dave."
  when "Dan"
    print "You are not Dave, but Dan."
  else
    print "You are neither Dave nor Dan."

-- 표현식·복수 값 매칭
b = 4
next_even = switch b
  when 1 then 2
  when 2, 3 then 4
  when 4, 5 then 6
  else error "I can't count that high!"
```

## 5. 스코프

```moon
-- 모든 값은 기본 local. export 로 전역 선언
export var_1, var_2
var_1, var_3 = "hello", "world"   -- var_3 은 local

-- do 블록으로 수동 스코프 생성 (클로저)
counter = do
  i = 0
  ->
    i += 1
    return i
print counter!   -- 1
print counter!   -- 2

-- local 키워드로 선-선언 / 섀도잉
local var_4
if something
  var_4 = 1
print var_4      -- if 스코프가 아닌 현재 스코프에 설정되어 동작
```

!!! note "레이아웃 파일도 결국 Moonscript 테이블입니다"

    `KS_f: recv2r: {…} topics: inbound: {…}` 구조는 중첩 테이블 리터럴이며, 따라서 조건문·루프·함수로 **설정 자체를 생성**할 수도 있습니다.
