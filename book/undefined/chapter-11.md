---
description: 보안
---

# Chapter 11

레디스는 **명령어를 이용 해 인스턴스의 중요한 설정값을 제어**할 수 있다.

* `CONFIG GET` 커맨드를 이용하면 레디스에 지정된 설정값을 읽어올 수 있으며,&#x20;
* 대부분의 파라미터는 `CONFIG SET` 커맨드를 이용해 재설정 할 수 있다.

## 커넥션 제어&#x20;

***

### bind

> 레디스 인스턴스가 실행 중인 서버는 **여러 개의 네트워크 인터페이스**를 가질 수 있다.
>
> 이는 서버가 여러 개의 ip를 가지고 있을 수 있다는 것을 의미한다.

* 기본 설정값을 로컬 IP 주소이다. (127.0.0..1)
* 따라서 별도의 설정을 하지 않으면 오직 동일한 서버 내에서의 연결만을 허용한다.
* 서버 외부에서 연결이 필요한 경우는 bind 설정값을 다른 IP주소로 명시적으로 설정해야 한다.
* 0.0.0.0 또는 \* 로 설정할 경우 모든 연결을 허용하지만 보안적인 설정을 권장한다.

### Password

> 레디스에서는 패스워드를 설정하는데 두 가지 방식이 있다.
>
> 노드에 직접 패스워드를 저장하는 방식과 ACL 기능을 사용하는 방식이다.

```sql
CONFIG SET requirepass password
```

* `requirepass` 명령어를 사용해서 패스워드를 설정할 수 있다.
* 패스워드는redis.conf에서 지정한 뒤 실행시킬 수도 있고`requirepass`를 통해 실행 중에 변경할 수도 있다.

```sql
redis-cli -a password
```

* `redis-cli` 의 -a 옵션을 사용하여 패스워드가 설정 노드에 접속할 수 있다.
* 다만 이렇게 커맨드 라인에서 직접 패스워드를 입력할 경우 안전하지 않을 수 있는 경고가 출력된다.
  * 경고가 출력 되는걸 방지하기 위해선 `--no-auth-warning` 옵션을 사용하면 출력되지 않는다.

### Protected mode

* 레디스를 운영 용도로 사용해야 한다면 설정하는 것을 권장한다.
* `기본값은 yes`로 설정되어 있으므로 레디스 인스턴스에 패스워드를 설정하지 않았다면 로컬에서 들어오는 연결만을 허용한다.
* 만약 패스워드 없이 레디스 인스턴스를 사용하고 싶다면 해당 값을 no로 설정해주어야만 한다.

## 커맨드 제어

***

* 운영자가 **커맨드라인**으로 편리하게 `인스턴스를 제어할 수 있는 장점`이 있지만,
* 레디스에 접근할 수 있는 **모든 클라이언트가 레디스 인스턴스를 제어**할 수 있다는 점에서는 위험할 수 있다.
* 예를 들어 `CONFIG SET dir <경로>` 커맨드를 수행하여 레디스의 기본 디렉터리를 변경한 뒤, `BGSAVE` 커맨드로 데이터를 저장하면 레디스의 모든 데이터를 원하는 경로에 저장할 수 있다.

### 커맨드 이름 변경

* `rename-command`는 `redis.conf` 파일에서 변경할 수 있다.
* 레디스에서 특정 커맨드를 다른 이름으로 변경하거나 커맨드를 비활성화 할 수 있다.
  * 커맨드를 커스터마이징하거나 보안을 강화하는데 도움이 된다.
* 실행 중에는 동적으로 변경할 수 없다.

```
rename-command CONFIG CONFIG_NEW
```

* 예를 들어 설정 파일에서 CONFIG 명령어를 CONFIG\_NEW라는 이름을 사용해 명령어를 실행 할 수 있게 된다.

### 커맨드 실행 환경 제어

```
enable-protected-configs no
enable-debug-command no
enable-module-command no
```

* **enable-protected-configs**: 레디스의 기본 경로 설정인 dir 및 백업 파일의 경로를 지정하는 `dbfile` 등의 옵션을 `CONFIG` 커맨드로 수정하는 것을 차단할 수 있다.
* **enable-debug-command**:
  * `DEBUG` 커맨드를 차단할 수 있다.&#x20;
  * `DEBUG` 커맨드는 레디스를 디버깅할 때 사용되어 운영환경에서는 잘 사용되지 않는다.
