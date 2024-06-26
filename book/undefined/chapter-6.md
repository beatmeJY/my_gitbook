---
description: 레디스를 메시지 브로커로 사용하기
---

# Chapter 6

## 레디스의 pub/sub를 메시지 브로커로 사용하기

***

* 레디스는 아주 가벼운 pub/sub 기능을 제공한다.
* 레디스 노드에 접근할 수 있는 모든 클라이언트는 발행자와 구독자가 될 수 있다.
* **발행자**는 특정 채널에 `메시지를 보낼 수` 있고, **구독자**는 특정 채널을 리스닝하다 `메시지를 읽을 수` 있다.

### 메시지 publish 하기

```sql
PUBLISH world hello
```

* 이렇게 하면 world 라는 채널에 hello 라는 메시지가 전파된다.

### 메시지 구독하기

```sql
SUBSCRIBE event1 event2
> "subscribe"
> "event1"
> "subscribe"
> "event2"

PSUBSCRIBE event*
> "PSUBSCRIBE"
> "event*"
> (integer) 2
```

* **SUBSCRIBE:** 커맨드를 이용하면 특정 채널을 구독할 수 있다.
* **PSUBSCRIBE**: 패턴으로 특정 채널들을 구독할 수 있다.
  * 단, `SUBSCRIBE`으로 등록한 채널을 `PSUBSCRIBE`으로 등록한 채널이 중복되어 구독될 수 있다.
  * 위의 예로 event1에서 "hello"를 발행 시 각각 발행 받아 "hello"  **2번** 받게 된다.

### 클러스터 구조에서의 pub/sub

> 클러스터는 레디스가 자체적으로 제공하는 데이터 분산 형태의 구조이다.

* 레디스 클러스터에서 pub/sub를 사용할 때 메시지를 발행하면 해당 메시지는 클러스터에 속한 모든 노드에 자동으로 전달된다.
* 따라서 레디스 클러스터의 아무 노드에나 연결해 SUBSCRIBE 커맨드를 사용하면 데이터를 수신할 수 있다.
* 하지만, 클러스터의 주요 목적을 고려한다면 이는 불필요한 사용과 네트워크 부하가 발생할 수 있다.
  * 대부분클러스터는 대규모 서비스에서 데이터를 분산해서 저장하고 처리하기 위해 도입한다.

### sharded pub/sub

> 클러스터 pub/sub의 비효율적인 구조를 해결하기 위해 레디스 7.0부터 도입된 기능이다.
>
> 모든 노드로 전파되지 않기 때문에 불필요한 복제를 줄여 자원을 절약할 수 있다는 장점이 있다.

```sql
SPUBLISH apple a
> Redirected to slot [7092] located at 10.0.0.2:6379

SSUBSCRIBE apple
> Reading messages... (press Ctrl-C to quit)
> Redirected to slot [7092] located at 10.0.0.2:6379
> Reading messages... (press Ctrl-C to quit)
> "sscubscribe"
> "apple"
> (integer) 1
> "smessage"
> "apple"
> "a"
```

* **SPUBLISH**: 클러스터에서 키가 슬롯에 할당되는 것과 동일한 방식으로 채널이 할당되며, 같은 슬롯을 가지고 있는 노드 간에만 pub/sub 메시지를 전파한다.
* 해당 채널을 구독하는 특정 복제본에만 메시지가 전송된다.
* **SSUBSCRIBE**: 마찬가지로 특정한 서버에서만 수행될 수 있고, apple 키 값을 할당 받을 수 있는 슬롯을 포함한 마스터 노드에 연결 될 수 있도록 리다이렉트 된다.



## 레디스의 list를 메시징 큐로 사용하기

***

> list 는 큐로 사용하기 적절한 자료 구조이다.
>
> 레디스에는 LPUSH, LPOP, RPUSH, RPOP 커맨드가 존재하기 때문에 메시징 큐를 직접 구현 할 수 있다.

### list의 확장 기능 (SNS 유저별 타임라인)

```sql
RPUSHX Timelinecache:userB data3
> 26

RPUSHX Timelinecache:userC data3
> 5

RPUSHX Timelinecache:userD data3
> 0
```

* 각 타임라인 캐시에 데이터를 저장할 때 RPUSH 커맨드가 아닌 RPUSHX 커맨드를 사용한다.
  * 데이터를 저장하고자 하는 list가 이미 존재할 때만 아이템을 추가할 수 있다.
  * 이는 자주 SNS에 들어오지 않는 D 유저에 대해선 자동으로 타임라인을 관리하지 않아도 된다.

