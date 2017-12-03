### 예시

[[ images/logstash/logstash-example.gif | height = 500px | width = 1024px]]

### 목록

* 사전 준비 
    * [Elasticsaerch JVM 조정](#elasticsearch-jvm)
    * [Logstash JVM 조정](#logstash-jvm)
* Logstash
    * [Logstash Home Directory 이동](#logstash-home)
    * [Logstash 실행 파일 생성](#logstash-conf)
---

### 사전준비

* 테스트 환경으로 `AWS EC2 t2.micro instance`를 사용하기에 필요한 옵션이다.
* 테스트 환경이라도 이보다 더 넉넉한 Memory를 사용하고 있다면 [다음 단계](https://github.com/higee/elastic/wiki/Logstash-%EC%8B%A4%ED%96%89-%ED%8C%8C%EC%9D%BC-%EC%83%9D%EC%84%B1#logstash-home)로 넘어가도 된다.

<a name='elasticsearch-jvm'></a>
#### elasticsearch JVM options 조정

* `$ vim ~/fc/elasticsearch-5.6.4/config/jvm.options`
* 편집 모드 : `i` 입력
* 아래 부분 수정
    * 기존
    ```
    # Xms represents the initial size of total heap space
    # Xmx represents the maximum size of total heap space

    -Xms512m
    -Xmx512m
    ```
    * 수정
    ```
    # Xms represents the initial size of total heap space
    # Xmx represents the maximum size of total heap space

    -Xms128m
    -Xmx128m
    ```
* 저장 : `ESC` 누르고 `:wq` 입력

<a name='logstash-jvm'></a>
#### logstash JVM options 조정

* `$ vim ~/fc/logstash-5.6.4/config/jvm.options`
* 편집 모드 : `i` 입력
* 아래 부분 수정
    * 기존
    ```
    # Xms represents the initial size of total heap space
    # Xmx represents the maximum size of total heap space

    -Xms512m
    -Xmx512m
    ```
    * 수정
    ```
    # Xms represents the initial size of total heap space
    # Xmx represents the maximum size of total heap space

    -Xms128m
    -Xmx128m
    ```
* 저장 : `ESC` 누르고 `:wq` 입력

---

### Logstash
<a name='logstash-home'></a>
#### Logstash Home Directory 이동

* Logstash 실행 파일을 실행할 때 Logstash Home Directory 밑의 `bin/logstash`을 사용한다
* 명령어 : `$ cd ~/fc/logstash-5.6.4/`

<a name='logstash-conf'></a>
#### Logstash 실행파일 생성

* 데이터 수집 및 전처리를 위한 모든 작업은 Logstash 실행 파일 내부에서 코드를 작성한다
* 명령어 : `$ touch logstash.conf`