* **enable-module-command**:
  * `MODULE` 커맨드의 수행을 차단할 수 있다.
  * `MODULE` 커맨드 역시 잘 사용되지 않으며, 검증되지 않은 모듈을 가져올 수 있어 잠재적으로 위험할 수 있다.
* 설정 값:
  * **no**: 모든 연결에 대해서 명령어의 수행이 차단된다.
  * **yes**: 모든 연결에 대해서 명령어의 수행이 허용된다.
  * **local**: 로컬 연결에 대해서만 명령어의 수행이 허용된다.&#x20;
  * `DEBUG`나 `MODULE`의 경우 `local`로 사용하거나 아예 `no`로 설정하는 것이 좋다.

### 레디스를 이용한 해킹 사례

> 보안이 취약한 레디스를 통해 해당 서버에 SSH키를 서버에 저장하여 직접 접근하는 해킹 사례이다.

* `protected-mode`가 `no`로 되어 있고 패스워드도 설정 되어 있지 않는 서버라면,
* 우선 서버로 접근이 가능한지 telnet 커맨드로 먼저 확인한다.
* 해킹 서버에서 SSH 키를 생성하고 key.txt로 저장한다.
* 레디스 서버에 데이터를 `flushall` 로 모두 삭제한 뒤 방금 생성한 key.txt를 전달하여 저장한다.

```sql
CONFIG SET dir /home/centos/.ssh/
CONFIG SET dbfilename authorized_key
SAVE
```

* 이후 저장 경로와 파일 명을 변경한뒤 RDB 저장방식으로 백업파일을 SSH키로 위장하여 저장한 뒤 해킹 서버에서 레디스에 SSH 키를 사용해 직접 접근할 수 있게 되었다.

## ACL

***

> 버전 6부터 도입된 ACL(Access Control List) 기능은 유저라는 개념을 도입해,&#x20;
>
> 각 유저별로 실행 가능한 커맨드와 접근 가능한 키를 제한하는 기능이다.

### 유저의 생성과 삭제

```sql
ACL SETUSER garimoo on >password ~cached:* +@all -@dangerous
ACL GETUSER garimoo
> "flags"
> 1) "on"
> "passwords"
> "5e88..."
> "keys"
> "~cached:*"
...
ACL SETUSER garimoo ~id:*
ACL GETUSER garimoo
> "keys"
> "~cached:* ~id:*"
...
ACL DELUSER garimoo
```

* **ACL SETUSER**: 유저를 생성할 수 있다. 추가하고 싶은 값이 있다면 위와 같이 한 번 더 수행할 수 있다.
* **ACL GETUSER**: 유저정보를 조회할 수 있다.
* **ACL DELUSER**: 유저를 삭제할 수 있다.
*   **ACL LIST**: 레디스에 생성된 모든 유저를 확인 할 수 있다.



레디스 설치 후 아무런 패스워드와 유저를 생성하지 않았을 경우 아래와 같은 기본 유저가 존재한다.

```
ACL LIST
> 1) "user default on nopass ~* &* +@all"
```

* 유저 이름: default
* 유저 상태: on
* 유저 패스워드: nopass(패스워드 없음)
* 유저가 접근할 수 있는 키: \~\* (전체 키)
* 유저가 접근할 수 있는 채널: &\* (전체 채널)
* 유저가 접근할 수 있는 커맨드 +@all (전체 커맨드)

### 유저 상태 제어

* `on` 일 경우 해당 유저로의 접근을 허용한다.
* `off` 일 경우 해당 유저로는 더 이상 접근 할 수 없지만, 이미 접속해 있는 유저의 연결은 유지된다.
* `기본 값은 off`이기 때문에 유저를 새로 만들 때 `on` 로 명시해주거나 추후에 `on` 로 변경해야 한다.

### 패스워드

* 패스워드는 1개 이상 지정할 수 있다.
* `>패스워드`로 패스워드를 지정할 수 있다.
* `<패스워드`로 패스워드를 삭제할 수 있다.
* 패스워드를 지정하지 않으면 유저에 접근할 수 없다.
* `nopass` 권한을 부여하면 유저에는 패스워드 없이 접근할 수 있다.
* 또한 `nopass` 권한을 부여하면 기존에 해당유저에 설정되어 있던 모든 패스워드도 삭제된다.
* `resetpass` 권한을 부여해도 모든 패스워드가 삭제되지만, `nopass`도 삭제되어 다시 패스워드를 설정하기 전까진 유저에 접근할 수 없게 된다.

### 패스워드 저장 방식

```sql
CONFIG GET requirepass
> 1) "requirepass"
> 2) "mypassword"
```