### list의 블로킹 기능

> 레디스를 이벤트 큐로 사용할 경우 블로킹 기능 또한 유용하게 사용할 수 있다.

* list의 블로킹 기능을 사용하면, 이벤트 기반 구조 시스템의 단점들을 줄일 수 있다.
  * 이벤트 기반 구조에서 시스템은 이벤트 루프를 돌며 신규로 처리할 이벤트가 있는지 체크한다.
  * 이러한 작업을 폴링이라고 하며, 반복하며 불필요하게 리소스가 소모될 수 있다.
  * 또한 폴링 인터벌 시간 동안을 대기한 뒤 다시 확인하기 때문에 즉시 처리할 수 없는 단점도 존재한다.

```sql
BRPOP queue:a 5
> "queue:a"
> "data"

BRPOP queue:a queue:b queue:c timeout 1000
> "queye:a"
> "DATA"
```

* **BRPOP/ BLPOP**:&#x20;
  * 각각 RPOP, LPOP에 블로킹을 추가한 커맨드이다.
  * 클라이언트가 BLPOP을 사용해 데이터를 요청했을 때 list에 데이터가 있으면 즉시 반환한다.
  * 만약 데이터가 없을 경우에는 list에 데이터가 들어올 때까지 기다린 후에 들어온 값을 반환하거나
  * 클라이언트가 설정한 타임아웃 시간만큼 대기한 후에 nil 값을 반환한다.
  * 위의 커맨드는 queue:a의 데이터가 입력될 때까지 최대 5초간 대기하고 없으면 nil을 반환한다.
    * (0으로 설정하면 무한정 대기한다)
  * 하나의 리스트에 대해 여러 클라이언트가 동시에 블로킹 될 수 있으며, 가장 먼저 요청을보낸 클라이언트가 데이터를 가져간다.
* 여러 queue를 설정하면 여러 queue들 중 하나의 큐라도 데이터가 들어올때까지 기다린 다음 하나의 리스트에 데이터가 들어오면 해당 값을 읽어온다.

### list를 이용한 원형 큐

```sql
LPUSH clist A

LPUSH clist B

LPUSH clist C

LRANGE clist 0 -1

RPOPLPUSH clist clist
> "A"

LRANGE clist 0 -1
> "A"
> "C"
> "B"
```

**RPOPLPUSH**: 만약 계속해서 반복 접근해야 하는 클라이언트라면 해당 명령어를 통해 꺼내면서 반대로 다시 집어넣을 수 있다.

## Stream

***

> 컴퓨터 과학에서 스트림이란 연속적인 데이터의 흐름 일정한 데이터 조각의 연속을 의미한다.

#### 스트림은 사용목적에 따라 크게 두 가지 방식으로 활용될 수 있다.

1. 백엔드 개발자들은 대량의 데이터를 효율적으로 처리하는 플랫폼으로 활용할 수 있다.
2. 데이터 엔지니어들은 여러 생산자가 생성한 데이터를 다양한 소비자가 처리할 수 있게 지원하는 데이터 저장소 및 중간 큐일 시스템으로 사용할 수 있다.

### 레디스 Stream과 아파치 카프카 관계

* 카프카는 링크드인에서 처음 개발된 `분산 메시징 시스템`이며 2011년에 오픈소스로 공개되어 현재는 Confluent에서 관리하고 있다.
* 레디스 stream은 카프카의 영향을 많이 받은 시스템인 만큼 카프카와 유사한 여러 기능을 갖고 있다.
* 일부는 카프카보다 뛰어난 처리를 할 수 있도록 설계되었다.

### 데이터의 저장

#### 메시지 저장과 식별

* 레디스의 stream에서는 각 메시지는 시간과 관련된 유니크한 ID를 가지며, 이 값은 중복되지 않는다.
  * `<millisecondsTime>-<sequenceNumber>`
  * **millisecondsTime**: 실제 stream에 아이템이 저장될 시점의 레디스 노드 로컬 시간이다.
  * **sequenceNumber**: 동일한 밀리세컨드 시간에 여러 아이템이 저장될 수 있으므로 같은 밀리세컨드에 저장된 데이터의 순서를 의미한다.

#### 스트림 생성과 데이터 입력

