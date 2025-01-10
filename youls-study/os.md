# 💻 OS

## 🍴 fork() 란?

* 유닉스와 같은 운영체제에서 프로세스가 자기 자신을 복제하는 동작입니다.
* Kernel 내부에 구현되어 있는 System Call의 일종이며 프로세스를 만드는 주된 방식입니다.



### fork()를 수행하는 이유

* **동시성 향상 및 병렬성 증가**
  * 부모 프로세스가 자식 프로세스를 생성하여 동일한 코드를 병렬적으로 실행하기 때문에 동시 처리 능력이 향상됩니다.
* **멀티테스킹 가능**
  * 부모와 자식 프로세스가 독립적으로 실행 되어 서로에게 영향을 끼치지 않고 안전하게 병렬적으로 작업이 가능합니다.
* **프로세스 생성 오버헤드 감소**
  * 거의 동일한 로직을 반복하여 수행해야 하는 경우 프로세스를 새로 생성하는 오버헤드가 더 발생 할 수 있어 복제하여 사용하는 것이 더 효과적입니다.
  * 또한 어떠한 프로세스가 다른 프로그램을 실행해야 하는 경우에도 처음부터 생성하는 것보단 복제하여 exec()를 사용하여 덮어씌우는 것이 더 오버헤드가 적습니다.
* **IPC를 통한 프로세스 간 통신**
  * fork()를 사용하여 만든 프로세스간에는 IPC(inter-Process Communication)를 통해 서로 통신 할 수 있습니다.



### fork() 자식 프로세스 변환 예시

* 자식 프로세스가 생성되면 우선 실행 중 바뀌지 않는 텍스트(text, 프로그램 코드)영역은 공유합니다.
* 그리고 나머지 부모 프로세스의 Stack, Heap, Data 뿐 아니라 부모 프로세스의 PCB(Process Control Block)도 그대로 복사합니다.



<figure><img src="../.gitbook/assets/image (77).png" alt=""><figcaption><p><a href="https://wslog.dev/fork-exec">https://wslog.dev/fork-exec</a></p></figcaption></figure>

그러면 아래와 같이 부모 process1, 자식 process2가 생성됩니다.

<figure><img src="../.gitbook/assets/image (78).png" alt=""><figcaption><p><a href="https://wslog.dev/fork-exec">https://wslog.dev/fork-exec</a></p></figcaption></figure>

이 때 부모 프로세스에서 Page C의 데이터가 변경되면&#x20;

<figure><img src="../.gitbook/assets/image (79).png" alt=""><figcaption><p><a href="https://wslog.dev/fork-exec">https://wslog.dev/fork-exec</a></p></figcaption></figure>

COW로 인해 공유하고 있던 데이터의 수정이 발생하였으므로 page C를 복사하여 다른 물리주소에 올려 수정을 하고, 수정이 발생한 프로세스인 부모 프로세스의 주소 참조를 변경하게 됩니다.



### COW 란?

* **Copy on Write**의 약자로 메모리 관리 기술 중 하나 입니다.
* 주로 프로세스 간 메모리 공유를 효율적으로 관리하기 위한 방법입니다.
* 예를 들어 fork() 를 사용하면 부모 프로세스와 자식 프로세스가 처음에는 동일합니다.&#x20;
  1. 따라서 먼저 복제를 하지 않고 우선 메모리를 공유하며 진행합니다.
  2. 그러다 다른 한쪽 프로세스에서 데이터가 변경되어 공유하지 못할 상황이 왔을 때
  3. 그 때 복사를 하여 수정된 데이터와 원본 데이터를 분리하는 방법입니다.



## IPC란?

* **Inter-Process Communication**의 약자로 프로세스 간에 데이터를 주고 받을 수 있는 행위를 뜻합니다.
* 주로 멀티프로세스 환경에서나 서로 다른 프로그램 간에 데이터를 전송하거나 공유할 때 사용됩니다.
* 주로 메시지 큐, 공유 메모리, 파이프, 소켓, 신호 등이 있습니다.



### IPC 중 공유 메모리에서 한 번 공유된 이후에 커널의 도움이 필요한지?

* 커널의 도움은 한 번 공유메모리를 만들 때 이후에는 더이상 커널의 도움이 필요하지 않습니다.
* 왼쪽 그림은 커널의 도움을 받는 메시지 전달 방법이고
* 오른쪽 그림은 공유메모리를 통해 커널의 개입 없이 메모리를 공유하는 모습입니다.
*

    <figure><img src="../.gitbook/assets/image (80).png" alt=""><figcaption><p>공룡 책 이미지</p></figcaption></figure>



## PCB 란?

* Process Control Block 으로 운영체제가 프로세스의 정보를 저장하는 자료 구조입니다.
  1. 프로세스 식별자
  2. 프로세스 상태: 생성, 준비, 실행, 대기, 종료
  3. 프로그램 계수기 (PC: Program Counter): 해당 프로세스가 다음번에 실행 할 명령어의 주소를 가르킵니다.
  4. CPU 레지스터 및 일반 레지스터: 스택 포인터(SP), 누산기(A), 명령어 레지스터(IR) 등
  5. CPU 스케줄링 정보: 우선순위, 스케줄링 알고리즘, CPU 점유시간, 최종 실행시각 등
  6. 메모리 관리 정보: 해당 프로세스 주소 공간 등
  7. 프로세스 계정 정보: 페이지 테이블, 스케줄링 큐 포인터, 소유자, 부모 프로세스 등
  8. 입출력 상태 정보: 프로세스에 할당된 입출력 장치, 열린 파일 목록 등
