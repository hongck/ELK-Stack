[[ images/region_map.gif | height = 500px | width = 1024px]]

### 순서

* [Geo Json 데이터 준비](#geo)
* [CORS (cross origin resource sharing) 활성화된 서버에 Geo Json 띄우기](#cors)
* [Kibana yml 설정](#kibana)
* [Elasticsearch 색인](#es)
* [Region Map 시각화](#vis)

---
#### Geo Json 데이터 준비 <a name="geo"></a>

* 기본적으로 `세계 국가`와 `미국 주` Geo Json은 제공해준다
* 다만 그 외의 Geo Json을 사용하려면 사용자가 추가 작업을 해야한다
* 이번에는 한국의 시/도 Geo Json 정보를 준비하고 시각화해보는 과정을 살펴본다.
* 인터넷에서 쉽게 찾을 수 있으며 이번에는 [통계청 자료](https://github.com/southkorea/southkorea-maps/blob/master/kostat/2013/json/skorea_provinces_geo_simple.json)를 사용했다. 
* 대략 이렇게 생겼다. Geo Json 파일 내 한글 행정권역명을 사용할 경우 encoding에 주의해야 한다

[[ images/region_map_geo_json.png | height = 300px | width = 1024px]]

#### CORS 활성화된 서버에 Geo Json 띄우기<a name="cors"></a>

* 여러 옵션 중에 이번에는 AWS S3을 통한 정적 웹호스팅 방법을 이용한다.
* AWS CLI 를 이용하면 아래와 같이 할 수 있다 (AWS Console에서도 가능하다)
* 사전 준비
    * AWS CLI 설치 : `$ pip install aws`
    * AWS Configure : `$ aws configure`
        * Access Key ID, Secret Access Key, Region Name 정도를 입력해준다
        * 다만, 여기서 사용하려는 user는 AWS 상에서 IAM (Identity and Access Management)에서 S3 권한을 부여 받아야 한다. 

* AWS S3 버킷 생성
    * 명령어 : `$ aws s3 mb s3://{버킷 이름}` # 버킷 이름은 임의로 작성
    * 예시 : `$ aws s3 mb s3://higeemap`

* 정적 웹호스팅 유효화
    * 명령어 : `$ aws s3 website s3://{버킷 이름} --index-document index.html` # 버킷 이름은 위에서 사용한 거 사용
    * 예시 : `$ aws s3 website s3://higeemap --index-document index.html`

* CORS 활성화
    * 명령어
    ```
         $ aws s3api put-bucket-cors --bucket {버킷이름} --cors-configuration '{
           "CORSRules": [{
             "AllowedHeaders": ["*"],
             "AllowedMethods": ["GET"],
             "AllowedOrigins": ["http://{IP주소}:5601"]
           }]
         }'
    ```
    * 예시
    ```
         $ aws s3api put-bucket-cors --bucket higeemap --cors-configuration '{
           "CORSRules": [{
             "AllowedHeaders": ["*"],
             "AllowedMethods": ["GET"],
             "AllowedOrigins": ["http://localhost:5601"]
           }]
         }'
    ```

* Geo Json 파일 S3 버킷에 전송하기
    * 명령어 : `$ aws s3 cp {경로}/{파일명} s3://{버킷명}/`
    * 예시 : `$ aws s3 cp /Users/korea.geojson s3://higeemap/`

* (S3에 올린) 파일 접근 권한 부여하기
    * 명령어 : `$ aws s3api put-object-acl --bucket {버킷이름} --key {파일이름} --acl public-read`
    * 예시 : `$ aws s3api put-object-acl --bucket higeemap --key korea.geojson --acl public-read`

#### Kibana yml 설정 <a name="kibana"></a>

* (위에서 했던) AWS S3에 올린 Geo Json을 Kibana Region Map이 바라보게 해야한다.
* 그러려면 Kibana 설정을 변경해야 한다.
    * Kibana가 설치된 곳으로 이동한다 
        * (예: /Users/elastic/kibana)
        * `$ cd /Users/elastic/kibana/config`
    * Kibana.yml 수정
        * 명령어 : `$ vi kibana.yml` # 입력 후 아래 내용을 추가한다

            ```
            regionmap:
              layers:
              - name: "Korea City"
                url: "{AWS S3 버켓에 올린 파일 경로}"
                fields:
                  - name: "name"
                    description: "City name"
            ```
        * 항목 설명
            * (layer 아래) name : Kibana Region Map에서 Vector Map 선택지에 표시되는 이름
            * url : Geo Json이 올라간 곳의 url로 아래 이미지의 링크 부분을 입력하면 된다

[[ images/region_map_kibana_yml.png | height = 500px | width = 1024px]]
            * (field 아래) name : Geo Json 파일에서 `서울특별시`, `제주특별자치도` 등의 값을 담고 있는 field 이름으로 위의 이미지에서 보면 name으로 되어 있기에 여기도 name으로 써줘야 한다
            * (field 아래) description : Kibana Region Map에서 Join Field에 표시되는 이름

#### Elasticsearch 색인<a name="es"></a>

* 설정은 끝났고 이제는 색인을 하면 된다.
* 주의할 점은 색인 시 Geo Json 파일의 name 값들 `서울특별시`, `제주특별자치도` 등의 값을 가진 Field가 존재해야만 Visualize에서 사용 가능하다.
* 예를 들면 아래와 같은 Documents가 색인되면 정상적으로 Region Map이 사용 가능하다.
    * 아래와 같은 조건으로 색인한다고 하자
        * Index 이름 : test_index
        * Type 이름 : test_type
        * Elasticsearch url : http://localhost:9200
    * 예시
        * Kibana Console (Dev Tools에서 직접 넣을 시)
          ```
          PUT test_index/test_type/1
          {
              "날짜" : "2017-11-05T14:12:15",
              "고객주소_시도" : "서울특별시"
          }
          ```
        * curl 이용시 
          ```
          $ curl -XPUT 'localhost:9200/test_index/test_type/1?pretty' -H 'Content-Type: application/json' -d'
          {
              "날짜" : "2017-11-06T11:03:12",
              "고객주소_시도" : "경상남도"
          }
          '
          ```

#### Region Map 시각화 <a name="vis></a>

* Visualize에서 Region Map 선택

[[ images/region_map_home.png | height = 500px | width = 1024px]]

* Options 설정
    * Layer Settings에서 Vector map과 Join field가 맞게 설정되었는지 확인한다

    [[ images/region_map_options.png | height = 500px | width = 1024px]]

* Data 설정
    * buckets에서 Shape Field를 고른다
    * 이 때 Shape Field란 위의 예에서 보면 "서울특별시", "경상남도" 등의 값을 담고 있는 `고객주소_시도`가 된다.
    * 위의 Metric/Sibling Pipeline Aggregation은 기호에 맞게 설정한다

    [[ images/region_map_data.png | height = 500px | width = 1024px]]