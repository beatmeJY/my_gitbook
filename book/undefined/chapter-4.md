---
description: 레디스 자료 구조 활용사례
---

# Chapter 4

## 실시간 리더보드 (sorted set)

### 리더보드란?

> 경쟁자들의 순위와 현재 점수를 보여주는 순위표를 의미한다.
>
> 주로 게임에서 스코어로 정렬돼 상위 경쟁자의 순위를 보여주는 용도로 쓰이지만, 게임 외의 서비스에서도 여러 데이터들을 게임화하여 리더보드로 나타내는 방식을 자주 사용하고 있다.

#### 절대적 리더보드란

> 모든 유저를 정렬시켜 상위권의 목록만을 표시한다.

#### 상대적 리더보드란

> 사용자마다 다른 데이터를 보여주며 사용자의 스코어를 기반으로 다른 사용자와 비교해 순위를 결정하는 형태의 리더보드이다.



### 실시간 랭킹리더보드 예시

```sql
ZADD daily-score:240617 28 player:286
ZADD daily-score:240617 400 player:234
ZADD daily-score:240617 45 player:101

ZREVRANGE daily-score:240617 0 2 withscores
"player:234"
"400"
"player:101"
"45"
"player:286"
"28"
```

* 레디스의 sorted set은 데이터가 저장될 때부터 정렬되어 들어가기 때문에,
* 유저의 score를 가중치로 설정한다면 스코어 순으로 유저가 정렬되어 매번 데이터를 정렬할 필요가 없다.
* 일별 리더보드를 도입하기 위해서 `daily-score:<날짜>`를 이용해 sorted set 키를 만들고 사용자의 스코어를 가중치로 사용해서 데이터를 만들 수 있다.
* `ZREVRANGE`를 사용해서 높은 스코어부터 검색하여 오늘의 상위 스코어 3명을 출력 할 수 있다.

```sql
ZADD daily-score:240617 200 player:286
ZINCRBY daily-score:240617 172 player:286
```

* 만약 플레이어의 스코어를 갱신해야 한다면 위와 같은 커맨드로 쉽게 갱신 할 수 있다.
  * **ZADD**: 같은 데이터는 중복으로 저장 되지 않으므로 새로운 스코어로 갱신된다.
  * **ZINCRBY**: `INCREBY` 커맨드와 비슷하게 동작하여 스코어를 입력한 만큼 증가시킬 수도 있다.

```sql
ZUINONSTORE weekly-score:2406-3 7 
daily-score:240617 daily-score:240618 daily-score:240619 daily-score:240620 
daily-score:240621 daily-score:240622 daily-score:240623
```

* **ZUINONSTORE**: 지정한 키에 연결된 각 아이템의 스코어를 합산하는 커맨드이다.
  * 만약 주말에 2배 점수 이벤트를 한다면 마지막에 `WEIGHTS` 옵션으로 가중치를 둘 수 있다.
  * 위 코드의 맨 마지막 구문에 이와 같이 추가. `weight 1 1 1 1 1 2 2`



## 최근 검색 기록 (sorted set)

> 사용자별 가장 최근에 검색한 검색 기록 5개만을 가져온다 가정하자.

```sql
ZADD search-keyword:123 20240616103212 육개장사발면
```

* sorted set에 score를 날짜로 넣어 정렬을 하는 방법을 사용할 수 있다.
* sorted set은 중복을 허용하지 않으므로 동일한 검색 결과도 존재 하지 않는다.
* 동일한 검색 결과가 발생했다면 가장 최근에 검색한 데이터로 변경이 될 뿐이다.

<figure><img src="../../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

* 만일 ㄱ가 5개 이후에 삭제하는 로직을 구현할 경우 0번째 데이터를 삭제해야 하는데,
* 이는 음수 인덱스를 사용하여 -6 부터 삭제를 하면 된다.















