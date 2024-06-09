# Chapter 3

## 레디스의 자료 구조

### String

> 레디스에서키와 실제 값이 일대일로 연결되는 유일한 자료 구조이다.

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

ZRANGE score:240610 100 150 BYSCORE
```

* ZRANGE: Sorted Set에 저장된 데이터를 조회할 수 있으며 start와 stop 범위를 항상 입력해야 한다.
  * 인덱스로 데이터 조회:&#x20;
  * 스코어로 데이터 조회:&#x20;
  * 사전순으로 데이터 조회:&#x20;

### 비트맵

### Hyperloglog

### Geospatial

### Stream