* ACL 이전 기존의 requirepass 방식은 암호화 되지 않은 채로 패스워드가 저장되었기 때문에 설정 파일에 접근할 수 있으면 누구나 패스워드를 확인할 수 있었다.

```sql
ACL SETUSER user:100 on >mypassword
ACL GETUSER user:100
> "flags"
> 1) "on"
> "passwords"
> 1) "5e88..."
```

* 하지만 ACL을 이용해 패스워드를 저장하면 내부적으로 SHA256 방식으로 암호화 되어 저장되기 때문에 유저의 정보를 확인하고자 해도 패스워드 정보를 바로 조회할 수 없다.

```sql
ACL GENPASS
> "05c8f..."
```

* **ACL GEPASS**: 커맨드를 이용하면 난수를 생성할 수 있다.

### 커맨드 권한 제어

* 운영의 편의성을 위해 일부 커맨드는 그룹화되어 카테고리로 정리되어 있다.
* 이는 유저가 사용할 수 있는 커맨드를 제어할 수 있으며 개별 커맨드 및 특정 서브 커맨드를 제어하는 것도 가능하다.

```sql
ACL SETUSER user1 +@all -@admin +slowlog|get
```

* 만일 위와 같은 명령어가 실행된다면 ACL 룰은 왼쪽부터 오른쪽으로 순서대로 적용된다.
  1. **+@all**: 모든 커맨드의 수행 권한을 부여한 뒤
  2. **-@admin**: `admin` 카테고리의 커맨드 수행 권한은 제외시킨다.
  3. **+slowlog**: 커맨드 중 `get` 이라는 서브 커맨드에 대한 수행 권한만 추가로 다시 부여한다.

```sql
ACL CAT
> "keyspace"
> "read"
> "write"
> "set"
> "sortedset"
> "list"
> "hash"
> "string"
> "bitmap"
> "hyperloglog"
> "geo"
> "stream"
> "pubsub"
> "admin"
> "fast"
> "slow"
> "blocking"
> "dangerous"
> "connection"
> "transaction"
> "scripting"
```

* ACL CAT: 해당 명령어를 통해 레디스에 미리 정의되어 있는 카테고리의 커맨드 list를 확인할 수 있다.

#### dangerous

> replicaof 같은 커맨드는 마스터 정보를 변경하기에 운영자가 의도하지 않은 구성으로 변경할 수 있고,
>
> sort나 keys와 같은 커맨드는 메모리에 있는 모든 키들에 접근하기 때문에 오랜 기간 수행될 수 있다.
>
> 이렇듯dangerous 카테고리만 수행하지 못하도록 설정하더라도 의도치 않은 장애 상황을 많이 줄일 수 있다.

* **구성 변경 커맨드**:&#x20;
  * replconf&#x20;
  * replicaof&#x20;
  * migrate&#x20;
  * failover
* **장애 유발 커맨드:**&#x20;
  * sort
  * flushdb
  * flushall
  * keys
* **운영 커맨드**:&#x20;
  * shutdown
  * monitor
  * acl | log, acl | deluser, acl | list, acl | setuser
  * bgsave, bgrewriteaof
  * info
  * config | get, config | set, config | rewrite, config | resetstat
  * debug
  * cluster | addslots, cluster | forget, cluster | failover
  * latency | graph, latency | doctor, latency | reset, latency | history
  * client | list, client kill,  client | pause
  * module | loadex, module | list, module | unload

#### admin

* dangerous 카테고리에서 **장애 유발 커맨드를 제외**한 커맨드가 들어있다.
* 즉 **구성 변경 커맨드, 운영 커맨드** 가 들어있다.
* 예를 들어 개발 용도의 레디스 인스턴스를 제공할 때는 위와 같은 커맨드를 사용할 수 있도록 `admin` 카테고리만 제외시킨 권한을 전달해줄 수 있다.

#### fast

* O(1) 로 수행되는 커맨드만 모아놓은 카테고리다.
* get, spop, hset 등의 커맨드가 포함되어 있다.

#### slow

* fast 카테고리에 속하지 않는 커맨드가 들어 있다.
* scan, set, setbit, sunion 등의 커맨드를 포함한다.

#### keyspace

* 키와 관련된 커맨드가 포함된 카테고리다.
* scan, keys, rename, type, expire, exists 등 키의 이름을 변경하거나 키의 종류를 파악하거나, 키의 TTL 값을 확인하거나 혹은 키가 있는지 확인하는 등의 커맨드를 포함한다.

#### read