* 레디스에서는 따로 stream을 생성하는 과정은 필요하지 않으며,&#x20;
* 새로운 이름의 stream에 데이터를 저장하면 데이터의 저장과 동시에 stream 자료 구조가 생성된다.

```sql
XADD Email * subject "first" body "hello?"
> "1659114481311-0"
```

* **XADD**: 커맨드를 실행하면 Email 이라는 이름의 stream이 생성된다.
* 메시지는 키 값 쌍으로 저장되며 subject 키에 "first"가 저장되었고, body 키에 "hello?"가 저장되었다.
* 반환 값은 해당데이터의 ID를 반환한다.
* 이 때 \* 필드는 저장되는 데이터의 ID를 의미하며 \* 로 사용할 경우 자동 생성되는 타임스탬프 ID를 사용한다.
* ID를 직접 만들 경우 최소 값은 0-1 이며, 이후에 저장되는 stream은 이전에 저장된 ID값보다 커야한다.

#### 데이터의 조회

> 레디스는 데이터를 두가지 방식으로 읽을 수 있다.

1. **카프카처럼 실시간으로 처리되는 데이터를 리스닝**

```sql
XREAD BLOCK 0 STREAMS Email 0
> 1) "EMAIL"
> 1) 1) "1659114481311-0"
>    2) 1) "subject"
>       2) "first"
>       3) "body"
>       4) "hello?"
```

* 위와 같이 실행하면 Email에 저장된 데이터를 모두 읽어오고 이후 추가되는 데이터를 실시간으로 읽어올 수 있다.
* BLOCK 0:
  * 더 이상 stream에서 가져올 데이터가 없더라도 연결을 유지하는 시간이다.
  * 0으로 설정하면 계속 리스닝하라는 의미이다.
* STREAMS Email 0:
  * Email이라는 stream에서 저장된 데이터 중 ID가 0보다 큰 값을 읽어오라는 의미이다.

2. **ID를 이용해 필요한 데이터를 검색**

```sql
XRANGE key start end [COUNT count]
XREVRANGE key start end [COUNT count]

XRANGE email (1659114481311-0 + COUNT 5
> 1659114481311 이후부터 ~ 끝까지 중 5개 결과 출력
```

* **XRANGE**: ID를 이용해 원하는 시간대의 데이터를 조회할 수 있다.
* `XREAD`와의 차이점은 XRANGE는 해당 시점 모든 값을 조회한 후 이후 추가된 값은 가져오지 않는다.
* `-`는 가장 오래된 ID를 `+`는 가장 최근의 ID로 표시할 수 있다.
* **XREVRANGE**: XRANGE와 동일하게 역순으로 조회한다.
* `(` 를 사용하여 해당 ID 다음 데이터부터 조회할 수도 있다.
* `COUNT` 옵션을 이용해서 데이터의 개수를 제한하는 것도 가능하다.

#### 소비자와 소비자 그룹

> 이번엔 stream 데이터를 여러 소비자가 읽어가는 상황에 대해 조금 더 알아보자.

* 같은 데이터를 여러 소비자에게 전달하는 것을 `팬아웃` 이라고 한다.
* 레디스에서는 `XREAD`를 여러 소비자가 수행한다면 팬아웃이 가능하다.

#### 소비자 그룹

```sql
XGROUP CREATE Email EamilServiceGroup $
XREADGROUP GROUP EamilServiceGroup emailService1 COUNT 1 STREAMS Email >
```

* **XGROUP**:&#x20;
  * Stream Email을 읽어가는 EmailServiceGroup을 만든다.
  * `$`는 현재 시점 이후의 데이터부터 리스닝 한다는 것이다.
* **XREADGROUP**:
  * `XREAD`와 같은 형태로 데이터를 응답하지만 지정한 소비자 그룹을 통해 데이터를 읽는다.
  * 위는EmailServiceGroup에 속한 emailService1 라는 이름의 소비자로 Email 에 있는 1개의 메시지를 읽어오고자 하는 커맨드이다.
  * 만약 다른 소비자에게 읽히지 않는 데이터가 있다면 가져오고 없다면 nil을 반환한다.
  * `>` 는다른 소비자에게 전달되지 않았던 새로운 메시지를 전달하라는 것을 의미한다.
    * 해당 소비자 그룹을 사용하는 이유는 다른 사용자에게 읽히지 않는 데이터를 가져오기 위함이기에 대부분의 상황에선 `>` 을 사용하면 되겠지만
    * 만약 0 또는 다른 숫자 ID를 입력할 경우 새로운 메시지가 아닌 입력한 ID보다 큰 ID 중 읽히지 않고 대기에 속하던 메시지를 반환한다.

