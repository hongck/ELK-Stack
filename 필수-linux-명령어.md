### 목록

* [관리자 권한으로 실행(`sudo`)](#sudo)
* [디렉토리(폴더) 이동 (`cd`)](#cd)
* [디렉토리(폴더) 생성 (`mkdir`)](#mkdir)
* [파일 복사 (`cp`)](#cp)
* [파일 이동 (`mv`)](#mv)
* [파일 압축해제 (`tar`)](#tar)
* [파일 삭제 (`rm`)](#rm)
* [파일 다운로드 (`wget`)](#wget)
* [프로그램(어플리케이션) 백그라운드 실행 (`nohup`)](#nohup)

---

<a name='sudo'></a>
#### 관리자 권한으로 실행 (sudo - super user do)
* 명령어 : `$ sudo {실행하려는 명령어}`
* 예시
    * 코드 : `$ sudo vi test.py`
    * 의미 : 관리자 권한으로 test.py 열어서 편집하기

<a name='cd'></a>
#### 디렉토리 이동 (cd - change directory)
* 명령어 : `$ cd {이동하려는 경로}`
* 예시
    * 코드 : `$ cd users`
    * 의미 :  (현재 디렉토리 하위에 있는) `users` 라는 디렉토리로 이동해라

<a name='mkdir'></a>
#### 디렉토리 생성 (mkdir - make a directory)
* 명령어 : `$ mkdir {생성하려는 디렉토리 경로}
* 예시
    * 코드 : `$ mkdir users`
    * 의미 :  (현재 디렉토리에) `users` 라는 디렉토리를 생성해라 

<a name='cp'></a>
#### 파일 복사 (cp - copy)
* 명령어 : `$ cp {복사하려는 파일 경로/이름} {복사 파일이 생기는 경로/이름}` 
* 예시
    * 코드 : `$ cp hello.py bye.py` 
    * 의미 : 현재 디렉토리에 있는 hello.py를 현재 디렉토리에 bye.py 라는 이름으로 복사해라

<a name='mv'></a>
#### 파일 잘라내고 붙여넣기 (mv - move)
* 명령어 : `$ mv {원본 파일 경로/이름} {원본 파일을 이동할 경로/이름}`
* 예시
    * 코드 : `$ mv ../hello.py ./bye.py`
    * 의미 : 현재 디렉토리 상위에 있는 hello.py를 잘라내서 현재 디렉토리 하위 디렉토리에 bye.py라는 이름으로 붙여넣어라

<a name='tar'></a>
#### 파일 압축해제 (tar -x)
* 명령어 : `$ tar {옵션} {압축해제 할 파일 경로/이름}`
* 예시
    * 코드 : `$ tar -xzvf elasticsearch-5.6.4.tar.gz`
    * 의미 : elasticsearch-5.6.4.tar.gz 파일을 압축해제해라

<a name='rm'></a>
#### 파일 삭제 (rm)
* 명령어 : `$ rm {삭제할 파일 경로/이름}`
* 예시
    * 코드 : `$ rm elasticsearch-5.6.4.tar.gz`
    * 의미 : elasticsearch-5.6.4.tar.gz 파일을 삭제해라

<a name='wget'></a>
#### 파일 다운로드 (wget)
* 명령어 : `$ wget {다운받을 데이터가 업로드 되어 있는 url}`
* 예시
    * 코드 : `$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.4.tar.gz`
    * 의미 : https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.4.tar.gz를 다운 받아라

<a name='nohup'></a>
#### (터미널 접속이 끊어져도) 프로그램(어플리케이션) 백그라운드에서 실행 (nohup)
* 명령어 : `$ nohup {실행하려는 프로그램 실행 명령어} &`
* 예시 
    * 코드 : `$ nohup bin/elasticsearch &`
    * 의미 : elasticsearch를 백그라운드에서 실행해라