* PCB는 프로세스가 생성되고 종료될 때 까지 해당 정보들이 유지되며,
* 프로세스 간 전환 시 현재 실행 중인 프로세스를 PCB에 저장하고,
* 다음에 실행 될 프로세스의 PCB를 읽어 CPU 레지스터 등을 복원하는 등
* 멀티프로세스 환경에서 효율적으로 프로세스를 관리 할 수 있습니다.



### PCB는 어디에 저장되고 있을까요?

* PCB는 프로세스의 중요한 정보를 포함하고 있기 때문에 주로 운영체제의 커널 스택에 저장되고 있습니다.
  * 커널 스택에는 인터럽트 처리, 시스템 호출, 예외 처리 같은 운영체제 핵심 부분에서 실행되는 코드의 호출 스택 정보가 저장됩니다.&#x20;
  * 이는 각 프로세스나 스레드마다 별도의 커널 스택이 할당되어 격리 되어 있습니다.
  * 일부 OS는 편리성과 효율성을 위해 PCB를 커널 스택의 처음에 위치 시켜 PCB 주소에 빠르게 접근 할 수 있도록 합니다.



## 프로세스 동기화 하는 방법

* **뮤텍스**
  * 공유 자원에 대한 접근을 조절하기 위한 가장 기본적인 방법입니다.
  * 잠금과 해제 연산을 제공하여 한 번에 하나의 프로세스 또는 스레드 만이 특정 자원에 접근 할 수 있습니다.
  * 뮤텍스를 특정 종류의 세마포어 형식으로 부를 수 있습니다.
* **세마포어**
  * 여러 프로세스 또는 스레드가 동시에 접근을 허용 할 수 있는 정수 카운터를 사용합니다.
  * 뮤텍스와 유사하지만 세마포어는 뮤텍스가 될 수 없습니다.
* **조건 변수 (signal)**
  * 이는 주로 뮤텍스와 함께 사용됩니다.&#x20;
  * 스레드는 조건 변수 x를 기다리는 동안 뮤텍스를 해제하고
  * 다른 스레드가 특정 조건을 만족하여 조건 변수x에 signal를 보내면,
  * x를 기다리던 스레드가 깨어나 뮤텍스를 얻고 작업을 수행합니다.
* **스핀 락**
  * 반복문을 돌며 계속 기다리는 방식입니다.
  * 뮤텍스와다른점은 CPU 자원을 계속하여 소모하기 때문에 대기 시간이 짧은 경우에 효과적입니다.
* **데드락 방지 기법**
  * **상호배제 / 점유와 대기 / 비선점 / 순환 대기** 이4가지 조건 중 하나를 제거하는 방법입니다.



## 🍏 메모리

### 가상 메모리를 왜 사용할까요?

<figure><img src="../.gitbook/assets/image (109).png" alt=""><figcaption><p><a href="https://www.yes24.com/Product/Goods/62054527">https://www.yes24.com/Product/Goods/62054527</a></p></figcaption></figure>

* **실제 물리적인 메모리보다 더 큰 메모리를 사용하는 것**처럼 느낄 수 있습니다.
  * 이는 프로그램 실행 시 실제 필요한 메모리만을 할당 받아 올리고 나머지는 디스크의 swap area에 저장하여 사용 할 수 있습니다.
* 메모리에 대한 제한된 액세스 권한을 부여하여 메모리를 보호할 수 있습니다.
* 필요한 경우 스와핑과 페이지 아웃을 통해 물리적인 메모리에서 디스크로 쫓아 낼 수 있습니다.
* 가상 메모리는 미리 읽기와 지연된 할당 등의 기술을 통해 성능을 향상 시킬 수 있습니다.
  * 필요한 페이지들을 미리 물리적 메모리로 가져오거나, 페이지를 실제로 필요한 시점에 할당하여 성능을 향상 시킬 수 있습니다.
* 각 프로세스는 독립적으로 실행되지만 메모리를 공유해야 할 때가 있어 이 때 가상 메모리를 통해 독립된 가상 주소 공간을 제공하여 프로세스 간에 충돌을 방지하고 보호 할 수 있습니다.



### 외부 단편화 vs 내부 단편화

<div align="left"><figure><img src="../.gitbook/assets/image (110).png" alt="" width="548"><figcaption><p><a href="https://www.yes24.com/Product/Goods/62054527">외부 단편화</a></p></figcaption></figure> <figure><img src="../.gitbook/assets/image (114).png" alt="" width="563"><figcaption><p><a href="https://www.yes24.com/Product/Goods/62054527">(고정 분할 시) 내부 단편화</a></p></figcaption></figure></div>

#### 외부 단편화

