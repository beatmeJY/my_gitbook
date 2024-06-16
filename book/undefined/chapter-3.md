# Chapter 3

## 레디스의 자료 구조

### String

> 레디스에서 키와 실제 값이 일대일로 연결되는 유일한 자료 구조이다.

<pre class="language-sql"><code class="lang-sql">SET hello world
GET hello
<strong>> "world"
</strong></code></pre>

* **SET**: `hello` Key에 `world` Value를 저장하였다.
* **GET**: hello Key로 검색하여 world의 값을 얻는다.

```sql
SET hello world1 NX
GET hello
> "world"
SET hello world2 XX
GET hello
> "world2"
```

* **NX**: 키에 이미 값이 있을 경우 저장되지 않는다.
* **XX**: 반대로 키가 있을때에만 새로운 값으로 덮어쓴다.

```sql
SET counter 10
INCR counter
> 11
INCR counter
> 12
INCRBY counter 20
> 32
DECR counter
> 31
DECRBY counter 10
> 21
```

String 자료 구조에서도 숫자 형태의 데이터라면 숫자를 조작할 수 있다.

> 해당 커맨드들을 이용하면 데이터를 읽고 -> 증가하고 -> 저장하는 일련의 과정이 모두 원자적으로 발생한다. 이는 원자적으로 처리함으로써 여러 클라이언트가 경쟁 상태를 발생 시킬 일이 없다.

* **INCR**: 해당  counter Key의 값에 1을 더한다.
* **INCRBY:** 입력한 숫자만큼 해당 Key의 값에 더한다.
* **DECR:** 해당 counter Key의 값에 1을 뺀다.
* **DECRBY**: 입력한 숫자만큼 해당 Key의 값에 뺀다.

```sql
MSET a 10 b 20 c 30
MGET a b c
> "10"
> "20"
> "30"
```

* **MSET**: 한번에 여러개를 저장할 수 있다.
* **MGET:** 한번에 여러개를 조회할 수 있다.

### List

> 레디스에서 List는 순서를 가지는 문자열 목록이다.
>
> 하나의 list에는 최대 42억여 개의 아이템을 저장할 수 있고, 직접 인덱스를 이용해 접근 할 수도 있다.

```sql
LPUSH myList B A
RPUSH myList D C
LRANGE myList 0 -1
> "A"
> "B"
> "D"
> "C"
LPOP myList 2
> "A"
> "B"
LTRIM myList 0 1
LINSERT myList BEFORE 변경할_데이터 변경_될_데이터
LINDEX myList 2
LSET myList 2 변경_될_데이터 
```

* **LPUSH**: 리스트의 왼쪽에 추가한다.
* **RPUSH**: 리스트의 오른쪽에 추가한다.
* **LRANGE**: 주어진 인덱스 사이의 리스트를 조회합니다. (음수-1은 맨 뒤, -2는 맨 뒤에서 하나 앞)
* **LPOP**: 리스트의 가장 앞 데이터를 삭제하고 반환합니다. (반복 숫자 지정 시 반복)
* **LTRIM**: 지정한 인덱스 범위 이외의 데이터를 삭제하며 반환하지는 않습니다.
* **LINSERT**: 데이터의 앞이나 뒤에 데이터를 추가할 수 있다.
* **LINDEX**: 원하는 인덱스의 데이터를 확인할 수 있다.
* **LSET**: 지정한 인덱스의 데이터를 신규 입력하는 데이터로 덮어쓴다.

### Hash

> 레디스에서 Hash는 필드-값 쌍을 가진 아이템의 집합이다.
>
> 하나의 hash 내에서 아이템은 필드-값 쌍으로 저장된다.

```sql
HSET Product:123 Name "Spring" Email "test@gitbook.com"
HSET Product:123 Gender "MAN"
HSET Product:234 Name "JAVA" Gender "WOMAN"

HGET Product:123 Name
> "Spring"
HMGET Product:234 Name Gender
> "JAVA"
> "WOMAN"
HGETALL Product:123
> "Name"
> "Spring" 
> "Email"
> "test@gitbook.com"
> "Gender"
> "MAN"
```

* **HSET**: 하나의 Hash에 필드-값으로 저장한다.
* **HGET**: 하나의 Hash내에서 하나의필드를 조회한다.
* **HMGET**: 하나의 Hash 내에서 여러 컬럼을 한 번에 조회할 수 있다.
* **HGETALL**: 해당 Hash의 모든 필드-값을 번갈아 모두 출력한다.

### Set

> 레디스에서 Set은 정렬되지 않은 문자열 모음이다.
>
> 하나의 Set안에선 아이템은 중복해서 저장되지 않으며 집합 연산과 관련된 커맨드를 제공한다.

```
SADD mySet A
SADD mySet A A C B D D C

SMEMBERS mySet
> "A"
> "C"
> "D"
> "B"

SREM mySet B
SPOP mySet
> "E"
```

