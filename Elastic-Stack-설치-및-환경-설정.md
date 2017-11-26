### 주의

* 다양한 설치법 중 한 가지를 소개합니다
* 테스트 환경 설정이므로 실 사용시에는 사용자 needs에 맞게 설정하기를 권장합니다.

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
    * [Kibana 환경설정](#configure_elasticsearch)

---

<a name='java'></a>
### Java 1.8 Upgrade 

#### 설치된 Java 버전 확인

`$ java -version`

[[ images/install/java_versions_before.pn | height = 500px | width = 1024px]]

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
         * 작업 후 재확인 : `$ ulimit -Hn # have it checked`

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
            * 재시작 : `$ reboot`
            * AWS EC2 재접속
                * 형식 : `$ ssh -i "{키 페어 이름}" {사용자 이름/ID}@{Public DNS}`
                * 예시 : `$ ssh -i "fc-test.pem" ec2-user@ec2-13-345-67-234.ap-northeast-2.compute.amazonaws.com`
            
            [[ images/install/virtual_memory.gif | height = 500px | width = 1024px]]

<a name='jvm'></a>
#### JVM Options

* 테스트 환경에서 Free Tier EC2 Instance를 선택했기에  
---

<a name='configure_kibana'></a>
### Kibana 환경설정