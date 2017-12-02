### 주의

* 다양한 설치법 중 한 가지를 소개합니다
* 테스트 환경 설정이므로 실 사용시에는 사용자 needs에 맞게 설정하기를 권장합니다.
* AWS EC2에서 Elastic IP 설정을 안하면 서버를 Stop/Start 할 때 마다 elasticsearch.yml과 kibana.yml에서 IP 주소를 변경해야 합니다.

### 목록

* [Java 1.8 Upgrade](#java)
* 설치
    * [Elasticsearch 설치](#install_elasticsearch)
    * [Logstash 설치](#install_logstash)
    * [Kibana 설치](#install_kibana)
* 환경설정
    * [Elasticsearch 환경설정](#configure_elasticsearch)
        * [Bootstarp Checks](#bootstrap)
        * [JVM Options](#jvm)
        * [Network](#elasticsearch_network)
    * Kibana 환경설정
        * [Network](#kibana_network)
* 애플리케이션 실행
    * [Elasticsearch 시작](#start_elasticsearch)
    * [Kibana 시작](#start_kibana)
---

<a name='java'></a>
### Java 1.8 Upgrade 

#### 설치된 Java 버전 확인

`$ java -version`

[[ images/install/java_version_before.png | height = 500px | width = 1024px]]

#### Java 1.8 다운로드

```
$ cd /usr/lib/jvm
$ sudo wget -c --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u141-b15/336fa29ff2bb4ef291e347e091f7f4a7/jdk-8u141-linux-x64.tar.gz"
$ sudo tar -xzvf jdk-8u141-linux-x64.tar.gz
```

[[ images/install/download_java.gif | height = 500px | width = 1024px]]

#### Java 1.8 설치

`$ sudo alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_141/bin/java 2`

[[ images/install/install_java.png | height = 500px | width = 1024px]]

#### Java 1.8을 default로 설정

`$ sudo alternatives --config java`

[[ images/install/set_default_java.gif | height = 500px | width = 1024px]]

#### Java 버전 다시 확인 

`$ java -version`

[[ images/install/java_version_after.png | height = 500px | width = 1024px]]

---

<a name='install_elasticsearch'></a>
### Elasticsearch 설치

#### 디렉토리 생성

```
$ cd ~
$ mkdir fc
$ cd fc
```
[[ images/install/directory.gif | height = 500px | width = 1024px]]

#### Elasticsearch 다운로드

`$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.4.tar.gz`

[[ images/install/download_elasticsearch.gif | height = 500px | width = 1024px]]

#### Elasticsearch 설치 파일 압축 해제

`$ tar -xzvf elasticsearch-5.6.4.tar.gz`

[[ images/install/uncompress_elasticsearch.gif | height = 500px | width = 1024px]]

#### Elasticsearch 설치 파일 제거

`$ rm elasticsearch-5.6.4.tar.gz`

---

<a name='install_logstash'></a>
### Logstash 설치

#### Logstash 다운로드

`$ wget https://artifacts.elastic.co/downloads/logstash/logstash-5.6.4.tar.gz`

[[ images/install/download_logstash.gif | height = 500px | width = 1024px]]

#### Logstash 설치 파일 압축 해제

`$ tar -xzvf logstash-5.6.4.tar.gz`

[[ images/install/uncompress_logstash.gif | height = 500px | width = 1024px]]

#### Logstash 설치 파일 제거

`$ rm logstash-5.6.4.tar.gz`

---

<a name='install_kibana'></a>
### Kibana 설치

#### Kibana 다운로드

`$ wget https://artifacts.elastic.co/downloads/kibana/kibana-5.6.4-linux-x86_64.tar.gz`

[[ images/install/download_kibana.gif | height = 500px | width = 1024px]]

#### Kibana 설치 파일 압축 해제

`$ tar -xzvf kibana-5.6.4-linux-x86_64.tar.gz`

[[ images/install/uncompress_kibana.gif | height = 500px | width = 1024px]]

#### Kibana 설치 파일 제거

`$ rm kibana-5.6.4.tar.gz`

---

<a name='configure_elasticsearch'></a>
### Elasticsearch 환경설정

<a name='bootstrap'></a>
#### Elasticsearch Bootstrap Check 통과하기

* max file descriptors 늘려주기
     * 설명
         * Mac OS 및 Linux만 해당 (Windows는 불필요)
         * Elasticsearch를 구동중인 사용자의 open files descriptors를 65536까지 올려야 함
     * 방법
         * 작업 전 확인 : `$ ulimit -Hn` (default로 4096이 출력될 것이다)
         * limits.conf 편집 : `$ sudo vim /etc/security/limits.conf`
         * 편집 모드로 변경 : `i` 누르기
         * 아래 코드를 limits.conf 가장 아래 입력
         ```
         *        hard    nofile           65536
         *        soft    nofile           65536
         ```
         * 저장 : `ESC 누른 후 :wq 입력 후 Enter 누르기`
         * 재접속
         ```
         $ sudo su
         $ su - ec2-user
         ```
         * 작업 후 재확인 : `$ ulimit -Hn`

         [[ images/install/max_file_descriptor.gif | height = 500px | width = 1024px]]

* virtual memory areas 늘리기
    * 설명
        * Elasticsearch는 `mmapfs` 디렉토리에 index를 저장한다 (default 설정)
        * `mmap counts`에 대한 운영체제의 limit이 default로는 낮게 되어 있어서 높혀주지 않으면 out of memory 발생
    * 방법
        * 임시 (재접속시 해제)
            * 작업 전 확인 : `$ sudo sysctl -a | grep vm.max_map_count` => 65530
            * 늘리기 : `sudo sysctl -w vm.max_map_count=262144`
            * 작업 후 확인 : `$ sudo sysctl -a | grep vm.max_map_count` => 262144
        * 영구적 (재접속 후에도 효과 지속)
            * `$ sudo vim /etc/sysctl.conf`
            * 편집 모드 변경 : `i` 입력
            * sysctl.conf 가장 아래에 `vm.max_map_count=262144` 입력
            * 저장 : `ESC 누르고 :wq 입력 후 Enter`
            * 재시작 : `$ sudo reboot`
            * AWS EC2 재접속
                * Mac
                    * 형식 : `$ ssh -i "{키 페어 이름}" {사용자 이름/ID}@{Public DNS}`
                    * 예시 : `$ ssh -i "fc-test.pem" ec2-user@ec2-12-345-67-891.ap-northeast-2.compute.amazonaws.com`
                * Windows
                    * Putty.exe 실행
                    * 저장한 Settings 불러오기
                        * Category - Session에서 저장한 Settings Load 선택
                        * 밑에 Open 선택
                    * 저장한 Settings가 없는 경우 : [수동으로 다시 접속](https://github.com/higee/elastic/wiki/AWS-EC2-Instance-%EC%83%9D%EC%84%B1-%EB%B0%8F-%EC%A0%91%EC%86%8D#connect-windows-putty)
            
            [[ images/install/virtual_memory.gif | height = 500px | width = 1024px]]

<a name='jvm'></a>
#### JVM Options

* 설명
    * 테스트 환경에서 Free Tier EC2 Instance를 선택했기에 JVM의 Heat Size를 줄여줘야 한다
    * 넉넉한 EC2 Instance를 선택했다면 이 작업은 스킵해도 된다
* 방법
    * elasticsearch config 디렉토리 이동 :  `$ cd /home/ec2-user/fc/elasticsearch-5.6.4/config`
    * jvm 설정 파일 편집 : `$ vim jvm.options`
    * 편집 모드 : `i` 입력
    * Xms와 Xmx를 모두 512m로 변경
    ```
    -Xms512m
    -Xmx512m
    ```
    * 저장 : `ESC 누르고 :wq 입력 후 Enter`

    [[ images/install/jvm.gif | height = 500px | width = 1024px]]


<a name='elasticsearch_network'></a>
#### Network 설정

* elasticsearch config 디렉토리 이동 :  `$ cd /home/ec2-user/fc/elasticsearch-5.6.4/config`
* elasticsearch.yml 파일 편집 : `$ vim elasticsearch.yml`
* 입력 모드 전환 : `i` 입력
* network.host에 EC2 Instance Public DNS 주소를 입력한다 
    * 형식 : `network.host: {Public DNS}`
    * 예시 : `network.host: ec2-12-345-67-891.ap-northeast-2.compute.amazonaws.com`
* 저장 : `ESC 입력 후 :wq 후 Enter`

[[ images/install/elasticsearch_yml.gif | height = 500px | width = 1024px]]

---

<a name='kibana_network'></a>
### Kibana 환경설정

* Kibana config 디렉토리 이동 : `$ cd /home/ec2-user/fc/kibana-5.6.4-linux-x86_64/config`
* kibana.yml 파일 편집 : `$ vim kibana.yml`
* 입력 모드 전환 : `i` 입력
* server.host와 elasticsearch.url 수정
    * 형식
        ```
        server.host: "{Public DNS}"
        elasticsearch.url: "http://{IPv4 Public IP address}:9200"
        ```
    * 예시
        ```
        server.host: "ec2-12-345-67-891.ap-northeast-2.compute.amazonaws.com"
        elasticsearch.url: "http://12.345.67.891:9200"
        ```
[[ images/install/kibana_yml.gif | height = 500px | width = 1024px]]

---

<a name='start_elasticsearch'></a>
### Elasticsearch 시작

* Elasticsearch Home Directory 이동 : `cd /home/ec2-user/fc/elasticsearch-5.6.4`
* 시작
    * 일반 시작 : `$ bin/elasticsearch` 
    * 데몬 시작 : `$ bin/elasticsearch -d`
    * 백그라운드 시작 : `$ nohup bin/elasticsearch &`
* 확인
    * 브라우저로 확인 : `http://12.345.67.891:9200` 접속
    * 콘솔로 확인 : `$ curl http://52.78.156.86:9200`

[[ images/install/start_elasticsearch.gif | height = 500px | width = 1024px]]

<a name='start_kibana'></a>
### Kibana 시작

* 주의할 점은 Elasticsearch를 작동 중인 상태에서 Kibana를 실행해야 한다는 것이다
* 이를 위해 Elasticsearch를 한 번 중단 시키고 background에서 재실행한다
     * 중단 : 터미널에서 `Control + c` 입력
     * background 실행 : `$ nohup bin/elasticsearch &`
* Kibana Home Directory 이동 : `$ cd /home/ec2-user/fc/kibana-5.6.4-linux-x86_64`
* Kibana 실행
    * 일반 시작 : `$ bin/kibana`
    * 백그라운드 시작 : `$ nohup bin/kibana &`
* 확인 : 브라우저로 `http://12.345.67.891:5601` 접속

[[ images/install/start_kibana.gif | height = 500px | width = 1024px]]

---

### Source
* [elastic reference - JVM Heap Size](https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html)
* [elastic reference - File Descriptor](https://www.elastic.co/guide/en/elasticsearch/reference/current/file-descriptors.html)
* [elastic reference - Virtual Memory](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)