* **SADD**: Set에 아이템을 저장할 수 있다.
* **SMEMBERS**: 해당 Set에 모든 원소를 출력한다. (순서는 랜덤)
* **SREM**: 지정한 문자를 삭제할 수 있다.
* **SPOP**: 랜덤으로 하나의 아이템을 삭제하고 반환한다.

```
SINTER set:111 set:222
SUNION set:111 set:222
SDIFF set:111 set:222
```

* **SINTER**: 교집합을 수행할 수 있다. (A ∩ B)
* **SUNION**: 합집합을 수행할 수 있다. (A ∪ B)
* **SDIFF**: 차집합을 수행할 수 있다. (A − B)

### Sorted Set

> 레디스에서 Sorted Set은 스코어 값에 따라 정렬되는 고유한 문자열의 집합이다.
>
> 모든 아이템은 스코어-값 쌍을 가지며, 저장될 때부터 스코어 값으로 정렬되어 저장된다.
>
> 동일한 스코어를 가질 경우 데이터의 사전 순으로 정렬된다.

```sql
ZADD score:240610 100 user:B
ZADD score:240610 150 user:A 150 user:C 200 user:D
```

* **ZADD**: Sorted Set에 스코어-값 쌍으로 저장한다.
  * **XX**: 아이템이 이미 존재할 떄에만 스코어를 업데이트 한다.
  * **NX**: 아이템이 존재하지 않을 때에만 신규 삽입하며 기존 아이템 스코어를 업데이트 하지 않는다.
  * **LT**: 업데이트하고자 하는 스코어가 기존 아이템의 스코어보다 작을 때에만 업데이트 한다. (만약 기존에 아이템이 존재 하지 않는다면 새로 삽입한다.)
  * **GT**: 업데이트하고자 하는 스코어가 기존 아이템의 스코어보다 클 때에만 업데이트 한다. (만약 기존에 아이템이 존재 하지 않는다면 새로 삽입한다.)

```sql
ZRANGE score:240610 1 2 WITHSCORES
> "user:A"
> "user:C"
ZRANGE score:240610 1 2 WITHSCORES REV (역순)
> "user:C"
> "user:A"

ZRANGE score:240610 100 150 BYSCORE WITHSCORES
ZRANGE score:240610 200 +inf BYSCORE WITHSCORES

ZADD mySortedSet 0 a 0 b 0 c 0 d 0 e
ZRANGE mySortedSet (b [e BYLEX
> b
> c
> d
```

* **ZRANGE**: Sorted Set에 저장된 데이터를 조회할 수 있으며 start와 stop 범위를 항상 입력해야 한다.
  * **인덱스로 데이터 조회**:&#x20;
    * WITHSCORES 를 사용하면 데이터와 스코어 값이 순서대로 출력된다.
  * **스코어로 데이터 조회**:&#x20;
    * BYSCORE 를 사용하여 위의 예시에선 100 이상 150 이하인 값을 조회하라는 것을 의미한다.
    * `(`를 포함하면 해당 값은 포함하지 않는 값만 조회한다.&#x20;
      * `100 150`일 경우 100 초과 150 이하
    * \+inf, -inf 값을 이용해 \~보다 큰값 \~ 보다 작은 값을 출력할 수 있다.&#x20;
      * `200 +inf`일 경우 200 이상의 값 모두 조회.
      * `-inf +inf` 일 경우 모든 데이터 조회.
  * **사전순으로 데이터 조회**:&#x20;
    * BYLEX 를 사용하면 사전식 순서를 이용해 특정 아이템을 조회할 수 있다,
    * BYLEX에선 `(`가 해당 문자를 포함하며 `[`가 해당 문자를 포함하지 않을 때 사용한다.
    * 가장 첫 문자는 `-`로 가장 마지막 문자는 `+`로 대체할 수 있다.
    * 문자열은 ASCII 바이트 값에 따라 사전식으로 정렬되기 때문에 한글도 가능하다.
* **ZREVRANGE:** ZRANGE와 반대로 내림차순으로 조회한다.
* **ZINCRBY**: `INCREBY` 커맨드와 비슷하게 동작하여 스코어를 입력한 만큼 증가시킬 수도 있다.

```
ZUINONSTORE <생성할 키 이름><합산할 키 개수><합산할 키>

ZUINONSTORE weekly-score:2406-3 7 
daily-score:240617 daily-score:240618 daily-score:240619 daily-score:240620 
daily-score:240621 daily-score:240622 daily-score:240623
```

* **ZUINONSTORE**: 지정한 키에 연결된 각 아이템의 스코어를 합산하는 커맨드이다.
  * 위와 같인 7일간의 주간 스코어를 각 아이템(유저) 별로 점수를 합산할 때 사용할 수 있다.



### 비트맵

> String 자료 구조에 bit 연산을 수행할 수 있도록 확장한 형태이다.
>
> 때문에 2³² 의 비트를 가지고 있는 비트맵 형태라고 볼 수 있다.

```sql
SETBIT mybitmap 2 1
GETBIT 2
> 1
BITFIELD mybitmap SET u1 6 1  SET u1 10 1
BITFIELD mybitmap GET u1 6 GET u1 7
> 1
> 0
BITCOUNT mybitmap
> 3
BITPOS mybitmap 1 2 7
> 추가
```

