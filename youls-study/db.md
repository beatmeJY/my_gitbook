# 💾 DB

## 🍕 기타

### 🍊MySQL을 사용하신 이유는 무엇인가요?

* 저는 MySQL을 전공에서 배운 것이 다였고, 전 회사에서는 SQL-Server나 PostgreSql을 사용하였지만 토이프로젝트에서라도 한번 대중적으로 유명한 DB를 사용해보는 것도 좋은경험이라고 생각하여 사용하게 되었습니다.



### 🍋 버전을 어떤 걸 사용하셨을까요?

* 저는 UTF8MB4 캐릭터 셋이 기본 문자셋으로 설정되어 있는 버전인 **MySQL 8.0**을 사용하였습니다.
  * 이모지 등을 저장하기 위해서. (물론 다른 버전도 UTF8MB4을 지정하면 사용가능)



### 🍌 가장 큰 버전 별 특징은 어떤 것이 있을까요?

#### **MySQL 5.7 (2015년):**

* JSON 데이터 형식이 지원되고 Generated Columns과 같은 기능 추가 되었습니다.
* Information Schema를 사용하여 메타데이터를 관리하는 마지막 버전입니다.

#### **MySQL 8.0 (2018년):**

* Information Schema를 대체하는 InnoDB의 내부 데이터 딕셔너리를 활용하여 성능 및 확장성 향상
* SQL 쿼리에서 윈도우 함수의 지원 추가되었습니다. (**Window Functions)**
  * 예: ROW\_NUMBER(), RANK(), DENSE\_RANK(), OVER() 등
* WITH 절을 사용한 재귀 쿼리를 지원합니다.
* UTF8MB4 캐릭터 셋이 기본 문자셋으로 변경되었습니다.
  * MySQL 5.5.3이상부터 utf8mb4 문자 셋을 사용하면 이모지와 같은 4-byte 문자 지원을 하였지만, 이제 utf8mb4를 기본으로 변경하였습니다.
* 인덱스의내림차순을 지원합니다.
  * MySQL 5.7 버전에서는 내림차순 인덱스를 만들기 위해 먼저 오름차순 인덱스를 생성하고 역순으로 검색해야 합니다.
* 외래키의 길이가 최대 64자로 제한되었습니다.



### 🍑 MySQL이 대중화된 이유는 무엇일까요?

* 오래전부터 존재하던 오픈 소스로 많은 기업 및 개발자들에게 비용을 절감하면서도 안정적이고 강력한 기회를 제공하였습니다.
* 그리고 다양한 운영체제와 프로그래밍 언어에서의 호환성이 높아 개발자들이 쉽게 채택할 수 있었습니다.
* 물론PostgreSql도 오래전부터 존재하였지만 그 때 당시에는 개발자들에게 MySQL가 더 강세였고 그로인해오라클에 인수당하기 전까지 많은 개발자들에 의해 커뮤니티 기여와 오픈소스 생태계를 통해 빠르게 발전할 수 있었습니다.&#x20;
* 따라서 많은 사용자로부터 검증된 안정성과 지속적인 발전으로 인해 현재까지도 많이 사용되고 있습니다.



## 정규화

### 🍇 정규화를 시키는 이유가 무엇일까요?

* 데이터의 중복을 최소화 하여 DB에 저장 할 때 저장되는 용량을 줄일 수 있고,
* 데이터 변경 시 한 곳에서만 수정하여 데이터의 일관성을 지키는 장점을 얻습니다.
* 때문에 정규화를 하면 유지보수와 쿼리성능을 최적화 할 수 있습니다.



### 🍈 **정규화 1, 2, 3형에 대해 설명해주세요.**

#### <mark style="color:blue;">**제 1정규화**</mark> 는 모든 컬럼의 값은 원자값을 가지게 해야합니다.

* 여기서 원자값이란 컬럼에는 하나의 단일 값만 존재해야 합니다.
* 예를 들어

| 이름 | 과목          |
| -- | ----------- |
| 철수 | 생명과학, 지구과학  |

* 위와 같이 하나의 컬럼에는 여러 값을  가지고 있을 경우 아래와 같이 바꾸어야 합니다.

| 이름 | 과목   |
| -- | ---- |
| 철수 | 생명과학 |
| 철수 | 지구과학 |

#### <mark style="color:blue;">**제 2정규화**</mark> 는 1정규화를 충족하면서, 완전 함수 종속이 만족되어야 합니다.

* 여기서 완전 함수 종속이란 어떠한 컬럼이 기본키의 일부에만 종속이 되면 안됩니다.
* 즉, 복합키에서 주로 발생하며 단일 키의 경우 자동으로 조건을 만족합니다.
* 예를 들어, 복합키(이름, 과목)를 사용하는 테이블에서

| 이름 | 과목 | 교실    | 성적  |
| -- | -- | ----- | --- |
| 짱구 | 국어 | 해바라기반 | 100 |
| 치타 | 영어 | 장미반   | 70  |

* 일 때 복합키(이름,과목)로 성적을 결정하지만,
* **교실은** **기본키 중 과목에만 종속** 되므로 충족 되지 않습니다.
* 따라서 다음과 같이 나누어야합니다.

| 이름 | 과목 | 성적  |
| -- | -- | --- |
| 짱구 | 국어 | 100 |
| 치타 | 영어 | 70  |

| 과목 | 교실    |
| -- | ----- |
| 국어 | 해바라기반 |
| 영어 | 장미반   |

#### <mark style="color:blue;">**제 3정규화**</mark> 는 2정규화를 충족하면서, 이행적 종속이 되면 안됩니다.

* 여기서 이행적 종속은 한 테이블에서 A ->B이고 B->C 이므로 A->C가 되는 것은 안된다는 것 입니다.
* 쉽게 말해서 기본키가 아닌 컬럼에 종속이 되면 안됩니다.
* 예를 들어

| 유치원생 ID | 이름 | 과목 | 선생님 | 성적  |
| ------- | -- | -- | --- | --- |
| 1       | 짱구 | 국어 | 채성아 | 90  |
| 2       | 맹구 | 과학 | 채성아 | 80  |
| 3       | 철수 | 영어 | 나미리 | 100 |

* 위테이블에서 모든 ID는 PK에 종속적이지만
* **강사는 과목에도 종속**적이므로 만족하지 않습니다.
* 따라서 다음과 같이 두 테이블로 정규화가 되어야 합니다.

