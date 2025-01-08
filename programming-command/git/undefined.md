# 인텔리제이

#### 인텔리제이 PUSH 버튼으로 Push 하는것 주의하기.

* branch에서 작업 후 Main으로 Push했을 때 Merge가 되는 상황이 발생하였다.
*

    <figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>문제의 branch -> main Push 상황</p></figcaption></figure>
* 때문에 현재 진행 중이던 Branch가 자동으로 Main에 병합이 되어 해당 진행 중이던 Branch가 자동으로 머지 되어 종료되었다.
* 이러한 문제는 새로운 Branch를 작성해서 다시 PR을 올려야 하지만 기존 진행하던 Branch가 Approve 없이 커밋 된다는 것 자체가 문제가 되기에 이를 방지하기 위해 아래와 같은 2가지 정책을 추가하였다.
  1.  첫번째로는 Merge를 할때에는 2명 이상의 승인이 필요하도록 설정을 하여 강제로 merge가 되는 것을 막아주었다. (2인 프로젝트 이기 때문에)

      <figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>
  2.  두번째로는&#x20;

      <figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





