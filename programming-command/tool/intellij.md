# intellij

### **Intellij에서 CamelCase 단어 별로 선택하기**

Preferences -> Editor -> General -> Smart Keys 에서 User "CamelHumps" words 옵션을 체크



### 터미널 한글 깨짐 현상 해결

1. Settings -> Editor -> File Encodings ->  전부 UTF-8로 변경
2.  Help -> Edit Custom VM Options ... 에 아래 코드 추가.

    ```
    -Dfile.encoding=UTF-8
    -Dconsole.encoding=UTF-8
    ```
3.  C:\Program Files\JetBrains\IntelliJ IDEA 2024.1\bin 에 아래 코드 추가.

    ```
    -Dfile.encoding=UTF-8
    -Dconsole.encoding=UTF-8
    ```


4.

    <figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>


5.

    <figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>
