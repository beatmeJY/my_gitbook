---
description: 레디스 데이터 백업 방법
---

# Chapter 7

> 레디스에서는 RDB와 AOF 두 가지의 백업 방식을 지원한다.

```
SET key1 a
SET key1 apple
SET key2 b
DEL key2
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt="" width="563"><figcaption><p>AOF와 RDB 저장 차이</p></figcaption></figure>

* AOF 파일에는 레디스에서 실행된 모든 쓰기 작업이 기록된다.
* RDB 파일에는 저장되는 시점의 메모리 데이터가 그대로 저장된다.
* 레디스가 재시작 될 때 RDB와 AOF 파일 이 모두 존재할 경우, AOF이 더 안전하다고 판단하여 AOF를 로드한다.
* 하지만 RDB 만큼의 안정성을 원한다면 두가지 백업 방식을 모두 사용하는 것을 권장한다.

## RDB 방식의 데이터백업

***

> 일정 시점에 메모리에 저장된 데이터 전체를 저장하는 방식이다.

* 레디스를 백업하기 가장 단순한 방법이다.
* 특정 시점에 메모리 자체를 스냅샷 찍듯 저장할 수 있기 때문에 백업에 적합한 파일 형태이다.

### 특정 조건에 자동으로 RDB 파일 생성

```sql
save <초> <기간 내 변경된 키의 개수>
dbfilename <RDB 파일 이름>
dir <RDB 파일이 저장될 경로>
```

* 레디스의 설정파일에서 save 옵션을 사용해 원하는 조건에 RDB 파일을 저장하도록 설정할 수 있다.
* 파일 이름의 기본 값은 dump.rdb 이다.

```sql
save 900 1
save 300 10
save 60 1000
```

* 900초 동안 1개 이상의 키가 변경된 경우
* 300초 동안 10개 이상의 키가 변경된 경우
* 60초 동안 1000개 이상의 키가 변경된 경우

```sql
CONFIG GET save
> "save"
> "900 1 300 10 60 1000"

CONFIG SET save ""
> OK

CONFIG GET save
> "save"
> ""

CONFIG REWRITE
> OK
```

* CONFIG GET: 현재 적용된 save 옵션을 확인 할 수 있다.
*   CONFIG SET:  save옵션을 변경 할 수 있다.

    * ""로 설정하면 RDB 저장을 비활성화 할 수 있다.


* CONFIG REWRITE:&#x20;
  * 레디스 인스턴스가 실행 중인 상태에서 설정 파일을 변경하는 것은 실행 중인 인스턴스에 반영되지 않는다.
  * 때문에 해당 명령어를 사용하여 재작성하는 과정을 거친 후 인스턴스를 재시작해야 한다.

### 수동으로 RDB 파일 생성

* `SAVE` 커맨드를 이용하면 동기방식으로 파일을 저장하는데 이는 백업파일 생성이 완료 될 때까지 다른 모든 클라이언트의 명령을 차단한다.
  * 따라서 일반적인 운영 환경에서는 SAVE 커맨드를 되도록 사용하지 않는 것이 좋다.
* `BGSAVE`는 fork를 호출해 자식 프로세스를 생성하며 백그라운드에서 RDB 파일을 생성한 뒤 종료된다.
  * 만약 이미 백그라운드로 데이터가 저장되고 있을때 이 커맨드를 수행하면 에러를 반환한다.
  * `BGSAVE`와 함께 `SCHEDULE`옵션을 사용하면 이미 백그라운드에서 저장 중일 때 해당 커맨드를 입력 받으면 우선OK를 반환하고 진행 중이던 백업이 완료 되었을때 따시 `BGSAVE`를 실행한다.
* `LASTSAVE` 커맨드로 RDB 파일이 정상적으로 저장되었는지 확인할 수 있다.
  * 마지막 RDB 파일이 저장된 시점을 유닉스 타임스탬프 형식으로 반환한다.

### 복제를 사용할 경우 자동으로 RDB 파일 생성

* `REPLICAOF` 커맨드를 이용해 복제를 요청하면 마스터 노드에서 RDB 파일을 새로 생성해 복제본을전달한다.
* 또는 이미 복제 연결이 되어 있는 상태에서 네트워크 등의 이슈로 인해 일정 시간 이상 복제가 끊어졌다가 복구 되는 경우에도 마스터 노드는 RDB 파일을 생성하고 복제하여 전송한다.



## AOF 방식의 데이터 백업

***

> 레디스 인스턴스가 처리한 모든 쓰기 작업을 차례대로 기록한다.
>
> 복원 시에는 파일을 다시 읽어가면 데이터 세트를 재구성한다.

* 예를 들어 FLUSHALL 커맨드로 데이터를 모두 날려버렸더라도,
* AOF 파일을 직접 열어 FLUSHALL 커맨드를 삭제한 뒤 레디스를 재시작하면 데이터를 바로 복구할 수 있다.

```sql
appendonly yes
appendfilename "appendonly.aof"
appenddirname "appendonlydir"
```

* `appendonly` 옵션을 yes 로 지정하면 AOF 파일에 주기적으로 데이터가 저장된다.
* AOF은 지정한 경로와 이름으로 생성된다.
  * 설정하지 않은 경우 기본 이름은 appendonly.aof 이다.
  * 7.0 버전 이상부터는 AOF 파일은 여러개로 저장되며 appenddirname 디렉터리 하위에 저장된다.

#### AOF 파일 저장 예제

```sql
SET key1 apple
SET key1 beer
DEL key1
DEL none_key