#### ACK와 보류 리스트

> 여러 서비스가 메시지 브로커를 이용해 데이터를 처리할 때 예상치 못한 장애로 시스템이 종료되었을 경우 이를 인지하고 재처리할 수 있는 기능이 필요하다.

* 레디스 stream에서 소비자 그룹에 속한 소비자가 메시지를 읽어가면 다음과 같은 행동을 한다.
  1. 각 소비자 별로 읽어간 메시지에 대한 ID 리스트를 새로 생성한다. (소비자 별  보류 리스트 존재)
  2. 마지막으로 읽어간 데이터의 ID로 last\_delivered\_id를 업데이트 한다. (Stream 당 하나)\\
* 만약 서버 1에서 데이터를 130-1을 읽었다면 서버 1의 보류 리스트에 130-1이 저장되고,
* 이후 서버 1로부터 ACK 메시지를 받았다면 서버 1의 보류 리스트에서 130-1을 삭제한다.

```sql
XPENDING Email EmailServiceGroup
> 9
> "1659114481311-0"
> "1659170735630-0"
> 1) 1) "es1"
>    2) "1"
> 2) 1) "es2"
>    2) "1"
> 3) 1) "es3"
>    2) "7"
```

* **XPENDING**: 해당 커맨드를 통해 현재 소비자 그룹에서 보류 중인 리스트가 있는지 확인 할 수 있다.

```sql
XACK Email EmailServiceGroup "1659114481311-0"
> 1

XPENDING Email EmailServiceGroup
> 8
> "1659114481311-0"
> "1659170735630-0"
> 1) 1) "es2"
>    2) "1"
> 2) 1) "es3"
>    2) "7"
```

* **XACK**: 해당 커맨드를 이용해 데이터가 처리 되었음을 알려줄 수 있다.

#### 메세지 시스템 보증 전략

1. **at most once**:&#x20;
   * 메시지를 받자마자 실제 처리하기 전 먼저 ACK를 보낸다.
   * ACK를 보낸 후 소비자에 문제가 생겨 다운될 경우 데이터를 잃을 수 있다.
   * 메시지가 일부 손실되더라도 빠른 응답이 필요할 경우 선택하는 전략이다.
2. **at least once**:
   * 소비자가 받은 메시지를 모두 처리한 후 ACK를 보낸다.
   * 2개의 메시지를 받아 1개만 처리할 경우 ACK를 보내지 못하고 다시 받아오게 되는데
   * 이 경우, 이미 처리 된 건이 ACK 되지 않고 다시 받아 오게 되어 멱등성이 보장 되지 않을 수 있다.
3. **exactly once**:&#x20;
   * 모든 메시지는 무조건 한 번씩 전송되는 것을 보장한다.
   * 레디스의 stream을 이용하며 해당 전략대로 전송하고 싶다면, set등 추가 자료 구조를 이용해 이미 처리된 메시지인지 확인하는 추가 과정이 필요할 수 있다.

#### 메시지의 재할당

> 소비자 서버에 장애가 발생해 복구 되지 않는다면 다른 소비자가 대신 처리해야 한다.

```sql
XCLAIM <key> <group> <consumer> <min-idle-time> <ID-1> <ID-2> ... <ID-N>

EmailService 1: XCLAIM Email EmailServiceGroup EmailService3 360000
```

* **XCLAIM**:&#x20;
  * 메시지가 보류 상태로 머무른 시간이 최소 대기 시간을 초과한 경우에만 소유권을 변경할 수 있도록해서 같은 메시지가 2개의 다른  소비자에게 중복으로 할당되는 것을 방지할 수 있다.
* 위의 예제는 EmailService 1이 문제가 발생한 EmailService3 소비자의 보류 상태로 머무른 시간이 최소 360,000초를 넘겼을 경우 가져가는 커맨드이다.

#### 메시지의 자동 재할당

> `XPENDING`을 사용해 보류 중인 메시지를 확인하고 `XCLAIM` 명령을 통해 메시지를 다시 할당하는 것은 번거로울 수 있다. 이를 자동으로 가져와서 처리할 수도 있다.

```sql
XAUTOCLAIM <key> <group> <consumer> <min-idle-time> <start> [COUNT count] [JUSTID]

XAUTOCLAIM Email EmailServiceGroup es1 360000 0-0 count 1
> "1659170655277-0"
> 1) 1) "1659114966798-0"
>    2) 1) "subject"
>       2) "second"
>       3) "body"
>       4) "hihi"
```