* 외부 단편화는 프로그램을 할당하며 생기는 작은 메모리들이 더 이상 할당되지 않는 조각들이 발생하는 문제입니다.&#x20;
* 이는 주로 동적 할당 할 때 많이 발생합니다.

#### 내부 단편화

* 내부 단편화는 메모리 내에서 프로세스가 할당 받은 공간이 실제로 사용하고 있는 공간보다 커서 발생하는 문제입니다.
* 주로 고정 분할 방식을 사용할 때 발생합니다.



### 스택 vs 힙

<div><figure><img src="../.gitbook/assets/image (116).png" alt="" width="563"><figcaption><p><a href="https://velog.io/@sarang_daddy/CS-Memory">https://velog.io/@sarang_daddy/CS-Memory</a></p></figcaption></figure> <figure><img src="../.gitbook/assets/image (117).png" alt="" width="314"><figcaption></figcaption></figure></div>

|        |              Stack              |                 Heap                |
| :----: | :-----------------------------: | :---------------------------------: |
|  할당 방법 |           컴파일 시 정적 할당           |             런타임 시 동적 할당             |
|  할당크기  |          비교적 작은 메모리 영역          |             비교적큰 메모리 영역             |
|  생명 주기 | <p>함수 호출 시 생성<br>함수 종료 시 소멸</p> | <p>명시적 해제 없을 경우<br>프로그램 종료 시 까지</p> |
|   접근성  |            로컬 변수에만 접근           |              전역 변수에 접근              |
|  자료구조  |               선형적               |               계층적(트리)               |
| 구현 난이도 |              비교적 쉽다             | <p>비교적 어렵다<br>(메모리 누수, 오류 방지 등)</p> |



## 쓰레드와 프로세스

### 프로세스 간의 통신 방법 (IPC : Inter Process Communication)

#### 📨 메세지를 전달하는 방법 (Message Passing, PIPE)

* 커널을 통해 메세지를 전달합니다.
* 프로세스 간에 메세지를 주고받을 때 메세지 큐, 파이프, 소켓 등을 사용할 수 있습니다.
* Direct Communication: 통신하려는 프로세스의 이름을 명시적으로 표시 하여 직접 통신하는 방법입니다. (커널을 거치긴 함.)
* Indirect Communication: 커널에 있는 mailbox를 통해 메세지를 간접적으로 받는 경우입니다.

#### 🥜 주소 공간을 공유하는 방법 (Shared Memory)

* 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 합니다.
* 그만큼 신뢰할 수 있는 프로세스 들끼리만 해야합니다.
* 이 공유 메모리 영역을 통해 데이터를 공유하고 효율적으로 전달할 수 있습니다.
* 프로세스들끼리 쉐어드 메모리를 할 수 있도록 최초에는 동일하게 커널의 도움을 받아야 할 수 있지만
* 한번 할당 받고 난 이후로는 커널의 도움 없이 프로세스 간의 통신을 진행 할 수 있습니다.

이외에도 **Message Queue, Memory Map, Socket** 등이 있습니다.



### 크롬 브라우저의 탭은 프로세스일까 쓰레드 일까?

아마 이미 쓰레드에 대한 학습을 해보셨던분들이라면 하나의 프로세스를 여러 탭에서 쓰레드로 효율적으로 재사용 할 것 같지만,

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p><a href="https://developer.chrome.com/blog/inside-browser-part1?hl=ko#the_benefit_of_multi-process_architecture_in_chrome">https://developer.chrome.com/blog/inside-browser-part1?hl=ko#the_benefit_of_multi-process_architecture_in_chrome</a></p></figcaption></figure>

정답을 말씀드리자면 아래의 이유들을 통해 크롬의 브라우저 탭은 각각의 **프로세스**로 이루어져 있습니다.&#x20;

* 만약 모든 탭이 하나의 프로세스에 여러 쓰레드로 사용하는 환경이였다면,
* 하나의 페이지가 오류가 발생하여 응답이 되지 않을 경우 위의 사진과 같이 다른 탭의 페이지에도 응답을 기다리기 위해 동일하게 응답하지 않을 것 입니다.
* 따라서 특정 탭이 응답하지 않을 경우, 해당 탭의 프로세스를 재시작하여 문제를 해결하는 방식을 채택하고 있습니다.
* 단, 유추할 수 있듯이 멀티 쓰레드 방식에 비해서 멀티 프로세스는 높은 성능을 요구하게 됩니다.
* 그럼에도 크롬은 이러한 방식을 선택한 이유로는 웹 사이트를 별도의 심사 없이 배포하기 때문에 악성웹 사이트에게 모든 공유 자원을 주어 더 큰 문제의 발생을 막기 위함으로 볼 수 있습니다. (~~왠지 메모리를 맨날 많이 잡아먹더라니..~~)

#### 🍀참고로! 하나의 탭에도 하나의 프로세스가 아닌 멀티 프로세스를 사용하고 있다고 합니다.

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

