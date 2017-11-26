### 주의

* `테스트 환경` 설정이므로 실 사용시는 사용자 needs에 맞게 customize 하기를 권장합니다
* 설치에 관해서는 다양한 방법이 존재하며 그 중 한 방법을 소개합니다.

### 목록

* [AWS EC2 Instance 생성](#create)
    
* [AWS EC2 Instance 접속](#connect)
    * [Mac OS](#connect-mac)
    * [Windows](#connect-windows)

---

<a name='create'></a>
#### AWS EC2 Instance 생성

* [AWS 접속](https://aws.amazon.com/ko)

[[ images/aws/create/main.png | height = 500px | width = 1024px]]

* [`로그인` : 회원이면 로그인, 처음이면 회원가입 후 로그인](https://signin.aws.amazon.com/signin?client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fhomepage&redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fconsole%2Fhome%3Fstate%3DhashArgs%2523%26isauthcode%3Dtrue&page=resolve)

[[ images/aws/create/login.png | height = 500px | width = 1024px]]

* `Region` : 아시아태평양(서울) 선택

[[ images/aws/create/region.png | height = 500px | width = 1024px]]

* `EC2` 선택

[[ images/aws/create/ec2.png | height = 500px | width = 1024px]]

* 인스턴스 생성
    * `인스턴스 시작` 선택
        * AMI 중에서 원하는 Image 선택
        * READMD.md에서 언급했듯이 본 project에서는 `Amazon Linux AMI 2017.09.1 - ami-1196317f` 사용

        [[ images/aws/create/instance_step1.gif | height = 500px | width = 1024px]]

    * `인스턴스 유형` 선택 : 프리티어 사용 예정이므로 `t2.micro` 선택

    [[ images/aws/create/instance_step2.png | height = 500px | width = 1024px]]

    * `인스턴스 세부정보` 

    [[ images/aws/create/instance_step3.png | height = 500px | width = 1024px]]

    * `스토리지 추가` 

    [[ images/aws/create/instance_step4.png | height = 500px | width = 1024px]]

    * `태그 추가` 

    [[ images/aws/create/instance_step5.png | height = 500px | width = 1024px]]

    * `보안 그룹 구성`

    [[ images/aws/create/instance_step6.gif | height = 500px | width = 1024px]]

    | 포트 범위 | 설명 |
    |----- |-------------|
    | 22 | SSH로 EC2에 접속할 것이므로 필요 |
    | 9200 | Elasticsearch에 REST reqeust를 전송할 시 필요 |
    | 9300 | Cluster를 형성한 Elasticsearch node간 communication시 필요  |
    | 5601 | Kibana가 5601 포트를 사용하므로 필요 |
    
    * `인스턴스 시작 검토` : EC2 접속을 위해 매우 중요하니 보관에 유의
        * 새 키페어 선택
        * 본인 만의 키 페어 이름 임의로 입력
        * 키 페어 다운로드

    [[ images/aws/create/instance_step7.gif | height = 500px | width = 1024px]]

    * `인스턴스 생성 완료`

    [[ images/aws/create/instance_step8.gif | height = 500px | width = 1024px]]

<a name='connect'></a>
#### AWS EC2 Instance 접속

<a name='connect-mac'></a>
* Mac OS : `gee`는 사용자 이름으로 본인 컴퓨터 사용자 이름으로 변경

    [[ images/aws/connect/mac/main.gif | height = 500px | width = 1024px]]

    * 터미널 애플리케이션 실행 
    * aws_key directory 생성 : `$ mkdir /Users/gee/aws_key` 
    * aws_key로 이동 : `$ cd /Users/gee/aws_key`
    * 다운 받은 키 페어 파일 (예: fc-test.pem) 이동 : `$ mv /Users/gee/Downloads/fc-test.pem ./`
    * 키 페어 파일 (fc-test.pem) 접근 권한 변경 : `$ chmod 400 fc-test.pem`
    * ssh로 AWS EC2 접속
        * 형식 : `$ ssh -i "{키 페어 이름}" {사용자 이름/ID}@{Public DNS}`
        * 예시 : `$ ssh -i "fc-test.pem" ec2-user@ec2-13-345-67-234.ap-northeast-2.compute.amazonaws.com

<a name='connect-windows'></a>
* Windows
    * [PuTTY 다운로드](http://www.putty.org/)
        * [페이지 이동](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
        * [시스템 종류에 맞는 putty 다운로드]
        [[ images/aws/connect/windows/putty_download.png | height = 500px | width = 1024px]]
        * [시스템 종류에 맞는 puttygen 다운로드]
        [[ images/aws/connect/windows/puttygen_download.png | height = 500px | width = 1024px]]

    * ppk 파일 생성
        * puttygen.exe 실행
        * load 선택
        * 키 페어 선택 (fc-test.pem)
        * parameters - type of key to generate => SSH 선택
        * Save private key 선택

        [[ images/aws/connect/windows/puttygen.gif | height = 500px | width = 1024px]]

    * EC2 접속
        * putty.exe 실행
        * Category - Session : `Host Name` 입력
            * 형태 : Public DNS
            * 예시 : `ec2-13-345-67-234.ap-northeast-2.compute.amazonaws.com` (예시)
        * Category - Connection - Data : `Auto-login username` 입력 
            * 형태 : EC2 인스턴스 사용자 id
            * 예시 : `ec2-user`
        * Category - Connection - SSH - Auth : `Private key file for authentication` 선택
        * Category - Session - Saved Session : 저장할 임의의 Session 이름 입력 후 저장
        * Open 선택

        [[ images/aws/connect/windows/putty.gif | height = 500px | width = 1024px]]