| 유치원생 ID | 이름 | 과목 | 성적  |
| ------- | -- | -- | --- |
| 1       | 짱구 | 국어 | 90  |
| 2       | 맹구 | 과학 | 80  |
| 3       | 철수 | 영어 | 100 |

| 과목 | 선생님 |
| -- | --- |
| 국어 | 채성아 |
| 과학 | 채성아 |
| 영어 | 나미리 |



### 🍉 복합키의 장단점에 대해서 설명해주세요.

#### 장점

* 특정 데이터를 식별하는게 큰 의미가 없을 경우에 사용할 경우 도움이 될 수 있습니다.
  * 예를들어 통계성 데이터들을 저장할 경우에 사용해도 좋습니다.
  * 복합키를 통한 조회로 인덱스를 잘 활용하여 일부 쿼리에서는 성능향상을 가져올 수 있습니다.

#### 단점

* JPA는 복합키 생성 시 컬럼명의 알파벳 순으로 생성합니다.
  * Entity class에 정의된 순서대로 생성되는 것이 아니기 때문에
  * 기대했던 PK Index가 타지 않을 가능성이 높습니다.
* 쿼리 작성이나 인덱스 관리 등에 더 많은 주의가 필요합니다.
* FK를 맺을 때 불필요하게 많은 키가 맺어져야합니다.
*