* 데이터를 읽어오는 커맨드가 포함된 카테고리다.
* 각 자료 구조별 읽기 전용으로 키를 읽어오는 커맨드를 포함한다.
* get, hget, xtrange 등이 있다.

#### write

* 메모리에 데이터를 쓰는 커맨드가 포함된 카테고리다.
* set, lset, setbit, hmset 등을 포함한다.
* 키의 만료 시간 등의 메타데이터를 변경하는 expire, pexpire와 같은 커맨드들도 포함한다.

### 키접근 제어

* 레디스에선 프리픽스를 사용해 키를 생성하는 것이 일반이므로, 특정한 프리픽스를 가지고 있는 키에만 접근할 수 있도록 유저를제어할 수 있다.
* `~*` 또는 `allkeys` 키워드는 모든 키에 대한 접근이 가능함을 의미하며 `~<pattern>`을 이용해 접근 가능한 키를 정의할 수 있다.
* `~R<pattern>`는 읽기 권한을 `~W<pattern>`는 쓰기 권한을 부여할 수 있다.&#x20;
* `~RW<pattern>`도 가능하지만  `~<pattern>`와 동일하다.

```sql
ACR SETUSER loguser ~log:* %R~mail:*
COPY mail:1 log:mail:1
```

* loguser에게 log: 프리픽스의 대한 모든 접근권한을 부여한다.
* mail:에 대해서는 읽기 접근 권한만 부여한다.
* 따라서 위처럼 mail에서 읽어서 log에 저장하는 것이 가능하다.

```sql
ACR SETUSER loguser resetkeys
```

* **resetkeys**: 해당 커맨드를 사용하면 유저가 가지고 있는 키에 대한 접근 권한을 모두 초기화할 수 있다.

### 셀렉터

* 셀렉터는 버전7 에서 도입되어 좀 더 유연한 ACL 규칙을 위해 도입 되었다.

```sql
ACL SETUSER loguser ~log:* %R~mail:* %R~sms:*
TTL mail:1
> (integer) 95
```

* 위와 같은 권한이 있을 때 loguser는 mail:\* 프리픽스 키에 대한 메타데이터도 가져올 수 있다.
* 이를 방지하고 GET 커맨드만 사용하도록 강제하려면 **셀렉터**를 사용할 수 있다.

```sql
ACL SETUSER loguser resetkeys ~log:* (+GET ~mail:*)
```

* 여기서 괄호 안에 정의된 것이 바로 셀렉터이다.
* 위와 같이 하면 loguser에 대한 모든 키를 리셋하고 log:에 대한 모든 접근권한을 부여한 뒤,
* mail:에 대해서는 get만 가능하도록 설정할 수 있다.

### pub/sub 채널 접근 제어

* `&<pattern>` 키워드로 pub/sub 채널에 접근할 수 있는 권한을 제어할 수 있다.
* `all channels` 또는 `&*` 키워드로 전체 pub/sub 채널에 접근할 수 있는 권한이 부여된다.
* `resetchannels` 권한은 어떤 채널에도 발행 또는 구독할 수 없음을 의미한다.
* 기본적으로 유저를 생성하면 `resetchannels` 권한을 부여한다.

### 유저 초기화

```sql
ACL SETUSER <username> reset
```

* **reset**: 유저에 대한 모든 권한을 회수하고 기본 상태로 변경할 수 있다.
* `resetpass`, `resetkeys`, `resetchannels`, `off`, `-@all` 상태로 변경되어 `ACL SETUSER`한 직후와 동일해진다.

### ACL 규칙 파일로 관리하기

* ACL 규칙은 파일로 관리할 수 있다.
* 기본적으로 일반 설정 파일인 `redis.conf`에 저장된다.
* ACL 파일을 따로 관리해 유저 정보만 저장하는 것도 가능하다.

```sql
aclfile /etc/redis/user.acl
```

* 위처럼 `redis.conf`파일에 ACL 파일을 users.acl파일로 관리한다고 명시할 수 있다.
* 이처럼 `redis.conf`에 저장되든 다른 ACL 파일에 저장되든 저장되는 형태는 동일하며 저장되는 위치가 달라질 뿐이다.
* ACL 파일을 따로 관리할 경우 `ACL LOAD`나 `ACL SAVE` 커맨드를 이용해 유저 데이터를 레디스로 로드하거나 저장하는 것이 가능 해지기 때문에 운영 측면에서는 조금 더 유용하게 사용할 수 있다.
* 다만 ACL 파일을 따로 사용한다 했을 때 `CONFIG REWRITE` 커맨드로 ACL 정보는 저장되지 않는다는 점에 유의해야 한다.