#저장된 결과
*3
$3
set
$4
key
$5
apple
*3
$3
set
$4
key1
$4
beer
*2
$3
del
$4
key1
```

* **메모리에 영향을 끼치는 작업**만 AOF에 저장되기 때문에 마지막 non\_key를 삭제하는 커맨드는 기록되지 않았다.
* 추가로 AOF는 실행한 커맨드를 **그대로 저장하지 않는다**.
  * list 에서 블로킹 기능을 제공하는 `BRPOP` 는 `RPOP`로 기록된다.
  * 이렇듯 블로킹 기능처럼 굳이 명시해줄 필요가 없는 경우 다르게 저장될 수 있다.
  * 또한 `INCRBYFLOAT`와 같은 커맨드도 부동소수점을 처리하는 방식이 다를 수 있기 때문에 AOF 파일에는 증분 후의 값을 직접 `SET` 하는 커맨드로 변경하여 저장된다.

### AOF 파일을 재구성하는 방법

> AOF 파일을 이용한 백업 기능을 안정적으로 사용하려면 점점 커지는 파일을 주기적으로 압축시키는 재구성 작업이 필요하다.

### 자동 AOF 재구성

```
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

* **auto-aof-rewrite-percentage**:  AOF 파일을 다시 쓰기 위한 시점을 정하기 위한 옵션이다.
* 마지막으로 재구성됐던 AOF 파일의 크기와 비교해, 현재의 AOF 파일이 지정된 퍼센트만큼 커졌을 때 재구성을 시도한다.
* 마지막으로 저장된 AOF 파일의 크기는 레디스에서 `INFO Persistence` 커맨드로 확인할 수 있는 `aof_base_size` 값이다.

### 수동 AOF 재구성

* `BGREWRITEAOF` 커맨드를 이용하면 원하는 시점에 직접 AOF 파일을 재구성할 수 있다.
* 자동으로 재구성할 때와 동일하게 동작한다.

### AOF 타임스탬프

* 버전 7 이상부터는 AOF를 저장할 때 타임스탬프를 남길 수 있다.

```sql
aof-timestamp-enabled no
```

* 해당 옵션을 활성화 시키면 다음과 같이 AOF 데이터가 저장될 때 타임스탬프도 함께 저장된다.

<pre class="language-sql"><code class="lang-sql"><strong>#TS:1669532240
</strong>... AOF 기록들
</code></pre>

* 이를 이용하면 수동으로 AOF 파일을 조작하지 않아도 시스템상에서 시점 복원이 가능하다.
* 레디스에서 제공하는 redis-check-aof 프로그램을 사용하여 돌릴 수 있다.

```sql
src/ redis-check-aof --truncate-to-timestamp 1669532240appendonlydir/appendonly.aof.manifest
```

* 다만 해당 옵션을 사용해 AOF 파일을 복구하면 원본 파일이 변경되어 수행 이전의 파일도 보호하고 싶다면 옵션을 사용하기 전에 백업을 해두어야 한다.

### AOF 파일 복원

* 시점 복원에서 사용한 redis-check-aof 프로그램은 AOF 파일이 손상되었을 때에도 사용할 수 있다.
* 의도치 않은 서버의 장애 발생 시 AOF 파일 작성 도중 레디스가 중지 될 가능성이 존재한다.