참고하면 좋을 자료 : [https://prohannah.tistory.com/99](https://prohannah.tistory.com/99)



## 🪽 NOSQL

### 🍐 NOSQL이란 무엇일까요?

* Not Only SQL이란 뜻으로 관계형 데이터베이스 모델을 기반으로 하지 않는다는 뜻으로 테이블 간에 강력한 관계를 가지지 않습니다.
* 또한 고정된 스키마를 가지지 않고 샤딩(Sharding)이라는 기술로여러 노드로 분산하여 데이터베이스를 관리하므로&#x20;
* 수평적으로 확장이 가능하도록 설계가 되어있습니다.



### 🍏 NOSQL의 장점이 무엇일까요?

* 데이터에 따라 다양한 모델의 형태로 저장하고 검색할 수 있습니다.
  * Key-Value, Document-Oreinted(Json), Column-Family, Graph 등등
* 대량의 읽기나 쓰기 연상이 필요한 경우에 RDB에 비하여 더 유리합니다.
* 필요에 따라 서버를 추가하여 수평적으로 확장하기에 편리합니다.
* RDB에 비해 기존의 필드를 삭제하거나 변경하는 것이 쉽게 가능하여 유연합니다.



### 🍎 DML, DDL, DCL

#### DML 이란?

* **데이터 조작 언어**(Data Manipulation)로 **데이터의 검색, 삽입, 갱신, 삭제하는 작업**을 수행합니다.
* 테이블 내의 레코드를 조작하고 데이터를 변경하는 데 사용됩니다.
* 주요 명령어로는 SELECT, INSERT, DELETE 등이 있습니다.

#### DDL 이란?

* **데이터 정의 언어**(Data Definition Language)로 **데이터베이스의 구조를 정의하거나 변경**합니다.
* 테이블, 인덱스, 뷰 등 데이터베이스 구조를 정의하고 조작하는데 사용됩니다.
* 주요명령어로는 CREATE, ALTER, DROP 등이 있습니다.

#### DCL 이란?

* **데이터 제어 언어**(Data Control Language)로 **권한을 관리**합니다.
* 사용자에게 어떤 종류의 권한을 부여하거나 회수하는데 사용됩니다.
  * 이를 통해 불법 사용자들을 차단하여 데이터를 보호합니다.
* 주요 명령어로는 GRANT(권한부여), REVOKE(권한취소) 등이 있습니다.



## 🥜 Join

### 🍇 SQL에서 Join이란 무엇이며 Join의 종류는 어떤 것들이 있을까요?

두 개 이상의 테이블에서 서로 묶어 원하는 데이터를 조회 하는 방법입니다.

<figure><img src="../.gitbook/assets/image (82).png" alt="" width="450"><figcaption><p><a href="https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/">https://hongong.hanbit.co.kr/sql-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95-joininner-outer-cross-self-join/</a></p></figcaption></figure>

<table><thead><tr><th width="201">JOIN</th><th width="349">내용 설명</th><th>특징</th></tr></thead><tbody><tr><td>LEFT OUTER JOIN</td><td>왼쪽 테이블에만 존재하는 값과 A와 B에 공통으로 존재하는 데이터를 가져옵니다.</td><td>((A ∩ B) ∪ (A - B))</td></tr><tr><td>RIGHT OUTER JOIN</td><td>오른쪽 테이블에만 존재하는 값과 A와 B에 공통으로 존재하는 데이터를 가져옵니다.</td><td>((A ∩ B) ∪ (B - A))</td></tr><tr><td>FULL OUTER JOIN</td><td>양쪽 테이블 모든 데이터를 가져옵니다.</td><td>(A ∪ B)</td></tr><tr><td>INNER JOIN</td><td>두 테이블 간에 교집합 연산한 내용입니다.</td><td>(A ∩ B)<br>일반적으로 JOIN이라고 부르면  INNER JOIN을 의미합니다.</td></tr><tr><td>CROSS JOIN</td><td>한쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인하는 기능입니다. 전체 행 개수는 두 테이블의 각 행의 개수를 곱한 수가 됩니다.</td><td><p>(A × B)</p><p>카티션 곱(CARTESIAN PRODUCT)이라고도 합니다</p></td></tr><tr><td>SELF JOIN</td><td>자신이 자신과 조인하여 1개의 테이블만을 사용합니다.</td><td>(A ∩ A)</td></tr></tbody></table>



### 🍈크로스 조인이 왜 성능상 좋지 않을까요?

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

두 테이블의 행 수를 곱한 만큼의 결과를 생성하기 때문에 행의 수가 많은 경우 성능상의 문제가 발생 할 수 있습니다.&#x20;



### 🍉 FetchJoin이 SQL 수준에서 어떻게 이루어 질까요?

* FetchJoin은 Eager Loading(즉시 로딩)으로 필요한 데이터를 한 쿼리에서 즉시 가져옵니다.&#x20;
* 실제 질의하는 대상 Entity와 Fetch join이 걸려있는 Entity를 포함해 모든 컬럼을 조회를 하여  N+1 문제를 방지하고 성능을 향상시킬 수 있습니다.
* 실제 SQL에서는 필요한 다른 엔티티를 Inner Join으로 Join을 합니다.



### 🍊 ON vs WHERE

#### ON은

* 주로 Join 키워드와 함께 사용되며
* 두  테이블 간에 연결 조건을 지정할 때 사용됩니다.

#### WHERE은&#x20;

* 일반적으로 데이터를 필터링 하기 위해 사용됩니다.
* 조인된 결과에 대한 추가적인 조건을 지정할 때 사용됩니다.



### 🍋 ON vs WHERE 어느 것이 먼저 실행이 될까요?

#### 일반적으로 SQL 쿼리의 실행 순서는 다음과 같습니다

1. **FROM 절**:&#x20;
   * 데이터를 가져올 테이블이나 뷰를 지정합니다.
2. **JOIN 절**:&#x20;
   * 여러 테이블을 조인하여 하나의 결과 집합을 생성합니다.
3. **ON 조건**:&#x20;
   * JOIN 절과 함께 사용되며, 테이블 간의 연결 조건을 정의합니다.
4. **WHERE 절**:&#x20;
   * 특정 조건을 충족하는 행을 선택합니다.&#x20;
   * 조인된 결과에 대한 조건을 지정할 수 있습니다.
5. **GROUP BY 절**:&#x20;
   * 설정된 컬럼별로 데이터를 그룹화 합니다.&#x20;
   * 집계 함수(COUNT, SUM 등)와 함께 사용되어 그룹 단위로 계산합니다.
6. **HAVING 절**:&#x20;
   * GROUP BY로 그룹화된 결과에 대한 조건을 지정합니다.&#x20;
   * WHERE와 비슷하지만, GROUP BY 이후에 사용됩니다.
7. **SELECT 절**:&#x20;
   * 쿼리에서 반환할 열을 지정합니다. 집계 함수를 사용하거나, 특정 열을 선택하거나, 계산된 열을 추가할 수 있습니다.
8. **ORDER BY 절**:&#x20;
   * 결과를 오름차순(ASC) 또는 내림차순(DESC)으로 정렬합니다.
9. **LIMIT 또는 OFFSET 절**:&#x20;
   * 결과 집합의 크기를 제한하거나 오프셋을 설정 할 수 있습니다.

**위와 같이 JOIN 절인 ON이 먼저 실행이 되고 WHERE 문이 실행이 됩니다.**



### 🍌 서브 쿼리란 무엇일까요?

* 하나의 쿼리에서 SQL 문 안에 또 다른 SQL 문이 있을 때 안에 포함된 SQL 문을 서브쿼리라고 합니다
* 보통 서브쿼리에서 반환하는 결과가 메인쿼리에서 보조 용도로 사용됩니다

#### **사용위치 별 서브쿼리**

* **SELECT 절**
  * 스칼라 서브쿼리라 불립니다.
* **FROM 절**
  * 인라인 뷰라 불립니다.
* **WHERE 절**
  * 흔히 가장 많이 사용되며 서브쿼리(단일행/다중행)라 불립니다.
    * 단일 행의 경우 하나의 행만 반환되어 비교연산자(=, <, <=, >=, <>)를 사용합니다.&#x20;
    * 여러 행을 반환하는 서브쿼리로 IN, ANY, ALL, EXISTS를 사용해 비교합니다.
* 그 외에 사용 가능 한 곳
  * &#x20;HAVING절&#x20;
  * &#x20;ORDER BY절&#x20;
  * &#x20;INSERT문의 VALUES 절
  * &#x20;UPDATE문의 SET 절



### 🍍 서브 쿼리는 성능 저하가 왜 발생할까요

* 서브쿼리는 메인 쿼리의 각 행에 대해 실행될 수 있어 이로 인해 동일한 서브쿼리가 여러 번 실행되어야 할 수 있습니다.
* 또한 서브쿼리를 사용하면 일반적으로 쿼리가 더 복잡해지므로, 실행 계획 최적화가 어려워질 수 있고,
* 서브쿼리는 실체적인 데이터를 저장하지 않기 때문에 메타정보가 없어 옵티마이저(SQL 실행 계획을 수립, 관리하는 시스템)가 쿼리를 해석하기 위해 필요한 정보를 서브쿼리에서 읽어낼 수 없습니다.



## 📚 파일 시스템

### 🥭 파일시스템이 왜 데이터베이스에서 읽을 때 보다 느릴까요?

1. **접근 방식의 차이**
   * **데이터베이스:** 데이터베이스는 인덱싱과 같은 고급 데이터 구조를 사용하여 빠르게 데이터에 접근할 수 있습니다.
   * **파일 시스템:** 파일 시스템은 파일을 읽고 쓰는 데 특화되어 있습니다. 데이터를 찾기 위해 파일을 순차적으로 읽어야 할 수 있어 성능에 영향을 미칠 수 있습니다.
2. **캐싱**
   * **데이터베이스:** 데이터베이스는 자주 사용되는 데이터를 메모리에 캐시하여 빠른 응답을 제공합니다. 이는 반복적인 쿼리에 대한 성능을 향상시킬 수 있습니다.
   * **파일 시스템:** 일반적으로 파일 시스템은 캐싱을 제공하지만, 데이터베이스만큼 효율적이지 않을 수 있습니다.
3. **쿼리 계획 최적화**
   * **데이터베이스:** 데이터베이스는 쿼리를 실행하기 전에 여러 기술을 사용하며 최적의 실행 계획을 결정하는데, 이를 통해 불필요한 작업을 줄이고 성능을 향상시킬 수 있습니다.
   * **파일 시스템:** 파일 시스템에서는 데이터 저장 및 검색을 효율적으로 수행할 수 없습니다.
4. **데이터 구조**
   * **데이터베이스:** 데이터베이스는 특정 유형의 데이터에 최적화된 데이터 구조를 사용할 수 있습니다. 또한 데이터 압축을 통해 디스크 공간을 절약하면서도 읽기 속도를 향상시킬 수 있습니다.
   * **파일 시스템:** 파일 시스템은 다양한 유형의 데이터를 다루기 위해 일반적인 목적의 데이터 구조를 사용합니다.
5. **중복된 데이터**
   * **데이터베이스**: 데이터베이스는 동일한 데이터를 한 곳에만 저장하고, 다른 위치에서는 해당 데이터에 대한 참조하며 일관된 상태를 유지하기 위해 정규화와 같은 기술을 사용합니다.
   * **파일시스템**: 동일한 데이터가 여러 파일에 중복 저장될 수 있으며, 이는 데이터 일관성을 유지하기 어렵게 만들 수 있습니다. 또한 중복 데이터를 수정하려면 모든 중복된 위치에서 수동으로 수정해야 합니다.

[https://www.guru99.com/ko/difference-between-file-system-and-dbms.html](https://www.guru99.com/ko/difference-between-file-system-and-dbms.html)



## 🛠️  DBMS

### 🥑 DBMS의 분류

#### **계층형 DBMS**

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

* 1960대에 시작되어 각 계층은 tree 형태를 갖습니다.
* 계층형 DBMS의 문제는 처음 구성을 완료한 후에 이를 변경하기가 상당히 까다롭습니다.
* 다른 구성원을 찾아가는 것이 비효율 적입니다.
  * 예를 들어 회계팀에서 기술팀을 찾아가기 위해선 회계이사 -> 사장 -> 기술이사와 같이 각 단계를 거쳐야 합니다.
* 이러한 단점들 때문에 현대에는 잘 쓰이지 않고 있습니다.

#### 망형 DBMS

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

* 계층형 DBMS의 문제점을 개선하기 위해서 1970년대에 등장하였습니다.
* 하위의 있는 구성원끼리도 연결되는 유연한 구조를 가지고 있습니다.
* 예를 들면 회계팀에서 기술팀을 찾아가는것도 연결을 추가하면 가능합니다.
* 하지만 모든 프로그래머가 모든 구조를 이해해야만 프로그램 작성이 가능하다는 단점이 존재합니다.
* 때문에 망형DBMS도 현대에는 잘 쓰이지 않고 있습니다.

#### 관계형 DBMS

| ID  | 이름  | 직급  | 부서  |
| --- | --- | --- | --- |
| 1   | 이사장 | 사장  | 관리  |
| 2   | 정이사 | 이사  | 회계  |
| 3   | 김회계 | 매니저 | 회계  |
| 4   | 박재무 | 프로  | 재무  |
| ... | ... | ... | ... |

* 주로 RDBMS(Relational DBMS)라고 부릅니다.
* 여러분들이 주로 잘 아시는 MySQL, PostgreSQL 등이 여기에 포함됩니다.
* 테이블이라는 최소 단위로 구성이 되며 하나 이상의 열과 행으로 이루어져 있고,
* 모든 데이터 테이블에 저장됩니다.

🖼️ [그림 참조 링크](https://hongong.hanbit.co.kr/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-databasedb-dbms-sql%EC%9D%98-%EA%B0%9C%EB%85%90/)



### 🍓 데이터 독립성이란?

* 데이터베이스의 구조와 데이터의 내용이 서로 영향을 미치지 않는 것을 의미합니다.
* 이는 데이터베이스의 유지보수와 확장성을 향상시키는 장점을 가집니다.



### 🍎 논리적 데이터 독립성이란?

* 논리적 데이터 독립성은 데이터베이스의 논리적 구조가 변경되어도 응용 프로그램이나 사용자의 요구에 영향을 주지 않는 것을 말합니다.&#x20;
* 예를 들어, 테이블의 속성이나 관계가 추가되거나 삭제되어도 기존의 쿼리나 트랜잭션은 그대로 유지될 수 있습니다.



### 🍏 물리적 데이터 독립성이란?

* 물리적 데이터 독립성은 데이터베이스의 물리적 구조가 변경되어도 논리적 구조나 데이터의 내용에 영향을 주지 않는 것을 말합니다.&#x20;
* 예를 들어, 파일의 저장 방식이나 인덱스의 구성이 바뀌어도 데이터베이스의 스키마나 데이터는 그대로 유지될 수 있습니다.



### 🥝 MySQL(DB)에 insert문 쿼리 시 DB 동작 과정

1. **연결 및 사용자 스레드 할당:**
   * 클라이언트가 MySQL 서버에 연결을 시도하면, MySQL Connectors는 클라이언트의 요청에 대한 연결을 수락하고 사용자 스레드를 할당합니다.
2. **인증 :**
   * 사용자 스레드가 할당되면 클라이언트의 사용자 인증이 진행됩니다.&#x20;
   * 사용자가 올바른 권한을 가지고 있는지 확인합니다.
3. **쿼리 파싱 및 최적화:**
   * 인증이 완료되면 MySQL 엔진에 전달됩니다.
   * MySQL 엔진의 토큰 파서를 통해 쿼리를 파싱하며, SQL을 MySQL이 이해 가능한 최소 단위로 잘라내고 문법 유효성을 검증합니다.
4. **전처리**
   * 쿼리에 포함된 테이블명, 컬럼명 등이 존재하는지 확인하고 사용자가 테이블에 접근할 수 있는지 확인합니다.&#x20;
   * 데이터 형식에 대한 변환과 함께 서브쿼리와 조인과 같은 복잡한 쿼리도 처리됩니다.
5. **옵티마이저 최적화:**
   * 전처리된 쿼리는 옵티마이저에게 전달되어 최적의 실행 계획을 결정합니다.
6. **데이터 락 및 동시성 제어:**
   * 쿼리 실행에 필요한 테이블 또는 레코드에 대한 데이터 락이 설정되고, 동시성을 관리하기 위한 메커니즘이 사용됩니다.
7. **데이터베이스 엔진에서 쿼리 실행:**
   * 최적화된 실행 계획에 따라 MySQL 엔진이 쿼리를 실행하고, 결과를 생성합니다.
8. **커밋 또는 롤백:**
   * 트랜잭션이 사용 중인 경우, 쿼리 실행 후 변경 사항은 커밋 또는 롤백됩니다.
9. **결과 반환:**
   * 쿼리 실행이 완료되면 MySQL 엔진은 결과를 사용자 스레드로 반환합니다.
   * 사용자 스레드는 클라이언트에게 결과를 전송하고, 클라이언트는 이를 받아 처리합니다.
10. **디스크 반영**:
    * 백그라운드 스레드에서 커밋되었으나 디스크에 반영되지 않은 내용을 디스크에 접근하여 일괄 처리합니다.
    * 이때 트랜잭션이 커밋되었을 때 변경사항을 저장한 트랜잭션 로그(Transaction Log)를 사용하여 변경된 데이터를 백그라운드에서 비동기적으로 디스크에 기록합니다.
    * InnoDB 스토리지 엔진에서는 변경된 페이지들을 "더티 페이지"로 추적하고, 주기적으로 체크포인트를 수행하여 더티 페이지를 디스크에 기록합니다.&#x20;
    * 이로써 효율적인 디스크 I/O가 이루어집니다.



## 💽 데이터 액세스

### 🍇 암달의 법칙이란?

암달의 법칙(~~암달의 저주~~)이란 컴퓨터 시스템의 일부를 개선할 때 **전체적으로 얼마만큼의 최대 성능 향상**이 있는지 계산하는 데 사용됩니다.

<figure><img src="../.gitbook/assets/image (56).png" alt="" width="144"><figcaption><p>암달의 법칙 공식 (P는 작업 시간, S는 작업 효율 증가 배수)</p></figcaption></figure>

가령, 전체 작업 시간 중에서 CPU가 처리하는 시간이 10%, SDD가 처리하는 시간이 90%를 차지하고 있게 된다면,

* 10%를 차지하는 CPU의 작업의 속도를 2배 증가 시키더라도 전체 작업 속도는 약 1.05배 즉, 전체 성능에서 **5%** 가량 증가하게 되고
* 90%를 차지하는 SDD의작업의 속도를 2배 증가 시켰다면 약 1.81배 즉,  전체 성능에서 **81%**의 성능 개선 효과를 가져올 수 있습니다.

따라서 아무리 빠른 CPU를 가져와서 사용을 하더라도 **디스크 I/O가 빠르게 처리가 되지 않으면 전체 시스템 성능 향상에 큰 영향을 줄 수 없다는 걸** 강조하기 위해서 가져오게 되었는데요.

<figure><img src="../.gitbook/assets/image (57).png" alt="" width="375"><figcaption><p><a href="https://hudi.blog/storage-and-random-sequantial-io/">https://hudi.blog/storage-and-random-sequantial-io/</a></p></figcaption></figure>

DB는 대부분 이러한 디스크의 I/O를(읽기, 쓰기, 삭제 등) 많이 처리하고 있어 한번의 잘못 작성된  쿼리는 전체 성능의 저하를 가져올 수 있습니다.



### 🍈 랜덤 I/O와 순차I/O 란?

#### 🔀 랜덤I/O

* 랜덤 I/O는 읽어야 하는 데이터가 **물리적으로 불연속적**으로 있기 때문에 디스크 헤더를 이동시킨 다음 데이터를 읽는 것을 의미합니다.
* HDD 일 때에는 이러한 랜덤 I/O를 읽어내기 위해 디스크 헤더를 움직여 읽으므로, 헤더를 움직이는데 걸리는 시간이 오래 걸렸지만.
* SSD는 물리적인 한계였던 디스크 판을 없애고 플래시 메모리를 사용하여 HDD에 비해서 월등하게 높은 성능 향상을 기대할 수 있습니다.

#### ➡️ 순차I/O

* 순차 I/O는 읽어야 하는 데이터가 **물리적으로 연속적**으로 있어 디스크 헤더의 이동 없이 순차적으로 읽는 것을 의미합니다.
* 순차 I/O의 경우 HDD와 SDD의 성능 차이는 비교적 많이 발생하지는 않는다고 합니다.
* SSD는 디스크판이 없지만 마찬가지로랜덤 I/O와 순차 I/O의 성능 차이는 많이 발생한다고 합니다.
  * 그 이유는 SSD는 NAND 플래시 메모리에 저장된 데이터를 직접 읽는 것이 아니라 사실 논리주소를 물리주소로 매핑하는 매핑테이블을 사용하고 있다고 합니다.
  * 따라서 매핑 테이블을 통해 NAND 플래시 메모리의 물리적인 위치를 찾아가야 하므로 SSD도추가적인 성능 이슈가 발생하게 됩니다.

따라서 일반적으로 **쿼리를 튜닝하는 것**은 순차 I/O를 사용할 지 랜덤 I/O를 선택할지 고민하는 것도 좋은 방법일 수 있으나, 실제로 랜덤 I/O가 순차 I/O로 변하는 일은 많이 않기에 가장 중요한 것은 꼭 필요한 정보만을 읽어 **I/O 작업 자체를 줄여주는 것이 주 목적**이라고 생각합니다.



## 🔍 INDEX

### 🍉 Index 란?

데이터 베이스에서 검색 속도를 향상 시키기 위해 사용되는 특정 열에 대한 정렬된 데이터 구조를 말합니다.

* 데이터베이스 엔진은 이를 참조하여 효율적으로 데이터에 접근 할 수 있습니다.
* 인덱스는 주로 SELECT 쿼리에서 WHERE나 JOIN 절에서 사용되어 검색 조건을 만족하는 행을 찾는데 도움이 됩니다.
*

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption><p><a href="https://mangkyu.tistory.com/96">https://mangkyu.tistory.com/96</a></p></figcaption></figure>



### 🍊 Index는 많은게 좋을까 적은게 좋을까?

저는 인덱스는 기본적으로 적합한 열에만 **적을 수록** 좋다고 생각합니다.

인덱스를 사용하지 않는 조회를 해야 하는 상황이라면 테이블전체를 탐색하는 Full Scan을 해야하므로, 인덱스를 설정해주는 것은 꼭 필요한 작업이라고 할 수 있습니다.

#### 하지만 이렇게 좋은 일을 하는 인덱스가 왜 적을 수록 좋다고 하였을까요?&#x20;

* 그 이유는 인덱스도 **생성** **및** **유지 비용**이 많이 들기 때문인데요.
  * **인덱스 생성 비용**
    * 인덱스는 원하는 값을 빠르게 탐색하기 위해 항상 최신의 정렬된 상태를 유지해야 합니다.
    * 인덱스가 적용된 컬럼에 데이터 변경 시 각각INSERT(인덱스 추가), DELETE(인덱스  사용하지 않음), UPDATE(인덱스 사용하지 않음 및 추가)의 작업을 진행하여야 합니다.
  * **인덱스 유지 비용**
    * 인덱스를 저장하기 위해선 테이블의 약 10% 정도의 디스크를 추가로 사용하여야 합니다.



### 🍑 Index Scan 방식 종류

#### Index Range Scan

<figure><img src="../.gitbook/assets/image (44).png" alt="" width="341"><figcaption></figcaption></figure>

* 인덱스 루트에서 리프 블록까지 수직적 탐색을 하고, 필요한 범위만큼 수평적 탐색하는 스캔 방식입니다.
* 선두 컬럼을 가공하지 않은 상태로 조건 절에 사용해야 합니다.
* 해당 스캔 방법이 가장 흔히 사용하는 인덱스 조건입니다.

#### Index Unique Scan

<figure><img src="../.gitbook/assets/image (43).png" alt="" width="328"><figcaption></figcaption></figure>

* 수직적 탐색으로만 데이터를 찾는 방식입니다.
* 값이 하나만 존재하는 것을 보장하기 때문에 1번만 조회합니다.
* 따라서 등치(=) 조건으로 탐색하는 경우에 작동합니다.
* 예를 들면 PK로 조회할 때 사용됩니다.

#### Index Skip Scan

<figure><img src="../.gitbook/assets/image (46).png" alt="" width="328"><figcaption></figcaption></figure>

* 조건 절에 첫 번째 인덱스가 없어도 두 번째 인덱스 만으로 인덱스를 검색 할 수 있게 해주는 기능입니다.
* MySQL 8.0 버전에 인덱스 스킵 스캔 최적화 기능이 도입되면서 옵티마이저가 A 컬럼을 건너뛰어서 B컬럼만으로도 인덱스 검색이 가능하게 되었습니다.
* 단점으로는 첫 번째 인덱스의 유니크한 값의 개수가 적어야 하고,
* 쿼리가 인덱스에 존재하는 컬럼만으로 처리가 가능해야합니다.
* [참고하기 좋은 자료](https://github.com/woowacourse-study/2022-Real-MySQL/issues/18)

#### Index Full Scan

<figure><img src="../.gitbook/assets/image (41).png" alt="" width="375"><figcaption></figcaption></figure>

* 수직 탐색 없이 리프 블록 처음부터 끝까지 수평적으로 탐색하는 방식입니다.
* 보통 데이터 검색을 위한 최적의 인덱스가 없을 때 차선으로 선택됩니다.
* 인덱스 풀 스캔 후 필터 된 데이터를 대상으로 테이블에 엑세스 하는 것이 효율적입니다.

#### Index Fast Full Scan

<figure><img src="../.gitbook/assets/image (38).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

* 인덱스 테이블의 특정 부분만을 블록 단위로 스캔을 합니다.
* Index Full Scan과의 가장 큰 차이점은 정렬을 보장하지 않지만 병렬 읽기가 가능하다는 것입니다.
* 따라서 상대적으로 속도가 빠릅니다.
* 예를 들어, 2024년에 생성된 데이터만을 찾고 싶을 경우 인덱스 풀 스캔 보다는 Index Fast Full Scan으로 일정 부분의 블럭만을 스캔하는 것이 빠를 수 있습니다.

#### Index Merge Scan

<figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

* 여러 개의 인덱스를 병합하여 검색하는 방법입니다.
* 병합 방법으로는 합집합(union), 교집합(intersection) 방식이 있습니다.
* 단점으로는 인덱스의 크기가 작거나 데이터의 분포가 특정한 경우에 유용합니다.
* 데이터 테이블의 Access 비용을 줄일 수 있습니다.
  * 예를 들자면, A 인덱스 테이블에서 결과 값이 1, 2, 3 이 나오고 B 인덱스 테이블에서 2, 3, 4가 나오면,
    * union에선 1,2,3,4의 4번의 테이블 Access 비용만이 필요하게 되는데,
    * 만약 각각의 인덱스를 별개로 태운다면 1, 2, 3 / 2, 3, 4 총 6번의 Access 비용이 필요할 것 입니다.
* 단, 각 병합에는 인덱스의 양이 많을 경우 병합에 대한 오버헤드가 더 클 수 있어 다음 방식을 시도해 볼 수 있습니다.
  * SQL 문 자체를 독립된 하나의 인덱스만 사용하는 방법을 선택합니다.
  * 만약 위의 방법이 안된다면 별개로 생성된 인덱스들을 하나의 인덱스로 통합합니다.

#### Index Loose Scan

<figure><img src="../.gitbook/assets/image (34).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption><p><a href="https://12bme.tistory.com/138">https://12bme.tistory.com/138</a></p></figcaption></figure>

* 인덱스의 필요한 부분들만 골라서 스캔하는 방식입니다.
* 즉, 넓은 범위에 전부 접근하지 않고 WHERE 조건문 기준으로 불필요한 데이터의 인덱스 키는 무시합니다.
* GROUP BY, MAX(), MIN() 함수 포함 시 동작합니다.
* Index Loose Scan 불 충족 예제
  * 다른 집계 함수가 들어가 있는 있는 경우.
    * ```sql
      SELECT c1, SUM(c2) FROM t1 GROUP BY c1;
      ```
  * Group by 요소 중 인덱스의 첫 번째 요소가 들어가 있지 않은 경우.
    * ```sql
      SELECT c1, c2 FROM t1 GROUP BY c2, c3;
      ```
  * Group By 부분에 필요한 컬럼 들이 충분하지 않을 경우.
    * ```sql
      SELECT c1, c3 FROM t1 GROUP BY c1, c2; // WHERE에 c3 추가하면 가능
      ```



### 🍋 Hash Table

* 컬럼의 값으로 해시 값을 계산해서 인덱싱하는 알고리즘으로 매우 빠른 검색을 지원합니다.
* 하지만 값을 해싱하여 저장하기 때문에 한글자라도 바뀌면 전혀 다른 값이 되므로, 특정 문자로 시작하는 값 등을 검색할 경우 해시 인덱스를 사용할 수 없습니다.
* 위와 마찬가지의 이유로 = 동등 연산에는 특화되었지만 <,> 등의 부등호 연산에도 해시 인덱스를 사용할 수 없습니다.



### 🍌 B-Tree

DB와 파일 시스템에서 널리 사용되는 Self-balanced Tree입니다.

* 노드에는 2개 이상의 데이터가 들어갈 수 있으며 항상 정렬된 상태로 저장됩니다.

<figure><img src="../.gitbook/assets/image (84).png" alt="" width="246"><figcaption><p><a href="https://code-lab1.tistory.com/217">https://code-lab1.tistory.com/217</a></p></figcaption></figure>

* 최대 M개의 자식을 가질 수 있는 B 트리를 M차 B트리라고 하는데, 내부노드는 최소 ceil(M/2)개 부터 최대 M개의 자식을 가질 수 있습니다. (ceil() 은 소수점 무조건올림)

<figure><img src="../.gitbook/assets/image (85).png" alt="" width="512"><figcaption><p><a href="https://code-lab1.tistory.com/217">https://code-lab1.tistory.com/217</a></p></figcaption></figure>

* 특정 노드의 데이터가 K개 라면 자식 노드의 개수는 K+1개 여야 합니다.

<figure><img src="../.gitbook/assets/image (86).png" alt="" width="510"><figcaption><p><a href="https://code-lab1.tistory.com/217">https://code-lab1.tistory.com/217</a></p></figcaption></figure>

* 왼쪽 서브트리보다 오른쪽에 위치한 서브트리는 항상 더 큰 값들로 구성됩니다.
* 내부노드 내 데이터는 수는 항상 최소 ceil(M/2) - 1개부터 최대 M - 1개 이여야 합니다.

<figure><img src="../.gitbook/assets/image (87).png" alt="" width="547"><figcaption><p><a href="https://code-lab1.tistory.com/217">https://code-lab1.tistory.com/217</a></p></figcaption></figure>

* 균형다진트리로 모든 리프 노드들은 같은 레벨에 존재해야 합니다. (깨지면 다시 균형을 유지)

<figure><img src="../.gitbook/assets/image (89).png" alt="" width="563"><figcaption><p><a href="https://code-lab1.tistory.com/217">https://code-lab1.tistory.com/217</a></p></figcaption></figure>

* 이렇듯 구조를 유지하기 위해서는 추가적인 연산이 수행되거나 새로운 노드가 생성이 되는 단점이 존재합니다.
* **B-tree의 삽입 분할 삭제 과정**
  * [https://velog.io/@chanyoung1998/B%ED%8A%B8%EB%A6%AC](https://velog.io/@chanyoung1998/B%ED%8A%B8%EB%A6%AC)



### 🍍 B+Tree

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption><p><a href="https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/">https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/</a></p></figcaption></figure>

* leaf node에 모든 자료들이 존재하고, 그 자료들이 연결 리스트로 연결되어 있어 탐색에 매우 유리합니다.
* 따라서, 데이터 검색을 위해서는 반드시 leaf node까지 내려가야 한다는 특징을 가지고 있습니다.
* leaf node가 아닌 자료는 **인덱스 노드**라고 부르며, Value  값에 다음 노드를 가르키는 포인터 주소가 존재합니다.
* leaf node 는 **데이터 노드**라고 부르며, Value값에 데이터가 존재합니다.
* 데이터베이스에서 가장 중요한 것은 **검색 속도** 이므로 대부분의 현대 데이터베이스 시스템은 B+Tree구조를 채택하고 있습니다.



### 🥭 B\*Tree

* &#x20;B-Tree에서 자식 노드 수 최소 제약 조건이 2M/3개로 늘어났습니다.
* 노드가 가득하면 분열 대신 이웃한 형제 노드로 재배치 합니다.
* 더 이상의 재배치가 불가한 경우에 분열합니다.



### 🍎 Clustered Index 란?

데이터가 테이블에 클러스터형 인덱스의 키 순서로 물리적으로 정렬됩니다.

<figure><img src="../.gitbook/assets/image (92).png" alt="https://gwang920.github.io/database/clusterednonclustered/" width="563"><figcaption><p>Clustered 인덱스의 구조</p></figcaption></figure>

* Leaf level 과 DataPage가 동일한 구조를 갖습니다.
* 테이블 데이터는 오직 한 가지의 방법으로 정렬되기 때문에 테이블 당 하나의 클러스터 형 인덱스만 존재 할 수 있습니다.
* Clustred는 정렬을 위해 중간에 새로운 데이터가 삽입된다면 이후의 모든 컬럼을 한칸씩 이동시켜줘야 합니다.
* 1\~10000까지의 id가 있을경우 중간에 2를 새로 삽입한다면 기존의2\~10000까지의 컬럼을 순차적으로 1씩 증가시켜 이동 시켜야 합니다. (물론 상황에 따라 전체를 전부 밀지는 않을 수도 있습니다. B트리라면 분열 등)
*

    <figure><img src="../.gitbook/assets/image (91).png" alt=""><figcaption><p><a href="https://gwang920.github.io/database/clusterednonclustered/">https://gwang920.github.io/database/clusterednonclustered/</a></p></figcaption></figure>
* 검색속도는 향상시키는 장점이 있지만, 이처럼 새로운 데이터를 삽입할 때 많은 비용이 소모되는 단점이 존재합니다.
* 항상 정렬 된 방식으로 데이터를 반환해야 하는 경우에 사용하면 좋고, (Order By 대신)
* 자주 업데이트 되지 않는 테이블에서 사용하는 것이 좋아주로 읽기 작업이 많은 경우에 사용하면 좋습니다.



### 🍏 Clustered Index를 명시적으로 설정하지 않으면 어떻게 될까요?

* 보통PK를 자동 생성하여 사용한다면 데이터베이스에서는 PK가 클러스터 된 인덱스를 자동으로 생성하기 때문에 PK가 클러스터 인덱스가 됩니다.
* InnoDB 스토리지 엔진을 사용하는 경우, 기본 키는 클러스터 인덱스로 설정됩니다.
* 테이블에 PK를 명시적으로 정의하지 않고 테이블을 생성하는 경우, 테이블은 클러스터 인덱스 없이 생성될 수 있습니다.
* Clusterd Index없는 테이블은 힙 구조로 데이터가 저장됩니다.



### 🍐 Nonclusterd Index 란?

<figure><img src="../.gitbook/assets/image (94).png" alt="" width="518"><figcaption><p>논 클러스터 인덱스 구조</p></figcaption></figure>

* Clustered 구조와는 다르게 Leaf level과 DataPage가 따로 구분이 되어 있으며,  Data Page의 데이터는 정렬 되어있지 않습니다.
* 순서와 상관이 없으며 한 테이블에 여러개를 생성할 수 있습니다.
* Index를 저장할 테이블의 약 10%정도의 추가적인 공간이 필요합니다. (인덱스가DataPage에 포함되지 않음)

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption><p>논 클러스터형 예시</p></figcaption></figure>



### 🍍 Clusterd와 Nonclusterd를 같이 사용할 경우

DB를 사용하다 보면 클러스터 인덱스와 논 클러스터 인덱스를 같이 사용하는 경우가 많은데요. (PK + 기타 인덱스) 이런 경우에 어떻게 검색이 효과적으로 이루어지는지 간단히 설명을 드리겠습니다.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption><p>논 클러스터 조회 -> PK 얻음-> 클러스터 인덱스 조회</p></figcaption></figure>

먼저  논 클러스터 인덱스로만 조회한 쿼리를 실행 시키게 되면,

* 해당 쿼리에서 사용된 논 클러스터 인덱스로 우선 빠르게 데이터를 찾아냅니다.
* 하지만논 클러스터 인덱스 테이블은 리프노드에 데이터를 가지고 있지 않는데,&#x20;
* 이 리프노드들은 실제 데이터 위치가 아닌 클러스터 인덱스(기본키)의 포인터를 저장하고 있게 됩니다.
* 이로써찾아낸 클러스터 인덱스로 빠르게 실제 데이터 페이지를 조회 할 수 있게 됩니다.
* 이는 클러스터 인덱스에서만 물리적으로 정렬하여 데이터를 저장할 수 있게 되고,
* 나머지 논클러스터 인덱스 테이블들에서는 데이터가 변경 시 각 테이블마다 데이터를 정렬하지 않아도 되는 장점을 가질 수 있습니다.

#### 📚 참고자료

* [https://hudi.blog/db-clustered-and-non-clustered-index/](https://hudi.blog/db-clustered-and-non-clustered-index/)
* [https://so-what-93.tistory.com/81](https://so-what-93.tistory.com/81)



### 🍑 인덱스 실행 계획 확인하는 방법

#### EXPLAIN 방법

ex) `EXPLAIN SELECT * FROM your_table WHERE your_condition;`

* EXPLAIN은 쿼리 실행 계획을 분석하고 최적화 하기 위한 명령어입니다.
* 해당 명령어를 아래와 같이 쿼리 문 앞에 붙여주면 MySQL이 쿼리를 실행하는 방식과 사용하는 인덱스, 테이블의 읽기 순서 등에 대한 정보를 얻을 수 있습니다.
  * **possible\_keys**는 MySQL의 옵티마이저가 쿼리에서 사용할 수 있는 인덱스의 모음을 확인할 수 있고,
  * **key**는 해당 쿼리에서 실제로 사용한 인덱스를 확인 할 수 있습니다.



## 📜 로그

### 🍒 Binary log란? (binlog)

* 이진 로그 라고도 불리며 데이터베이스 시스템에서 수행된 변경 사항을 기록하는 파일입니다.
* 기본적으로 Transaction Commit 시에 기록되어지며 데이터 변경 순서를 보장한다는 특징이 있습니다.
* 에러코드, 바이너리 로그 자체에 대한 메타데이터 등 다양한 데이터가 포함됩니다.
* 원래는 Master DB서버를 Slave DB서버와 Replication하기 위한 것이 주목적 이였다고 합니다.



### 🍓 Binary log는 언제 사용할까?

1. **복구 (Recovery)**
   * 데이터베이스에서 문제가 발생했을 때 이진 로그를 사용하여 이전 상태로 복구할 수 있습니다.
   * 데이터베이스의 일관성을 유지하고 손상된 데이터를 복구 할 수 있습니다.
   * 복구 방법 예시 참고([https://hudi.blog/mysql-pit-recover/](https://hudi.blog/mysql-pit-recover/))
2. **복제 (Replication)**
   * 데이터베이스의 변경 사항을 다른 서버로 전송하여 레플리케이션 등을 구현하는데 사용됩니다.
   * 이를 통해 여러 서버 간에 데이터를 동기화 하고 가용성도 향상 시킬 수 있습니다.



### 🥝 Transaction log 란?

* 트랜잭션이 커밋될 때 마다 기록이 되며 보통사용자가 읽을 수 있는 형태로 기록됩니다.
* 트랜잭션에 대한 처리, 롤백, Crash Recovery, Point In Time Recovery 등을 위한 용도로 사용됩니다.
* 트랜잭션 로그는 데이터 변경의 일관성을 유지하고 데이터베이스의 장애 발생 시 복구하는 데 중점을 두고 있습니다.



## 📦 트랜잭션

### 🥑 트랜잭션이란?

* 트랜잭션은 데이터의 일관성과 무결성을 보장하며 다중 클라이언트와 같이 병렬적으로 발생하는 작업에서 발생할 수 있는 데이터 부정합을 방지하고자 할 때 사용됩니다.
* 일련의 수행을 하나의 논리적인 단위의 묶음으로 처리하며 정상적으로 성공하였을 경우에는 모든 변경사항을 저장하고,
* 어떠한 이유들로 인하여 중간에 비정상적인 종료가 되었을 경우 모든 변경사항을 처음과 동일한 상황으로 돌아갑니다.



### 🍏 트랜잭션 4가지 특성(ACID)

#### 원자성(Automicity)

* 트랜잭션에서 정의된 연산들은 모두 성공적으로 실행되던지 모두 전혀 실행되지 않은 상태로 남아 있어야 합니다. (전부 성공 또는 전부 롤백)

#### 일관성(Consistency)

* 트랜잭션 시작 전과 완료 후에도 데이터 베이스가 일관된 상태로 유지되어야 합니다.

#### 격리성(Isolation)

* 모든 트랜잭션은 다른 트랙잭션으로부터 독립되어 있어야 합니다.
* 즉, 여러개의 트랜잭션이 실행될 때 서로에게 영향을 끼치면 안되고, 연속으로 실행 된 것과 동일한 결과를 나타내야 합니다.

#### 지속성(Durability)

* 트랜잭션이 성공적으로 수행되면 그 트랜잭션이 갱신한 데이터베이스의 내용은 영구적으로 저장된다.
* 트랜잭션이 성공적으로 완료 되었을 경우, 시스템 장애 또는 기타 문제가 발생하더라도 트랜잭션의결과는 보존되어야 합니다.



### 🍐 TCL이란?

* Transaction Control Language의 약자로 DBMS에서 트랜잭션을 제어하기 위한 언어입니다.
* 주로 트랜잭션의 관리, 커밋, 롤백 등의 관련된 작업을 수행합니다.
* 명령어로는 COMMIT, ROLLBACK, SAVEPOINT 가 있습니다.
  * COMMIT: 트랜잭션의 변경사항을 영구적으로 데이트베이스에 적용하고 종료합니다.
  * ROLLBACK: 트랜잭션의 모든 변경 사항을 취소하고 이전(기존) 상태로 되돌립니다.
  * SAVEPOINT: 트랜잭션을 잘게 분할하여 트랜잭션 내에서 지정한 곳까지 롤백 할 수 있습니다.