## SSL/TLS

***

> 레디스는 네트워크를 통해 클라이언트와 서버 간에 데이터를 빠르게 주고 받는다.
>
> 이 과정에서 민감한 정보나 중요한 데이터가 평문 형태로 전송되면 악의적인 공격자가 네트워크를 감청해서 정보를 쉽게 열람하거나 조작할 수 있다. 이를 SSL/TLS통신을 적용함으로써 데이터 전송 과정을 암호화 하여 보안 위험을 크게 감소 시킬 수 있다.

#### SSL/TLS 란?

* 암호화를 위한 인터넷 기반 보안 프로토콜로 1995년 처음 개발 되었다.
* TLS는 현재 널리 사용되고 있는 보안 프로토콜로. SSL에서 시작해서 발전해왔다.
* 1996년 이후로 SSL은 업데이트 되지 않았고, 현재는 대부분 더 안전한 TLS 프로토콜을 사용한다.
* SSL/TLS 프로토콜은 데이터 전송과정에서 정보를 암호화함으로써 중간에서 데이터가 노출되거나 조작되는 것을 방지한다.

### 레디스에서 SSL/TLS 사용하기

```sql
make BUILD-TLS=yes
```

* 레디스에서 SSL/TLS 설정은 `기본적으로 비활성화` 상태이다.
  * 따라서 SSL/TLS 프로토콜을 사용하기 위해서는 위와 같이 정의해야 한다.
* 일반적으로 레디스 인스턴스와 클라이언트 간 동일한 인증서를 사용한다.
  * 따라서 설정에서 정의한 key, cert, ca-cert 파일은 레디스와 클라이언트에 동일하게 복사해둬야 한다.

```sql
tls-port <포트번호>
tls-cert-file /path/redis.crt
tls-key-file /path/redis.key
tls-ca-cert-file /path/ca.crt
```

* redis.conf 파일에서 tls-port 값을 추가하면 SSL/TLS 연결을 사용할 것이라는 걸 의미한다.
* port와 tls-port를 모두 지정했다면 레디스 인스턴스는 두 가지의 설정을 모두 받아들일 수 있다.
  * port 6379, tls-port 16379로 설정했다면,
    * &#x20;6379로는 일반적인 통신을 16379 포트로는 인증서를 기반으로 한 TLS 통신이 가능하다.
    * 만약 인증서 없이는 인스턴스 접근을 막고 싶다면 port를 0으로 명시해 기본 포트를 비활성화 하여 SSL/TLS 없이는 사용하지 못하도록 막을 수 있다.
* redis cli을 이용해서 SSL/TLS 프로토콜을 활성화한 인스턴스에 접속할때때는 다음과 같이 인증서를 입력해야 한다.

```sql
./src/redis-cli --tls \
    --cert /path/redis.crt \
    --key /path/redis.key \
    --cacert /path/ca.crt
```



### SSL/TLS를 사용한 HA 구성

#### 복제 구성

```sql
tls-port <포트 번호>

tls-replication yes

tls-cert-file /path/redis.crt
tls-key-file /path/redis.key
tls-ca-cert-file /path/ca.crt
```

* 기본적으로 `tls-replication` 값은 `no`로 설정되어 있다.
* 이는 복제본에서 마스터로의 커넥션은 SSL/TLS 연결이 아닌 일반 프로토콜로 연결됨을 의미한다.
* SSL/TLS를 사용하는 마스터와 연결을 이용한 복제를 하기 위해서는 복제본 서버에서도 `yes`로 설정해주어야 한다.

#### 센티널 구성

```sql
tls-port <포트 번호>

tls-replication yes

tls-cert-file /path/redis.crt
tls-key-file /path/redis.key
tls-ca-cert-file /path/ca.crt
```

* 센티널에서도 SSL/TLS 연결을 사용해 레디스에 접속할 수 있다.
* 복제 연결을 할때와 마찬가지로 sentinel.conf에 위와 같이 추가해야 한다.

#### 클러스터 구성

```sql
tls-port <포트 번호>

tls-replication yes

tls-cluster yes

tls-cert-file /path/redis.crt
tls-key-file /path/redis.key
tls-ca-cert-file /path/ca.crt
```

* 클러스터 간 구성에서 도 SSL/TLS 연결을 사용하려면 위와 같이 `tls-cluster yes`를 추가해야 한다.
* 이러면 모든 클러스터 노드 간의 연결과 클러스터 버스의 통신은 SSL/TLS 프로토콜을 이용해 보호된다.