```sql
src/redis-check-aof appendonlydir/appendonly.aof.manifest
> Start checking Multi Part AOF
> ... ~ 실행 로그
> AOF appendonly.aof.15.incr.aof is not valid. Use the --fix option to try fixing it.

src/redis-check-aof --fix appendonlydir/appendonly.aof.manifest
> Start checking Multi Part AOF
> ... ~ 실행 로그
> Successfully truncated AOF appendonly.aof.15.incr.aof
> ALL AOF files and manifest are valid
```

* 레디스가 의도치 않은 장애로 중단 되었을 때 위처럼 AOF 파일의 상태가 정상적인지 확인하고,
* 정상적이지 않다면 fix 옵션을 사용하여 해결할 수 있다.
* 다만 fix 옵션을 사용해 복구하면 원본 파일을 변경하기 때문에 필요한 경우 원본 데이터를 다른 곳에 복사해두는 것이 안전하다.

### AOF 파일의 안전성

* 레디스는 디스크에 AOF를 저장하기 위해선 운영체제의 시스템 콜을 사용한다.
* 이는 OS Buffer로 `WRITE` 시스템 콜을 사용하여 버퍼에 저장시키는데,
* 해당 버퍼는 `FSYNC`를 통해 디스크의 AOF 파일에 버퍼의 데이터로 동기화하여 저장한다.
* 만약 `FSYNC` 시스템 콜을 사용하지 않는다면 운영체제가 판단하기에 커널이 여유 있거나 최대 지연 시간인 30초에 도달하면 커널 버퍼의 데이터를 실제 디스크에 내려 쓴다.
* 따라서 레디스는 FSYNC를 언제마다 호출할지 판단하여 사용하여야 한다.

#### APPENDFSYNC no:&#x20;

* `WRITE` 시스템 콜을 호출한다.
* 데이터는 커널 영역에 데이터가 잘 저장되는지만 확인하기 때문에 쓰기 성능이 가장 빠르다.
* 단,서버에 장애가 발생하면 최대 30초 동안 레디스에 입력했던 데이터를 잃을 수 있다.

#### APPENDFSYNC always:

* AOF 데이터를 저장할 때 항상 `WRITE`와 `FSYBC` 시스템 콜을 함께 호출한다.
* 즉, 매번 디스크에 쓰고자 하는 데이터가 정확하게 저장되는 것을 기다리기 때문에 굉장히 느려질 수 있다.

#### APPENDFSYNC everysec:&#x20;

* 데이터를 저장할 때 WRITE 시스템 콜을 호출하며, 1초에 한 번씩 `FSYNC` 시스템 콜을 호출한다.
* 성능은 no 옵션을 사용했을 때와 거의 비슷하다.
* 때문에 일반적인 경우 해당 옵션을 사용하는 것을 권장한다.

## 백업을 사용할 때 주의할 점

***

> RDB와 AOF 파일을 사용하는 경우 인스턴스의 maxmemory 값은 실제 서버 메모리보다 여유를 갖고 설정하는 것이 좋다.

* 백업을 사용하는 경우는 앞서 말했듯이 `BGSAVE` 커맨드로 백그라운드에서 백업을 진행한다.
  * `SAVE` 도 존재하지만, 일반적인 환경에서는 모든 클라이언트 요청이 중지되기에 사용 지양.
* 따라서 `fork()`를  통해 자식 프로세스를 만들어 `COW` 방식으로 메모리 상의 데이터를 하나 더 복사하는 방법을 이용해 백업을 진행한다.
* 이는 레디스의 모든 메모리를 그대로 파일에 저장해야 하므로 상당한 추가 메모리가 필요하게 된다.
* 따라서 `maxmemory`의 옵션은 실제 메모리보다 여유를 갖고 설정하는 것이 좋다.
* RDB 스냅숏을 저장하는 도중엔 AOF 의 재구성 기능을 사용할 수 없고,
* AOF 재구성이 진행될 때에는 `BGSAVE`를 실행 할 수 없다.

### 적절한 메모리 설정표

|  RAM  | maxmemory |  비율 |
| :---: | :-------: | :-: |
|  2 GB |   638 MB  | 33% |
|  4 GB |  2,048 MB | 50% |
|  8 GB |  4,779 MB | 58% |
| 16 GB | 10,240 MB | 63% |
| 32 GB | 21,163 MB | 65% |
| 64 GB | 43,008 MB | 66% |
