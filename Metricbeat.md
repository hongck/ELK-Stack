### 개요

* Metricbeat는 서버 모니터링 지표를 수집한다
* 더욱이 Kibana에서 Dashboard 자동생성도 가능해 쉽고 편하게 사용할 수 있다

---

[[ images/metricbeat/main.gif | height = 500px | width = 1024px]]


#### Metricbeat Home Directory 이동
```
$ cd /home/ec2-user/fc/metricbeat-5.6.4-linux-x86_64
```

#### Metricbeat.yml 편집
```
$ vim metricbeat.yml
```

```
#================================ Outputs =====================================

# Configure what outputs to use when sending the data collected by the beat.
# Multiple outputs may be used.

#-------------------------- Elasticsearch output ------------------------------
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["http://13.125.21.52:9200"]

dashboards.enabled: true

#----------------------------- Logstash output --------------------------------
#output.logstash:
  # The Logstash hosts
  #hosts: ["localhost:5044"]

```

#### Metricbeat 실행
```
./metricbeat -e
```