* **XAUTOCLAIM**: 할당 대기 중인 다음 메시지의 ID를 반환하는 방식으로 동작하기 때문에 반복적 호출을 가능하게 한다.
* 위의 예제에서는 첫 번째 반환 값으로 다음으로 대기 중인 보류 메시지의 ID가 반환된다.
* 더 이상 대기 중인 보류 메시지가 없을 경우 0-0이 반환된다.
* 두 번째 반환 값은 소유권이 이전되 메시지의 정보를 제공한다.
* 내부 필드-값 쌍이 순서대로 포함되며, 이 메시지의 소유권은 es1 에게로 할당 되었다.

#### 메시지의 수동 재할당

* stream 내의 각 메시지는 `counter`라는 값을 각각 가지고 있다.
  * `XREADGROUP`을 이용해 소비자에게 할당하거나 `XCLAIM` 커맨드로 재할당할 경우 1씩 증가한다.
* 만약 메시지에 문제가 있어 처리되지 못할 경우 여러 소비자에게 할당되기를 반복하면서 `counter` 값이 계속 증가하게 된다.
* 따라서 `counter` 가 특정 값에 달하게 되면 특수한 다른 stream으로 보내 관리자가 추후에 처리할 수 있도록 하는 것이 좋을 수 있다.
* 보통 이런 메시지를 `dead letter`라고 부른다.

#### Stream 상태 확인

> 일반적인 메시징 시스템이 그렇듯 어떤 소비자가 활성화 됐는지 보류된 메시지는 어떤 건지, 어떤 소비자 그룹이 메시지를 처리하고 있는지 등의 상태를 확인하는 커맨드가 없다면 stream을 관리하기 까다로울 것이다.

```sql
XINFO HELP
```

* **XINFO HELP**: stream의 여러 상태를 확인할 수 있는 커맨드들을 확인할 수 있다.

```sql
XINFO consumers Email EmailServiceGroup
> 1) 1) "name"
>    2) "es1"
>    3) "pending"
>    4) (integer) 1
>    5) "idle"
>    6) (integer) 650129
> 2) 1) "name"
>    2) "es2"
>    3) "pending"
>    4) (integer) 0
>    5) "idle"
>    6) (integer) 437738623
> 3) 1) "name"
>    2) "es3"
>    3) "pending"
>    4) (integer) 7
>    5) "idle"
>    6) (integer) 858725
```

* **XINFO consumers**: 특정 소비자 그룹에 속한 소비자의 정보를 알 수 있다.

```sql
XINFO GROUPS Email
> 1) 1) "name"
>    2) "bigroup"
>    3) "consumers"
>    4) (integer) 1
>    5) "pending"
>    6) (integer) 6
>    7) "last-delivered-id"
>    8) "1659170733830-0"
>    9) "entries-read"
>   10) (integer) 6
>   11) "lag"
>   12) (integer) 4
> 2) 1) "name"
>    2) "EmailServiceGroup"
>    3) "consumers"
>    4) (integer) 3
>    5) "pending"
>    6) (integer) 8
>    7) "last-delivered-id"
>    8) "1659170735630-0"
>    9) "entries-read"
>   10) (integer) 10
>   11) "lag"
>   12) (integer) 0
```

* **XINFO GROUPS**: stream에 속한 전체 소비자 그룹 list를 볼 수 있다.

```sql
XINFO STREAM email
>  1) "length"
>  2) (integer) 10
>  3) "radix-tree-keys"
>  4) (integer) 1
>  5) "radix-tree-nodes"
>  6) (integer) 2
>  7) "last-generated-id"
>  8) "1659170735630-0"
>  9) "max-deleted-entry-id"
> 10) "0-0"
> 11) "entries-added"
> 12) (integer) 10
> 13) "recorded-first-entry-id"
> 14) "1659114481311-0"
> 15) "groups"
> 16) (integer) 3
> 17) "first-entry"
> 18) 1) "1659114481311-0"
>     2) 1) "subject"
>        2) "hello" 
>        3) "body" 
>        4) "hi" 
```

* **XINFO STREAM**: Stream 자체의 정보를 알 수 있다.
* stream이 내부적으로 어떻게 인코딩되고 있는지 알 수 있다.
* 첫 번째와 마지막 메시지의 ID를 표시한다.