* 하나의 탭을 구성할 때에도 브라우저 프로세스, 렌더러 프로세스, 플러그인  프로세스, GPU 프로세스 등 여러 프로세스 들이 모여 하나의 브라우저를 그리게 되는데,
* 이외에도 그림에는 포함되지 않은 확장 프로세스, 유틸리티 프로세스 등과 같은 더 많은 프로세스들이 존재하고 있다고 합니다.

#### 🍀 또한 최근에는 페이지 내부에 각각의 **iframe마다 사이트 격리**를 목적으로 사이트와는 별도의 렌더기 프로세스를 사용하여 구현하고 있는데,

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

* 이는 동일한 렌더기 프로세스를 사용하여 iframe을 실행하게 된다면 한 사이트에서 동의 없이 다른 사이트의 데이터를 액세스 할 수 있고,
* 이렇게 우회하는 방식으로 **보안 공격의 목표**가 될 수 있기 때문이라고 합니다.

#### 📚 참고자료

[https://developer.chrome.com/blog/inside-browser-part1?hl=ko#browser-architecture](https://developer.chrome.com/blog/inside-browser-part1?hl=ko#browser-architecture)



### PCB 란? (Process Control Block)

<figure><img src="../.gitbook/assets/image (20).png" alt="" width="563"><figcaption><p><a href="https://byjus.com/gate/process-control-block-notes/">https://byjus.com/gate/process-control-block-notes/</a></p></figcaption></figure>

* PCB란 특정 프로세스에 대한 정보를 추적하는 데이터 구조로 운영체제가 프로세스를 표현 한 것이라고 할 수 있습니다.
* 운영체제의 커널영역에 저장되어 있는 자료구조로 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보입니다.
* PCB 구조 정
  * OS가 관리상 사용하는 정보
    * 프로세스 ID, 프로세스 상태(Ready, Running, Blocked)
    * scheduling information(스케줄링 정보), priority(우선순위)
  * CPU 수행 관련 하드웨어 값
    * PC(Program counter), registers
  * 메모리 관련
    * Code, Data, Stack 들의 위치 정보.
  * 파일관련
    * Open file descriptors
    * 오픈하고 있는 파일의 정보, 리소스와 관련 된 정보들

&#x20;

### 프로세스와 쓰레드의 메모리 영역

#### 프로세스

<figure><img src="../.gitbook/assets/image (31).png" alt="" width="375"><figcaption><p><a href="https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html">https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html</a></p></figcaption></figure>

* **코드 영역**: 실행 할 프로그램의 명령어가 저장되는 곳 입니다.
* **데이터 영역**: 전역 변수 및 정적 변수가 저장되는 곳 입니다.
* **스택 영역**: 지역 변수 및 함수 호출마다 스택 프레임이 생성됩니다.
* **힙 영역**: 동적으로 할당된 메모리가 저장되는 공간입니다.

#### 쓰레드

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption><p><a href="https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html">https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html</a></p></figcaption></figure>

* 모든쓰레드의 코드, 데이터, 힙 영역은 프로세스와 동일합니다. (공유)
* 때문에 동기화 관련된 문제에 대한 주의가 필요합니다.
* 스택 영역만을 독립적인 스택으로 가지고 있습니다.
* 또한 쓰레드 간에 스택 정보는 공유 되지않습니다.
* 따라서 쓰레드는 프로세스 내에서 실행 되는 여러 실행 흐름으로 볼 수 있습니다.



### 멀티 쓰레드의 장/단점

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption><p><a href="https://velog.io/@eunjin/OS-%EC%8B%B1%EA%B8%80%EC%8A%A4%EB%A0%88%EB%93%9C-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%9D%98%EB%AF%B8">그림 참조</a></p></figcaption></figure>

#### 장점

* 프로세스의 코드, 데이터, 힙 영역을 공유하고 있기 때문에 자원을 효율적으로 사용할 수 있고,
* 쓰레드 간에 컨텍스트 스위칭 비용을 상당히 아낄 수 있습니다. (전혀 없는 것은 아님)
* 또한 프로세스 간의 통신(IPC)은 대게 불편하거나 시스템 콜을 발생 시키는 반면에 쓰레드 간에 데이터를 주고 받는 것은 간단해져 공유에 대한 자원 소모를 줄일 수 있습니다.
* 작업을 쉽게 여러 쓰레드 단위로 나누어 빠른병렬 처리를 기대 할 수 있습니다.

#### 단점

* 하나의 프로세스에서 여러 쓰레드가 자원을 공유하므로 동기화 관련 문제가 발생합니다.
* 하나의 스레드에 문제가 발생하면 전체 프로세스가 영향을 받습니다.



### Context Switching에 대해 설명해주세요.

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption><p><a href="https://byjus.com/gate/context-switching-in-os-notes/">https://byjus.com/gate/context-switching-in-os-notes/</a></p></figcaption></figure>

* Context switching이란 CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정입니다.
* 현재 실행 중인 프로세스가 다른 I/O 등의 리소스를 필요로 할 경우 CPU는 대기를 해야 하는데,&#x20;
* 이 때, 대기하는 시간동안 CPU가 사용되지 못하므로 CPU의 최대한의 효율을 내기 어렵습니다.
* 그로인해 빠른 CPU를 더 효율적으로 사용하기 위해 컨텍스트 스위칭을 사용하게 됩니다.



### Context Switching 비용이 왜 발생 할까요.

* Context Switching이 발생하게 되면 실행 중이던 프로세스의 정보를(CPU에 있던 레지스터와 PC 등) 운영체제 커널 영역 내에 존재하는 PCB에 저장 하게 됩니다.
* 또한 다음에 실행하게 되는 Context(Process)에 대한 정보를 해당 PCB를 통해 가져오게 되는데,
* 이 때 마다 캐시 정보를 flush 해야 하므로 상당한 오버헤드가 발생합니다.

#### 🤔 그렇다면 Context Switching이 오히려더 발생하지 않을까 고민을 해볼 수 있을 텐데요.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

* 오늘날의 일반적인 타임 슬라이스는 10ms - 100ms 사이라고 하고,
* 컨텍스트 스위치 오버헤드 스위치는 대략 0.1ms – 1ms가 걸린다고 합니다.

때문에 오늘날의 약 1% 정도의 문맥 교환 비용은 CPU의 유휴 시간을 가지는 것보다 감당할 수 있는 수준이기 때문에 오늘날에 Context Switching가 사용된다고 합니다.



## 스케줄링

### CPU 스케줄링이란?

<figure><img src="../.gitbook/assets/image (27).png" alt="https://itwiki.kr/w/%ED%8C%8C%EC%9D%BC:%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4_%EC%83%81%ED%83%9C%EC%A0%84%EC%9D%B4%EB%8F%84_%EC%83%81%EC%84%B8.png"><figcaption></figcaption></figure>

* 운영체제의 CPU 스케줄러는 Ready 상태의 프로세스 중에서 어떤 프로세스에게 CPU를 할당할 지 결정합니다.
* 여러 프로세스가 동시에 실행될 수 있는 시스템에서 CPU 스케줄링은 시스템의 성능, 응답 시간, 처리량 등을 관리하고 최적화하기 위해 중요한 역할을 합니다.
* 그로인해 다양한 CPU 스케줄링 알고리즘이 있으며, 각각의 알고리즘은 특정 상황이나 요구사항에 맞게 선택되어야 합니다.



### Multilevel Queue 스케줄링이란?

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p><a href="https://www.javatpoint.com/multilevel-queue-scheduling-in-operating-system">https://www.javatpoint.com/multilevel-queue-scheduling-in-operating-system</a></p></figcaption></figure>

* Multilevel Queue란 우선순위에 따라 Ready Queue를 여러개의 foreground 큐와 background 큐로 분할 하는 방법입니다.
* 이는 시스템에서 다양한 작업들 간에 우선순위를 부여하여 효율적으로 관리할 수 있게 해줍니다.
* 또한, 각 큐에는 특정 우선순위의 작업들만을 관리하므로 처리 시에 우선순위에 따라 적절한 작업을 선택할 수 있습니다.&#x20;
* 이는 시스템의 성능과 응답 시간을 최적화하는 데 도움이 됩니다.
* 하지만 높은 우선순위를 가지는 foreground 큐에 계속하여 프로세스가 많으면 background 큐에는 Starvation(기아현상)이 발생할 수 있으므로,
* 낮은 우선순위 background 큐에게도 CPU time을 적절하게 배분해주어야 합니다. (예를 들어 7:3 배분)



### CPU가 여러 개 일 때 왜 멀티레벨 큐가 왜 적합할까

* 우선 멀티레벨 큐를 사용하는 목적은 우선순위에 따라 효율적으로 스케줄링을 하기 위함에 있는데요.
* 그 중에서 CPU가 여러 개 일 경우에 장점으로는 꼽는다면 병렬 처리를 효과적으로 할 수 있습니다.
* 만약 하나의 CPU만을 사용한다면 우선 순위가 높은 작업만 계속 실행 될 수 있고 이는 기아 현상이 발생 할 수 있습니다.
* 하지만 여러개의 CPU를 사용한다면 우선순위가 더 높은 큐에는 CPU를 더 많이 배분해주는 대신 다른 큐의 있는 작업들도 병렬적으로 실행하며 전체 시스템 처리율을 높일 수 있습니다.



### 비선점 스케줄링 VS 선점 스케줄링

#### 선점형(preemptive)

CPU를 강제로 중단시키고 빼앗는 경우에 해당합니다.

* Timer Interrupt 발생에 의해 할당 시간 만료로 CPU를 반납할 때. (Running -> Ready)
* System call 등이 발생한 경우. (Running -> Blocked)
* I/O 작업 수행 완료 시 해당 작업이 우선순위가 높은 경우. (Blocked -> Ready)

#### 비선점형(nonpreemptive)

CPU를 자진 해서 반납할 때까지 계속 실행 되는 것을 말합니다.

* 오래걸리는 I/O를 요청하는 시스템 콜 발생으로 인해 자진해서 CPU를 반납 할 때. (Running -> Blocked)
* 프로세스 종료에 의해 CPU 반환 할 때. (Terminate)
* 이렇듯 위와 같은 상황이 아니라면 계속하여 실행되기 때문에 Context Switching 비용은 거의 없지만 대기중인 프로세스들이 지연되어 전체 시스템의 처리율이 떨어질 수 있습니다.



### 선점 스케줄링의 예시

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption><p>Round-Robin 스케줄링</p></figcaption></figure>

선점 스케줄링에는 SJN, SJF, 멀티레벨 큐 등 여러가지가 있지만 현대의 CPU 스케줄링의 기본이 되고 있는 **Round Robin 스케줄링**을 예시로 들어보겠습니다.

* Round Robin은 모두에게 공평한 자원 기회를 주기 위해 각 프로세스 별로 일정 시간을 실행 시키고 컨텍스트 스위칭을 통해 번갈아 가면서 실행을 합니다.&#x20;
* 이 때 할당 시간이 만료 되면 OS의 CPU 스케줄러는 강제로 CPU를 뺏고 다른 프로세스에게 넘겨주게 됩니다.



## 🔄️ 동기화

### 임계 영역이란? (Critical Section)

* 임계영역이란 **공유 변수 영역**으로 볼 수 있는데 병렬 컴퓨팅에서 **둘 이상의 스레드가 동시에 접근해서는 안되는 자원을 접근하는 코드의 일부**를 말합니다.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>



### 임계 영역에 들어가는 것을 막는 방법은?

* 임계 영역에 들어가는 것을 정하기 위한 방법으로는 **뮤텍스, 세마포어, 모니터** 등의 해결 방법 등이 있습니다.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

* 밑에서 설명 드릴 뮤텍스, 세마포어, 모니터 등을 활용한 일종의 LOCK을 걸어 해당 변수에 대한 접근을 막고 순차적으로 실행 할 수 있도록 합니다.



### &#x20;경쟁상태 란? (Race Condition)

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

* **둘 이상**의 입력 또는 **조작의 타이밍이나 순서** 등이 **결과 값에 영향을 줄 수 있는 상태**를 말합니다.
* 즉, 하나의 자원에 여러 개의 프로세스가 동시에 접근을 시도할 때 자료의 일관성을 해치는 결과가 나타날 수 있습니다.



### &#x20;기아상태 란? (Starvation)

**특정 프로세스**의 우선 순위가 낮아 **원하는 자원을 계속하여 할당 받지 못하는 상태**를 의미합니다.

<figure><img src="../.gitbook/assets/image (6).png" alt="" width="450"><figcaption><p><strong>http://www.aleclongstreth.com/</strong></p></figcaption></figure>

* 예를 들면, 위 사진과 같이 우선순위가 낮은 프로세스들이 대기하고 있어도 높은 우선순위의 프로세스가 끼어들 경우 낮은 우선순위의 프로세스들은 계속 대기 해야합니다.
* **기아상태 해결방법**
  1. 오래 기다린 프로세스의 우선순위 높이는 방법이 있습니다.
  2. 상황에 따라 우선순위가 아닌 요청 순서대로 처리하는 요청큐 사용하는 방법이 있습니다.



### 교착상태 란? (Deadlock)

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>서로 기다리며 아무일도 진행하지 못하는 상황</p></figcaption></figure>

* 교착 상태란 두 개 이상의 작업이 서로의 작업이 끝나기 만을 기다리며 서로 아무런 행동을 취하지 못하는 상황을 이야기 합니다.
* 아직까진 이 문제를 해결하는 일반적인 확실한 정해진 방법은 없는 상태입니다.
  * 대신 아래 설명에 [여러가지 해결 방안](https://jiyoul.gitbook.io/question/undefined/os#undefined-22)은 존재합니다.

#### ⚖️ 교착상태 발생 조건

1971년 E. G. 코프만 교수가 정의하였던 교착 상태가 발생하기 위한 **4가지 필요 조건**이 있습니다.

* 🔒 **상호배제 (Mutual Exclusion)**
  * 매 순간 하나의 프로세스만이 자원을 사용할 수 있습니다.
* **⏯️ 점유대기 (Hold and Wait)**
  * 프로세스가 다른 자원을 기다리며 자신이 보유한 자원을 내놓지 않고 계속 가지고 있습니다.
* **🛤️ 비선점 (No Preemption)**
  * 자원을 본인 프로세스만 스스로 반환할 수 있어,  다른 프로세스가 강제로 빼앗지 못하는 상태를 말합니다.
* **🔀 순환대기 (Circular Wait)**
  * 자원을 기다리는 프로세스간에 사이클이 형성되어 있습니다.
  * ex) 프로세스 P₁, P₂, P₃가 있을 경우
    1. P₁ -> P₂ 자원을 기다림.
    2. P₂ -> P₃ 자원을 기다림.
    3. P₃ -> P₁ 자원을 기다림.



### 교착상태 해결 방안

**데드락** **방지 / 회피 / 감지 및 복구 / 무시** 의 4가지 방법들을 소개해 드리겠습니다.

#### **🛡️&#x20;**<mark style="background-color:orange;">**데드락 방지 (Deadlock Prevention)**</mark>

* 자원 할당 시 Deadlock 발생 조건의 **4가지 중 하나라도 만족하지 못하도록 방지**하는 방법입니다.
  1. **Mutual Exclusion 방지**
     * 공유해서는 안되는 자원의 경우 반드시 동시에접근이 불가능 하도록 해야 합니다.
  2. **Hold and Wait 방지**
     * 프로세스 시작 시부터 종료 시까지 필요한 모든 자원을 할당 받고 시작하여 다른 자원을 요청할 필요가 없도록 하는 방법입니다. (자원 이용도 측면에선 비 효율적)
     * 새로운 자원이 필요할 경우 현재 보유하고 있던 모든 자원을 반납하고, 요청 하는 방법을 사용합니다. (반납한 순간 다른 프로세스가 점유하여 기아 현상 발생 가능성)
  3. **No Preemption 방지**
     * 프로세스가 어떤 자원이 필요할 때 이미 해당 자원이 다른 프로세스에 의해 이미 선점 되어 있을 경우, 모든 필요한 자원을 얻을 수 있을 때 프로세스를 다시 시작합니다.
     * 위와 같은 작업을 쉽게 하기 위해 자원을 쉽게 빼앗고 빼앗길 수 있도록 합니다.
       * 예를 들어 CPU의 컨텍스트 스위칭에서 PCB와 같이 자원을 빼앗더라도 쉽게 상태를 저장하고 복원 할 수 있도록 해야 합니다.
  4. **Circular Wait 방지**
     * 자원의 할당 순서를 정하여 정해진 순서대로만 자원을 할당 받게 합니다.

#### **🪖&#x20;**<mark style="background-color:orange;">**데드락 회피 (Deadlock Avoidance)**</mark>

자원 요청 시 받는 **부가적인 정보**를 이용해서 **데드락의 가능성이 없는 경우에만 자원을 할당**하는 방법입니다.

* **Safe state 해야합니다.**
  * 시스템 내의 프로세스들에 대한 **safe sequence**가 존재하는 상태이어야 합니다.
    * safe sequence란 쉽게 설명해서 현재 자원이 부족하여 실행 하지 못하는 프로세스가 있더라도 다른 프로세스부터 끝내고 돌려받은 자원으로 현재 프로세스를 만족 할 수 있다는 상태가 될 수 있는 것 입니다.
  * 해당 상태가 만족 되지 못하면 unsafe state한 상태이므로 Deadlock이 발생 할 가능성이 존재합니다.
* **회피 알고리즘**
  * **자원 할당 그래프 알고리즘** **(Resource Allocation Graph Algorithm)**
    * **단일** 인스턴스 경우에 사용됩니다.&#x20;
    * 자원을 할당 요청 시 할당해주더라도 사이클이 생성되지 않는 경우에만 요청 자원을 할당 하여 줍니다.
    * Cycle 생성여부 조사 시에는 프로세스 수가 n일 때 O(n²) 만큼 걸립니다.
  * **뱅커스 알고리즘 (Banker's Algorithm)**
    * **다중** 인스턴스 경우에 사용됩니다.&#x20;
    * 어느 프로세스가 최대로 사용할 자원 수가 현재 남아있는 자원보다 많을 경우 남아있는 자원이 더 많아질 때까지는 전혀 할당하여 주지 않습니다.
    * 예를 들어 현재 사용 가능한 자원이 10이 있을 때,
      * 한 프로세스가 최대 사용자원은 12이고 현재 당장 사용해야 하는 자원은 6일 경우에도 할당하여 주지 않습니다.
      * 한 프로세스가 최대 사용자원은 10이고 현재 사용해야 하는 자원도 10일 경우에는 할당하여 줍니다.
    * 위와 같이 현재 **당장 자원이 남아있더라도** 혹시 몰라서 **할당하여 주지 않으므로 비효율적**입니다.

#### **🚑&#x20;**<mark style="background-color:orange;">**데드락 감지 및 복구 (Deadlock Detection and Recovery)**</mark>

**데드락 발생은 허용**하되 발생하면 **감지해서 복구**를 하는 방법입니다.

* **데드락 찾는 방법**
  1. **단일** 인스턴스인 경우
     * Corresponding Wait-for Graph
       * 자원 할당 그래프에서 자원을 빼고 프로세스들 끼리만 연결하는 변형 그래프입니다.
       * 자원 할당 그래프와 동일하게 프로세스 수가 n일때 O(n²) 만큼 걸립니다.
  2. **다중** 인스턴스 인 경우
     * 뱅커스 알고리즘 (Banker's Algorithm)과 유사한 방법을 활용하여 찾습니다.
* **데드락 복구 방법**
  1. Process termination
     * 데드락에 연관된 모든 프로세스를 중단하는 방법입니다.
  2. Resource Preemption
     * 해결 될 때까지 데드락에 연관된 프로세스를 하나씩 중단 시키는 방법입니다.
     * 비용을 최소화 할 victim(종료될 프로세스)를 선정합니다.
       * 비용 계산 시 높은 비용의 프로세스들만 계속 종료 시킬 경우 기아 현상이 발생할 수 있으므로, 종료의 타겟이 된 횟수 같은 것 도 계산에 넣는 것을 고려해야 합니다.

#### **🔕&#x20;**<mark style="background-color:orange;">**데드락 무시 (Deadlock Ignorance)**</mark>

**데드락이 생기지 않는 다고 생각**하고 시스템이 책임을 지지 않는 것입니다.

* Deadlock은 매우 드물게 발생하므로 조치 하는 것 자체가 더 큰 Overhead 비용 일 수도 있습니다.
* 만약 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사용자에게 직접 느끼게 하여 해결하도록 합니다.
* **UNIX, Windows 등 대부분의 범용적인 OS들이 채택하고 있는 방법**입니다.



### 세마포어란? (Semaphore)

다양한 스레드 간에 공유되는 **정수**(음수가 아닌) **변수**입니다.

* 세마포어는 **신호 메커니즘에 따라 작동**됩니다.
  * **wait** (**P**): 같이 들어 갈 수 없는 임계 영역에 들어가고자 하는 프로세스는 P연산을 거처야지만 들어 갈 수 있습니다.
  * **signal** (**V**): P연산을 통과해 임계 영역에 연산을 끝낸 프로세스가 V연산을 수행하여 다음 프로세스가 들어 갈 수 있게 합니다.
* **장점**
  * 한번에 **하나의 프로세스**만 임계 섹션에 액세스 하지만 **여러 스레드**가 허용됩니다.
    * 이는 즉, 한 프로세스의 여러 스레드가 동시에 중요한 섹션에 들어 갈 수 있으므로
    * 병렬 처리를 가능하게 하여 성능의 향상을 노릴 수 있습니다.
  * 기계에 독립적이므로 다양한 환경이나 플랫폼에서 사용 가능 합니다.
* **단점**
  * 우선순위의 역전이 발생합니다.&#x20;
    * ([동작하다 보면 가끔 높은 우선순위가 낮은 우선순위에게 밀림 (링크 맨 아래 참조)](https://beatmejy.tistory.com/22))
  * 프로그래밍 오류가 발생하기 쉬우며 올바른 방식으로 구현해야 합니다.
  * 운영체제는 wait과 signal 작업에 대한 모든 호출에 대해 감시하고 기록해야 합니다.&#x20;
    * 데드락 등의 문제를 감지하고 해결하기 위해 어떻게 실행되는지 기록 (이는 곧 오버헤드 발생)



### 뮤텍스란? (Mutex)

잠금 매커니즘을 제공하는 데 사용되는 **특정 종류의 이진 세마포어**라고 볼 수도 있습니다.

* 프로세스가 특정 시간에 코드의 **특정 섹션을** 실행하고 작업 할 수 있도록 코드의 특정 부분에 **상호 배제를 제공**하는데 주로 사용이 됩니다.
* **장점**
  1. 한 번에 하나의 프로세스만 임계 영역에 들어 갈 수 있으므로 간단한 잠금 매커니즘입니다.
  2. 데이터는 일관되게 유지되며 무결성을 유지할 수 있습니다.
* **단점**
  1. 임계 구역에 들어간 스레드가 휴먼 상태이면, 기아 상태로 이어질 수 있습니다.
  2. 우선순위가 높은 프로세스만 선점되면, 이 역시도 기아 상태로 이어질 수 있습니다.
  3. 이전 스레드가 스스로 임계 영역을 벗어나야지만 다른 프로세스가 임계 영역에 들어갈 수 있어서, 다른 프로세스가 잠금을 해제할 수 없습니다.
  4. [바쁜 대기](https://ko.wikipedia.org/wiki/%EB%B0%94%EC%81%9C_%EB%8C%80%EA%B8%B0)가 발생하여 CPU 주기가 낭비 될 수 있습니다.



### 뮤텍스 VS 세마포어

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption><p><a href="https://beatmejy.tistory.com/22">내 블로그 중 일부</a></p></figcaption></figure>



### 모니터란? (Monitor)

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p><a href="https://core.ewha.ac.kr/publicview/C0101020140411143154161543?vmode=f">반효경 교수님 OS 강의 중</a></p></figcaption></figure>

프로세스의 동기화를 제공하고 데드락을 방지하기 위한 프로그래밍 기법 중 하나 입니다.

* **모니터 내에서는 한번에 하나의 프로세스만이 활동**이 가능합니다.
* 실행되지 못한 **다른 프로세스**들은 모니터 밖의 **Entry Queue에서 대기**하고 있습니다.
* **Condition variable** (조건 변수)
  * 프로세스 모니터 안에서 실행 중이던 프로세스(스레드)가 특정 조건이 충족되어야 해서 대기해야 하는 경우 조건 변수를 사용하여 특정 조건이 충족되기를 기다릴 수 있게 저장 합니다.
  * 예를 들어 **x를 조건변수** 라고 한다면,
    * **wait**():
      * x.wait()을 호출한 프로세스는 다른 프로세스가 x.signal()를 호출 할 때까지 중단되어 있습니다.
    * **signal**()
      * x.signal()를 호출하게 되면 condition variable의 x 중 정확하게 하나의 중단된 프로세스를 다시 시작합니다.
      * wait() 등을 통해 중단된 프로세스가 없으면 아무 일도 일어나지 않습니다.