* **SETBIT**:  2번째 비트에 1의 값을 조회한다.
* **GETBIT**: 2번째 비트 값을 조회한다.
* **BITFIELD**: 한번에 여러 비트를 다룰 수 있다. u1은 1비트를 u4면 4비트를 사용한다.
* **BITCOUNT**: 1로 설정된 비트의 개수를 카운팅 할 수 있다.
* **BITOP**: 여러 개의 비트맵 간의 비트 연산을 수행합니다. (AND, OR, XOR, NOT)
* **BITPOS**: 비트맵에서 특정 비트 값이 처음으로 등장하는 위치를 찾습니다.

### Hyperloglog

> 집합의 원소 개수인 카디널리티를 추정할 수 있는 자료 구조이다.

```
PFADD members 123 200 300 11 200
PFCOUNT members
> 4
```

* PFADD: Hyperloglog에 아이템을 저장한다.
* PFCOUNT: 저장된 아이템 개수(카디널리티)를 추정할 수 있다.

#### 📚 참고자료

* [https://d2.naver.com/helloworld/711301](https://d2.naver.com/helloworld/711301)

### Geospatial

> 경도 위도 데이터 쌍의 집합으로 간편하게 지리 데이터를 저장할 수 있다.

<pre class="language-sql"><code class="lang-sql"><strong>GEOADD travel 127.0016985 37.5642135 Seoul -122.43454762275572 37.78530362582044 SanFrancisco
</strong>GEOPOS travel Seoul
> "127.0016985"
> "37.5642135"
GEODIST travel Seoul SanFrancisco km
> "9028.3077"
GEOSEARCH travel FROMLONLAT 126 37 BYRADIUS 1000 km COUNT 100
> "Seoul"
</code></pre>

* **GEOADD**: 경도 위도 아이템명을 순서대로 저장한다.
* **GEOPOS**: 등록한 아이템을 찾는다.
* **GEOSEARCH**: 특정 위치를 기준으로 원하는 거리 내에 있는 아이템을 검색할 수 있다.
  * FROMLONLAT: 좌표를 기준으로 검색할 경우 사용된다.
  * FROMMEMBER: 도시를 기준으로 검색할 경우 사용된다.
* BYBOX: 추후 보강&#x20;
* BYRADIUS: 추후 보강

### Stream

> 레디스를 메시지 브로커로서 사용할 수 있게 하는 자료 구조이다.
>
> 전체적인 구조는 카프카에서 영향을 받아 만들어 졌으며 소비자 그룹 개념을 도입하여 데이터를 분산 처리할 수 있는 시스템이다.



## 레디스에서 키를 관리하는 방법

***

### 키의 자동 생성과 삭제

> Stream이나 set, sorted set, hash와 같이 하나의 키가 여러 개의 아이템을 가지고 있는 자료 구조에서는 명시적으로 키를 생성하거나 삭제하지 않아도 키는 알아서 생성되고 삭제된다.

* 키가 존재하지 않을 때 아이템을 넣으면 아이템을 삽입하기 전에 빈 자료 구조를 생성한다.
* 모든 아이템을 삭제하면 키도 자동으로 삭제된다.
* 키가 없는 상태에서 키 삭제, 아이템 삭제, 자료 구조 크기 조회 같은 읽기 전용 커맨드를 수행하면 에러 대신 아이템이 없는 것처럼 동작한다.

```sql
DEL myList
> (integer) 0
```

### 키 조회 커맨드

#### EXISTS

> 키가 존재하는지 확인하는 커맨드이다.

```sql
EXISTS key [key...]
```

* 키가 존재하면 1, 존재하지 않으면 0 을 반환한다.

#### KEYS

> 레디스에 저장된 모든 키를 조회하는 커맨드이다.

```sql
KEYS pattern
```

* 매칭되는 패턴에 해당하는 모든 키의 list를 반환한다.
* 예를 들어 h?llo에는 hello, hallo 등이 매칭될 수 있다.
* 모든 키의 정보를 반환하므로 싱글 스레드로 동작하는 레디스에선 아주 **주의해서 사용**해야 한다.
  * (되도록 이면 사용하지 말고, 아래의 `SCAN` 명령어를 사용하자.)

#### SCAN

> SCAN 커맨드는 커서를 기반으로 특정 범위의 키만 조회할 수 있어 비교적 안전하게 사용할 수 있다.
>
> 때문에 **KEYS**를 **대체**해 키를 조회할 때 사용할 수 있다.

```
SCAB cursor [MATCH pattern] [COUNT count] [TYPE type]
```

* 첫 번째 반환되는 값은 다음 SCAN을 사용할 때의 cursor 위치이다.
* `COUNT`옵션을 사용하면 기본적으로 한번에 반환되는 키의 개수를 지정할 수 있다.
  * 하지만 정확하게 10개만 가져오는 것은 아니며 데이터가 저장된  형상에 따라 1 \~ 2개의 키를 더 읽어 올 수 도 있다.
*



