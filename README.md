#[ElasticSearch](http://www.elasticsearch.com/)

####Elasticsearch is a search server based on Lucene. It provides a distributed, multitenant-capable full-text search engine with a RESTful web interface and schema-free JSON documents. Elasticsearch is developed in Java and is released as open source under the terms of the Apache License.

## Overview
- 설치/실행이 간단하며, 클러스터링 구성이 용이
- [Search Engine created by Shay Banon](http://www.elasticsearch.com/about/team/)
- Lucene based Search Engine
	- Apache Lucene^TM^ is a high-performance, full-featured text search engine library written entirely in Java. It is a technology suitable for nearly any application that requires full-text search, especially cross-platform.
	- created by Doug Cutting
	- Lucene은 사용가능한 완벽한 시스템이 아니라 색인, 검색등을 가능하게 하는 library 산출물임
	- [Apache Solr(4.10.2)](http://lucene.apache.org/solr/), ElasticSearch(1.4.1)는 Lucene Library를 사용하여 제작한 시스템으로 사용자들이 직접 개발 하지 않고 검색(RESTful API)할 수 있는 방법을 제공함
	- [Apache Solr vs ElasticSearch](http://solr-vs-elasticsearch.com/)
		- Solr는 다양한 입력 포맷 지원(CSV, XML, JSON), 해외에서 높은 인지도, apache TLP
		- ES는 only-JSON, 운영편의성은 ES가 용이, 성능은 비슷함, elasticsearch 회사 지원
- [Various Programming API](http://www.elasticsearch.org/guide/)(Java, Python, Javascript, PHP, Ruby, Perl Etc)
- RESTful API
	- GET/PUT/POST/DELETE/HEAD
- DBMS와는 달리 비정형화된 문서에서 데이터 검색
- Multi-tenancy - 여러개의 Index에 한번의 질의 가능
- Marvel plugin Install - 검색 [UI](http://172.16.100.102:9200/_plugin/marvel/sense/index.html) 제공
- Apache 2 license
- HTTP(port 9200), Transport(port 9300)

##ElasticSearch VS DBMS
| 관계형 데이터베이스        | elasticsearch|
| ------------- |:-------------:|
| Database    | Index|
| Table      |Type      |
| Row | Document    |
| Column | Field    |
| Schema | Mapping    |
| Index | Everything is indexed    |
| SQL| Query DSL  |
[출처 - NAVER개발자블로그](http://helloworld.naver.com/helloworld/textyle/273788)
### ElasticSearch 기본 화면 - [http://172.16.100.102:9200](http://172.16.100.102:9200)

`curl -XGET 'http://172.16.100.102:9200'`

```
{
  "status" : 200,
  "name" : "Black Talon",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "1.4.1",
    "build_hash" : "89d3241d670db65f994242c8e8383b169779e2d4",
    "build_timestamp" : "2014-11-26T15:49:29Z",
    "build_snapshot" : false,
    "lucene_version" : "4.10.2"
  },
  "tagline" : "You Know, for Search"
}
```

##여기서 다루지 않는 것들
- analysis
- score(boostring query)
- filter
- 설치와 운영 및 성능 관련
- join via has_children and top_children queries
- regexp query, bool query 등등
- 메모리 관련 이슈

##Indices APIs(문서의 생성과 삭제)

- REST API형식
	- http://host:port/(index)/(type)/(action|id)

- 인덱스 생성

    ```
    PUT /test
    ```
- 인덱스 확인

	```
    GET /test
	```
    ```
    {
       "test": {
          "mappings": {},
          "settings": {
             "index": {
                "creation_date": "1418029762910",
                "uuid": "0A7XrxtpS_-_h825-C6jlA",
                "number_of_replicas": "1",
                "number_of_shards": "5",
                "version": {
                   "created": "1040199"
                }
             }
          }
       }
    }
	```

- 인덱스 생성2
	```
    PUT /test/
    {
        "settings" : {
            "index" : {
                "number_of_shards" : 3,
                "number_of_replicas" : 2
            }
        }
    }
    ```

- 인덱스 확인2

	```
    GET /test
	```
    ```
    {
       "test": {
          "mappings": {},
          "settings": {
             "index": {
                "creation_date": "1418029976244",
                "uuid": "JR1JH0xaTM69IuPLCDULIw",
                "number_of_replicas": "2",
                "number_of_shards": "3",
                "version": {
                   "created": "1040199"
                }
             }
          }
       }
    }
	```


- 인덱스 삭제

	```
    DELETE /test
	```

- 인덱스 open/close

	```
    POST /test/_open
    POST /test/_close
	```


- 데이타 입력
	- schema free

	```
    PUT /test/type1/1
    {
      "@timestamp" : "2014-12-08T00:13:09+00:00",
      "eps_time" : "1417965189.529396",
      "report_time" : "20141208001309",
      "src_ip" : "192.168.1.55"
    }
    ```
    ```
    GET /test/type1/_search
    ```
    ```
    {
       "took": 1,
       "timed_out": false,
       "_shards": {
          "total": 5,
          "successful": 5,
          "failed": 0
       },
       "hits": {
          "total": 1,
          "max_score": 1,
          "hits": [
             {
                "_index": "test",
                "_type": "type1",
                "_id": "1",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             }
          ]
       }
    }
    ```
    ```
    PUT /test/type1/2
    {
      "@timestamp" : "2014-12-08T00:13:09+00:00",
      "eps_time" : "1417965189.529396",
      "report_time" : "20141208001309",
      "src_ip" : "192.168.1.55"
    }
    ```
	```
    {
       "took": 2,
       "timed_out": false,
       "_shards": {
          "total": 5,
          "successful": 5,
          "failed": 0
       },
       "hits": {
          "total": 2,
          "max_score": 1,
          "hits": [
             {
                "_index": "test",
                "_type": "type1",
                "_id": "1",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             },
             {
                "_index": "test",
                "_type": "type1",
                "_id": "2",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             }
          ]
       }
    }
    ```
    ```
    POST /test/type1/
    {
      "@timestamp" : "2014-12-08T00:13:09+00:00",
      "eps_time" : "1417965189.529396",
      "report_time" : "20141208001309",
      "src_ip" : "192.168.1.55"
    }
    ```
    ```
    {
       "took": 1,
       "timed_out": false,
       "_shards": {
          "total": 5,
          "successful": 5,
          "failed": 0
       },
       "hits": {
          "total": 3,
          "max_score": 1,
          "hits": [
             {
                "_index": "test",
                "_type": "type1",
                "_id": "1",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             },
             {
                "_index": "test",
                "_type": "type1",
                "_id": "2",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             },
             {
                "_index": "test",
                "_type": "type1",
                "_id": "AUopZk9jpjgjdLKhDz-s",
                "_score": 1,
                "_source": {
                   "@timestamp": "2014-12-08T00:13:09+00:00",
                   "eps_time": "1417965189.529396",
                   "report_time": "20141208001309",
                   "src_ip": "192.168.1.55"
                }
             }
          ]
       }
    }
    ```

    - Mapping

    ```
    GET /connectome-20141209/conn/_mapping
	GET connectome-20141209/_mapping/conn
    ```

	```
    {
    "connectome-20141209": {
        "mappings": {
             "conn": {
                    "properties": {
                       "@timestamp": {
                          "type": "date",
                          "format": "dateOptionalTime"
                       },
                       "app_prtc": {
                          "type": "string"
                       },
                       "conn_local": {
                          "type": "string"
                       },
                       "conn_state": {
                          "type": "string"
                       },
                       "device_ip": {
                          "type": "string"
                       },
                       "dst_asn": {
                          "type": "long"
                       },
                       "dst_asname": {
                          "type": "string"
                       },
                       "dst_bytes": {
                          "type": "long"
                       },
                       "dst_cc": {
                          "type": "string"
                       },
                       "dst_ip": {
                          "type": "string"
                       },
                       "dst_ip_bytes": {
                          "type": "long"
                       },
                       "dst_lati": {
                          "type": "double"
                       },
                       "dst_long": {
                          "type": "double"
                       },
                       "dst_pkts": {
                          "type": "long"
                       },
                       "dst_port": {
                          "type": "string"
                       },
                       "duration": {
                          "type": "double"
                       },
                       "elastic_time": {
                          "type": "string"
                       },
                       "eps_time": {
                          "type": "string"
                       },
                       "history": {
                          "type": "string"
                       },
                       "log_type_Name": {
                          "type": "string"
                       },
                       "missed_bytes": {
                          "type": "long"
                       },
                       "pcr": {
                          "type": "double"
                       },
                       "prtc": {
                          "type": "string"
                       },
                       "report_time": {
                          "type": "string"
                       },
                       "resp_cname": {
                          "type": "string"
                       },
                       "src_asn": {
                          "type": "long"
                       },
                       "src_asname": {
                          "type": "string"
                       },
                       "src_bytes": {
                          "type": "long"
                       },
                       "src_cc": {
                          "type": "string"
                       },
                       "src_country_name": {
                          "type": "string"
                       },
                       "src_ip": {
                          "type": "string"
                       },
                       "src_ip_bytes": {
                          "type": "long"
                       },
                       "src_lati": {
                          "type": "double"
                       },
                       "src_long": {
                          "type": "double"
                       },
                       "src_pkts": {
                          "type": "long"
                       },
                       "src_port": {
                          "type": "string"
                       },
                       "tunnel_parents": {
                          "type": "string"
                       },
                       "uid": {
                          "type": "string"
                       }
                    }
                 }
              }
           }
        }
        ```




- 데이터 삭제
    ```
    DELETE /test/type1/1
    ```
    ```
    DELETE /test/type1
    ```
- 데이터 수정

- 데이터 입력(bulk)

	`curl -s -XPOST localhost:9200/_bulk --data-binary @requests; echo`

    ```
    action_and_meta_data\n
    optional_source\n
    action_and_meta_data\n
    optional_source\n
    ....
    action_and_meta_data\n
    optional_source\n
    ```
    `requests`
    ```
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:11:42+00:00","eps_time" : "1417965102.639954","report_time" : "20141208001142","src_ip" : "192.168.1.55"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:41+00:00","eps_time" : "1417965161.314342","report_time" : "20141208001241","src_ip" : "192.168.1.100"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:41+00:00","eps_time" : "1417965161.327586","report_time" : "20141208001241","src_ip" : "192.168.1.100"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:41+00:00","eps_time" : "1417965161.314338","report_time" : "20141208001241","src_ip" : "192.168.1.100"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:53+00:00","eps_time" : "1417965173.701496","report_time" : "20141208001253","src_ip" : "192.168.1.55"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:56+00:00","eps_time" : "1417965176.154323","report_time" : "20141208001256","src_ip" : "192.168.1.55"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:57+00:00","eps_time" : "1417965177.263694","report_time" : "20141208001257","src_ip" : "192.168.1.55"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:59+00:00","eps_time" : "1417965179.339803","report_time" : "20141208001259","src_ip" : "192.168.1.31"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:12:59+00:00","eps_time" : "1417965179.341043","report_time" : "20141208001259","src_ip" : "192.168.1.31"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:13:09+00:00","eps_time" : "1417965189.529396","report_time" : "20141208001309","src_ip" : "192.168.1.55"}
    { "index" : { "_index" : "test1", "_type" : "type1"} }
    {"@timestamp" : "2014-12-08T00:13:06+00:00","eps_time" : "1417965186.826456","report_time" : "20141208001306","src_ip" : "192.168.1.55"}
    ```

##Search APIs

### Search
- 인덱스는 반드시 작성하거나 모두 생략
- *METHOD /indices/types/command*
- *METHOD /indices/types/id*
- *METHOD /indices/command*
- *METHOD /command*

```
GET /indices/type/id
GET /_all/_search?size=1000
GET /_search?size=1000
```

- Conectome에서는 'connectome-YYYYMMDD' 날짜별 Index생성, 파일로그명으로 type구성 각 로그의 uid를 elasticsearch document의 id로 사용함, id를 사용하려면 indices, type 모두 지정되어 있어야함
- size를 명시하지 않으면 기본적으로 10이 적용됨

```
GET /connectome-20141206/conn/CTjrCi3Omh3YC9g1ve
GET /connectome-20141206/http/_search?q=*:*
GET /connectome-20141206/http/_search
GET /connectome-20141206/http,conn,dns/_search
GET /connectome-20141206,connectome-20141207/conn,http,dns/_search
GET /_all/_search?size=1000
GET /_search?size=1000
```

### URI Search
- 기존의 lucene query를 그대로 사용할 수 있는 장점이 있음

```
GET /connectome-20141202/conn/_search?q=src_ip:192.168.1.151&from=0&size=20&sort=report_time:desc,src_ip:asc
```
`Result`
```
{
   "took": 234,
   "timed_out": false,
   "_shards": {
      "total": 1,
      "successful": 1,
      "failed": 0
   },
   "hits": {
      "total": 10951,
      "max_score": null,
      "hits": [
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "CR2dMm4xIGtLaioea3",
            "_score": null,
            "_source": {
               "report_time": "20141202165719",
               "src_bytes": "38",
               "uid": "CR2dMm4xIGtLaioea3",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-02T16:57:19+00:00",
               "resp_cname": "unknown-",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": "150",
               "dst_asn": "0",
               "dst_asname": "unknown-0",
               "duration": "0.003108",
               "src_lati": "0.0",
               "src_port": "62457",
               "src_country_name": "unknown-",
               "conn_state": "SF",
               "pcr": "-0.525",
               "elastic_time": "20141202165737",
               "src_asn": "0",
               "src_ip": "192.168.1.151",
               "src_cc": "(empty)",
               "src_pkts": "1",
               "dst_long": "0.0",
               "dst_cc": "(empty)",
               "device_ip": "172.16.2.3",
               "prtc": "udp",
               "dst_lati": "0.0",
               "missed_bytes": "0",
               "app_prtc": "dns",
               "conn_local": "T",
               "dst_pkts": "1",
               "log_type_Name": "conn.log",
               "src_long": "0.0",
               "eps_time": "1417507039.717880",
               "dst_port": "53",
               "dst_ip": "172.16.3.6",
               "src_ip_bytes": "66",
               "dst_bytes": "122",
               "history": "Dd"
            },
            "sort": [
               "20141202165719",
               "192.168.1.151"
            ]
         },
```
- 필드(fields)를 지정할 수 있음
- 필드를 지정할 경우 결과가 배열로 출력된다는 점에 유의
- 필드가 존재하지 않는 document일 경우 출력되지 않음

```
GET /connectome-20141202/conn/_search?q=src_ip:192.168.1.151&from=0&size=20&sort=report_time:desc,src_ip:asc&fields=report_time,src_ip,dst_ip,pcr
```
`Result`
```
{
   "took": 5,
   "timed_out": false,
   "_shards": {
      "total": 1,
      "successful": 1,
      "failed": 0
   },
   "hits": {
      "total": 10974,
      "max_score": null,
      "hits": [
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "CaBzO21djoItCJNKOh",
            "_score": null,
            "fields": {
               "report_time": [
                  "20141202170039"
               ],
               "pcr": [
                  "-0.819512"
               ],
               "src_ip": [
                  "192.168.1.151"
               ],
               "dst_ip": [
                  "172.16.3.6"
               ]
            },
            "sort": [
               "20141202170039",
               "192.168.1.151"
            ]
         },
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "CEGtLa2Vx4PnRChNS2",
            "_score": null,
            "fields": {
               "report_time": [
                  "20141202170007"
               ],
               "pcr": [
                  "-0.679245"
               ],
               "src_ip": [
                  "192.168.1.151"
               ],
               "dst_ip": [
                  "172.16.3.6"
               ]
            },
            "sort": [
               "20141202170007",
               "192.168.1.151"
            ]
         },
```

### Request Body Search(QUERY DSL)

- 기본 검색

```
GET _search
{
  "query": {
    "match_all": {}
  }
}
```
`Result`
```
{
   "took": 4,
   "timed_out": false,
   "_shards": {
      "total": 28,
      "successful": 28,
      "failed": 0
   },
   "hits": {
      "total": 692211,
      "max_score": 1,
      "hits": [
         {
            "_index": ".marvel-2014.12.03",
            "_type": "node_stats",
            "_id": "AUoQL90PWVwb65544Xyb",
            "_score": 1,
            "_source": {
               "@timestamp": "2014-12-03T12:46:19.677Z",
               "cluster_name": "elasticsearch",
               "node": {
                  "id": "b-I02eBqQqCgp7hZaezZpw",
                  "name": "Geirrodur",
                  "transport_address": "inet[/172.16.100.102:9300]",
                  "ip": "172.16.100.102",
                  "host": "ConnecTome-maker",
                  "ip_port": "172.16.100.102:9300",
                  "master_node": true,
                  "data_node": true,
                  "master": true
               },
               "indices": {
                  "docs": {
                     "count": 32423,
                     "deleted": 5032
                  },
                  "store": {
                     "size_in_bytes": 18374197,
                     "throttle_time_in_millis": 0
                  },
                  "indexing": {
                     "index_total": 0,
                     "index_time_in_millis": 0,
                     "index_current": 0,
                     "delete_total": 0,
                     "delete_time_in_millis": 0,
                     "delete_current": 0,
                     "noop_update_total": 0,
                     "is_throttled": false,
                     "throttle_time_in_millis": 0
                  },
```

```
GET /connectome-20141205/_search
{
  "size": 1000,
  "query": {
    "match_all": {}
  }
}
```
`Result`
```
{
   "took": 32,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 259748,
      "max_score": 1,
      "hits": [
         {
            "_index": "connectome-20141205",
            "_type": "osfound",
            "_id": "CzJQ1R2yS4P08omf5d",
            "_score": 1,
            "_source": {
               "report_time": "20141205000053",
               "device_ip": "172.16.2.3",
               "uid": "CzJQ1R2yS4P08omf5d",
               "os_ip": "192.168.1.29",
               "@timestamp": "2014-12-05T00:00:53+00:00",
               "elastic_time": "20141205000104",
               "log_type_Name": "osfound.log",
               "eps_time": "1417705253.594825",
               "os_type": "[genre=Windows, detail=2000 SP2+, XP SP1+ (seldom 98), Vista SP1, 7 SP1, 2008 SP2, dist=0, match_type=direct_inference]"
            }
         },
         {
            "_index": "connectome-20141205",
            "_type": "osfound",
            "_id": "CAk4Nc2xsOyIIX80li",
            "_score": 1,
            "_source": {
               "report_time": "20141205000059",
               "device_ip": "172.16.2.3",
               "uid": "CAk4Nc2xsOyIIX80li",
               "os_ip": "192.168.1.29",
               "@timestamp": "2014-12-05T00:00:59+00:00",
               "elastic_time": "20141205000104",
               "log_type_Name": "osfound.log",
               "eps_time": "1417705259.407321",
               "os_type": "[genre=Windows, detail=2000 SP2+, XP SP1+ (seldom 98), Vista SP1, 7 SP1, 2008 SP2, dist=0, match_type=direct_inference]"
            }
         },
```
#### term query
- term은 형태소 분석을 통해 추출된 색인어로, 검색 시 입력한 질이어와는 다르다.
- lowercase filter가 적용되기 때문에(stanard_analyzer) 질이어로 사용하는 텍스트는 소문자여야 하고, 대문자로 입력하면 검색되지 않는다.
- 당연히 'korea republic' 이나 'repu' 등으로 검색 되지 않으며, *(Asterisk)등의 기호도 사용할 수 없다.
```
$ curl -XGET 'http://localhost:9200/connectome-20141202/conn/_search?pretty' -d '{
    "query" : {
        "term" : { "src_ip" : "192.168.1.151" }
    }
}
'
```
`Result`
```
{
   "took": 3,
   "timed_out": false,
   "_shards": {
      "total": 1,
      "successful": 1,
      "failed": 0
   },
   "hits": {
      "total": 11073,
      "max_score": 5.6057773,
      "hits": [
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "Cqs6Oc2M1i18Ox7jv6",
            "_score": 5.6057773,
            "_source": {
               "report_time": "20141202100458",
               "src_bytes": "2190",
               "uid": "Cqs6Oc2M1i18Ox7jv6",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-02T10:04:58+00:00",
               "resp_cname": "United States",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": "2436",
               "dst_asn": "15169",
               "dst_asname": "Google Inc.",
               "duration": "0.458304",
               "src_lati": "0.0",
               "src_port": "49375",
               "src_country_name": "unknown-",
               "conn_state": "SF",
               "pcr": "0.055422",
               "elastic_time": "20141202100925",
               "src_asn": "0",
               "src_ip": "192.168.1.151",
               "src_cc": "(empty)",
               "src_pkts": "16",
               "dst_long": "-122.057404",
               "dst_cc": "US",
               "device_ip": "172.16.2.3",
               "prtc": "tcp",
               "dst_lati": "37.419201",
               "missed_bytes": "0",
               "app_prtc": "ssl",
               "conn_local": "T",
               "dst_pkts": "9",
               "log_type_Name": "conn.log",
               "src_long": "0.0",
               "eps_time": "1417482298.255246",
               "dst_port": "443",
               "dst_ip": "173.194.117.225",
               "src_ip_bytes": "3034",
               "dst_bytes": "1960",
               "history": "ShADadFf"
            }
         },
```
```
GET /connectome-20141202/conn/_search?pretty
{   "size": 100,
    "from": 0,
    "fields": ["report_time","src_ip", "dst_ip", "pcr"],
    "sort": [
      {
        "report_time": {
          "order": "desc"
        },
         "src_ip": {
          "order": "asc"
        }
      }
    ],
    "query" : {
        "term" : { "src_ip" : "192.168.1.151" }
    }
}
```
`Result`
```
{
   "took": 10,
   "timed_out": false,
   "_shards": {
      "total": 1,
      "successful": 1,
      "failed": 0
   },
   "hits": {
      "total": 11114,
      "max_score": null,
      "hits": [
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "CIUcj8314cEm7xWo5d",
            "_score": null,
            "fields": {
               "report_time": [
                  "20141202171656"
               ],
               "pcr": [
                  "-0.786848"
               ],
               "src_ip": [
                  "192.168.1.151"
               ],
               "dst_ip": [
                  "172.16.3.6"
               ]
            },
            "sort": [
               "20141202171656",
               "192.168.1.151"
            ]
         },
         {
            "_index": "connectome-20141202",
            "_type": "conn",
            "_id": "COkARS1if8sGEZECH8",
            "_score": null,
            "fields": {
               "report_time": [
                  "20141202171655"
               ],
               "pcr": [
                  "-0.801453"
               ],
               "src_ip": [
                  "192.168.1.151"
               ],
               "dst_ip": [
                  "172.16.3.6"
               ]
            },
            "sort": [
               "20141202171655",
               "192.168.1.151"
            ]
         },
```
- 대문자 Korea는 검색 되지 않음

```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "term" : {
          "resp_cname" : "Korea"
    }
  }
}
```
`Result`

```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 0,
      "max_score": null,
      "hits": []
   }
}
```
- 소문자 korea로 검색

```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "term" : {
          "resp_cname" : "korea"
    }
  }
}
```
```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 10197,
      "max_score": 1.3142498,
      "hits": [
         {
            "_index": "connectome-20141207",
            "_type": "conn",
            "_id": "C8I0X31gLW8wwxBON",
            "_score": 1.3142498,
            "_source": {
               "report_time": "20141207000746",
               "src_bytes": 659,
               "uid": "C8I0X31gLW8wwxBON",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-07T00:07:46+00:00",
               "resp_cname": "Korea Republic of",
```


#### terms query
- term와 같은 쿼리로 대상 필드에 복수 개의 term이 포함된 도큐먼트를 검색한다. 이 쿼리는 mininum match설정을 통해 AND와 OR연산을 적용할 수 있다.
- 여러개의 term이 모두 포함된 문서만 찾고 싶으면 minimum_match를 term크기만큼 지정하고, 그 중 하나의 term이라도 포함된 문서를 찾고 싶다면 minumum_match를 1로 설정한다.
- 아래는 별 의미없는 샘플 예제, src_ip는 어짜피 하나만 존재한다.

`example`
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "terms" : {
          "src_ip" : ["192.168.1.31"],
          "minimum_match": 1
    }
  }
}
```
```
{
   "took": 2,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 3780,
      "max_score": 3.4575784,
      "hits": [
         {
            "_index": "connectome-20141207",
            "_type": "conn",
            "_id": "CBuVoZ2Wh9ur427PNd",
            "_score": 3.4575784,
            "_source": {
               "report_time": "20141207000401",
               "src_bytes": -1,
               "uid": "CBuVoZ2Wh9ur427PNd",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-07T00:04:01+00:00",
               "resp_cname": "unknown-",
               "tunnel_parents": "(empty)",
```

- term와 같은 원리로 대문자로 검색할 수 없음, terms쿼리는 반드시 배열로 지정하여야 검색 가능함
- "Korea Republic of"에 대해서 모두 포함되어 있는 문서를 검색시 배열에 모두 포함시켜 주고 minumum_match를 3으로 설정함, 그렇다고 해서 exact match 검색은 아님, 중복, 순서가 고려되지 않음

```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "terms" : {
          "resp_cname" : ["korea"],
          "minimum_match": 1
    }
  }
}
```
`Result`
```
{
   "took": 4,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 10731,
      "max_score": null,
      "hits": [
         {
            "_index": "connectome-20141207",
            "_type": "conn",
            "_id": "CfQi1x3CHSfblLqkm9",
            "_score": null,
            "_source": {
               "report_time": "20141207001723",
               "src_bytes": 2117,
               "uid": "CfQi1x3CHSfblLqkm9",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-07T00:17:23+00:00",
               "resp_cname": "Korea Republic of",
```
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "terms" : {
          "resp_cname" : ["korea", "republic", "fighting"],
          "minimum_match": 1
    }
  }
}
```
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "terms" : {
          "resp_cname" : ["korea", "republic", "fighting"],
          "minimum_match": 2
    }
  }
}
```
#### match query
- term쿼리와 비슷한 기능으로, 지정한 필드에서 Query String형태로 문서를 검색한다.
- term쿼리와는 다르게 쿼리에 대해서 분석 작업이 필요하다.(search_analyzer)
- search_analyzer는 기본적으로 lowercase filter가 동작하므로 대문자로 검색이 가능하다.
- 아래는 별 의미없는 샘플 예제, src_ip는 어짜피 하나만 존재한다.(sorting 예제 포함)
- *(Asterisk) 문자 사용 불가능

`example`
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "src_ip" : "192.168.1.31"
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```
`Result`
```
{
   "took": 9,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 3558,
      "max_score": null,
      "hits": [
         {
            "_index": "connectome-20141207",
            "_type": "conn",
            "_id": "CN2zka48CQOjR0ksCe",
            "_score": null,
            "_source": {
               "report_time": "20141207005829",
               "src_bytes": 7503,
               "uid": "CN2zka48CQOjR0ksCe",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-07T00:58:29+00:00",
               "resp_cname": "United States",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": 16577,
               "dst_asn": 8075,
```

- operator를 지정하지 않으면 기본적으로 or연산자가 적용

```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "resp_cname" : "Korea Republic"
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "resp_cname" : "Korea fighting"
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "resp_cname" : {
            "query": "Korea",
            "operator": "and"
          }
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```
```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "resp_cname" : {
            "query": "Korea",
            "operator": "or"
          }
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```

```
GET /connectome-20141207/conn/_search
{
  "query" : {
    "match" : {
          "resp_cname" : {
            "query": "Korea Republic",
            "operator": "and"
          }
    }
  },
  "sort": [
    {
      "app_prtc": {
        "order": "desc"
      }
    },
     {
      "report_time": {
        "order": "acs"
      }
    }
  ]
}
```

```
GET /connectome-20141206/conn/_search
{
  "query" : {
    "match" : {
          "history" : {
               "query": "S D",
               "operator": "or"
          }
    }
  }
}
```
#### multi match query
- match쿼리와 방법은 같으나 하나의 필드가 아니라 여러 개의 필드에서 쿼리 스트링으로 검색한다.
- operator는 match와 같이 query에 적용된다.

```
GET /connectome-20141205/conn/_search
{ "query": {
      "multi_match": {
        "query": "192.168.1.31 172.16.1.150",
        "fields": ["src_ip", "dst_ip"],
        "operator" : "or"
    }
  },
  "size": 1000
}
```
`Result`
```
{
   "took": 24,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 12154,
      "max_score": 2.221059,
      "hits": [
         {
            "_index": "connectome-20141205",
            "_type": "conn",
            "_id": "Cv3dg31Fh1PcrolWF9",
            "_score": 2.221059,
            "_source": {
               "report_time": "20141205010153",
               "src_bytes": 96,
               "uid": "Cv3dg31Fh1PcrolWF9",
               "src_asname": "Korea Telecom",
               "@timestamp": "2014-12-05T01:01:53+00:00",
```

#### Query String Query
- 전통으로 많이 사용하는 쿼리로, 대상 필드에 쿼리 스트링 질의로 문서를 검색한다.
- search uri나 Query String Query로 할 수 있는 것들을 우선 사용하자
- term/terms, match/multi-match는 이런게 있다는 것만 알아두자
- Lucene의 Query Parser Syntax를 사용한다.

```
GET /connectome-20141205/conn/_search
{ "query": {
      "query_string": {
        "query": "*:*"
    }
  }
}
```

```
GET /connectome-20141205/conn/_search
{ "query": {
      "query_string": {
        "default_field": "src_ip",
        "query": "192.168.1.29"
    }
  }
}
```

- [elasticsearch.com](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html) 페이지에서는 같다고 하였는데 실제 다르게 나옴, 검증이 필요함

```
GET /connectome-20141205/conn/_search
{ "query": {
      "query_string": {
        "fields": ["src_ip", "dst_ip"],
        "query": "192.168.* AND 172.16.*"
    }
  },
  "size": 1000
}

GET /connectome-20141205/conn/_search
{ "query": {
      "query_string": {
        "query": "(src_ip:192.168.* OR src_ip:172.16.*) AND (dst_ip:192.168.* OR dst_ip:172.16.*)"
    }
  },
  "size": 1000
}
```

#### Identifiers Query
- unique id 즉 _id 값을 이용해서 세부정보를 가장 빠르게 가져올 수 있는 쿼리
- index속성이 not_analyze되어 있어야 함

```
GET /connectome-20141205/_search
{
  "query" : {
    "ids": {
      "type" : "conn",
      "values": ["Cob3ol2UHf9asdfsp6"]
    }
  }
}
```
`Result`
```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 1,
      "max_score": 1,
      "hits": [
         {
            "_index": "connectome-20141205",
            "_type": "conn",
            "_id": "Cob3ol2UHf9asdfsp6",
            "_score": 1,
            "_source": {
               "report_time": "20141205000741",
               "src_bytes": 68,
               "uid": "Cob3ol2UHf9asdfsp6",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-05T00:07:41+00:00",
               "resp_cname": "unknown-",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": 80,
               "dst_asn": 0,
               "dst_asname": "unknown-0",
               "duration": 0.000072,
               "src_lati": 0,
               "src_port": "123",
               "src_country_name": "unknown-",
               "conn_state": "SF",
               "pcr": 0.133333,
               "elastic_time": "20141205000856",
               "src_asn": 0,
               "src_ip": "192.168.1.55",
               "src_cc": "(empty)",
               "src_pkts": 1,
               "dst_long": 0,
               "dst_cc": "(empty)",
               "device_ip": "172.16.2.3",
               "prtc": "udp",
               "dst_lati": 0,
               "missed_bytes": 0,
               "app_prtc": "-",
               "conn_local": "T",
               "dst_pkts": 1,
               "log_type_Name": "conn.log",
               "src_long": 0,
               "eps_time": "1417705661.256349",
               "dst_port": "123",
               "dst_ip": "172.16.1.150",
               "src_ip_bytes": 96,
               "dst_bytes": 52,
               "history": "Dd"
            }
         }
      ]
   }
}
```

#### Prefix Query
- Text의 앞 문자열에 대한 term의 일치 여부에 따라 검색 결과를 가져옴
- 검색 대상 필드는 인덱스 속성이 not_analyzed로 설정되어야 함

```
GET /connectome-20141207/conn/_search
{
  "query": {
    "prefix" : {
      "src_ip": {
        "value" : "172.16."
      }
    }
  }
}
```

####Match All Query
- 이 쿼리는 "SELECT * FROM TABLE" SQL처럼 모든 도큐먼트를 구해오는 쿼리
- 검색서비스에서는 거의 사용하지 않고, 보통 관리 목적으로 사용함

```
GET _search
{
  "query": {
    "match_all": {}
  }
}
```

####Range Query
- term쿼리에 유사하게 많이 사용하는 형태
- number, date, ip 등의 datatype에 대해서 효과적으로 사용할 수 있다.
- not_analyzed로 설정되어야 사용 가능하다.

```
GET /connectome-20141207/conn/_search
{ "query": {
    "range": {
      "duration": {
        "gte": 2000,
        "lte": 3000
      }
    }
  }
}
```
`Result`
```
{
   "took": 2,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 7,
      "max_score": 1,
      "hits": [
         {
            "_index": "connectome-20141207",
            "_type": "conn",
            "_id": "Cq6Xe14zMRdTro9C4c",
            "_score": 1,
            "_source": {
               "report_time": "20141207064839",
               "src_bytes": 6044,
               "uid": "Cq6Xe14zMRdTro9C4c",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-07T06:48:39+00:00",
               "resp_cname": "United States",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": 15219,
               "dst_asn": 15169,
               "dst_asname": "Google Inc.",
               "duration": 2100.135519,
```

### aggregations
#### terms aggregation
- group by 형태의 검색
- 앞쪽에 size:0 이 지정되지 않으면 기본적으로 10개의 document가 출력된다.++
- aggs에 포함된 size는 term aggregation의 경우 상위 10개만 출력됨 , 기본적으로 desc 정렬됨

```
curl -XPOST 'localhost:9200/connectome-20141029/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_src_ip": {
      "terms": {
        "field": "src_ip",
        "size" : 0
      }
    }
  }
}'
```
```
curl -XPOST 'localhost:9200/connectome-20141029/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_proto": {
      "terms": {
        "field": "prtc",
        "size" : 0
      }
    }
  }
}'
```
`Result`
```
{
   "took": 501,
   "timed_out": false,
   "_shards": {
      "total": 1,
      "successful": 1,
      "failed": 0
   },
   "hits": {
      "total": 8615647,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "group_by_proto": {
         "buckets": [
            {
               "key": "udp",
               "doc_count": 1969935
            },
            {
               "key": "tcp",
               "doc_count": 1614271
            },
            {
               "key": "icmp",
               "doc_count": 17007
            }
         ]
      }
   }
}
```
- 내/외부 src_ip분포

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "group_by_local": {
      "terms": {
        "field": "conn_local"
      }
    }
  }
}
```
```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 40802,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "group_by_local": {
         "doc_count_error_upper_bound": 0,
         "sum_other_doc_count": 0,
         "buckets": [
            {
               "key": "t",
               "doc_count": 37663
            },
            {
               "key": "f",
               "doc_count": 3139
            }
         ]
      }
   }
}
```

####Min Aggregation
- 기본적으로 size를 지정하지 않으면 10개의 데이터 출력 후 sum aggregation정보 출력

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "min_duration": {
      "min": {
        "field": "duration"
      }
    }
  }
}
```
`Result`
```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 40967,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "min_duration": {
         "value": -1
      }
   }
}
```

####Max Aggregation

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "max_duration": {
      "max": {
        "field": "duration"
      }
    }
  }
}
```
`Result`
```
{
   "took": 9,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 41041,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "max_duration": {
         "value": 36299.162081
      }
   }
}
```

####Sum Aggregation

```
POST /connectome-20141207/conn/_search?pretty
{
  "aggs": {
    "src_bytes_sum": {
      "sum": {
        "field": "src_bytes"
      }
    },
    "dst_bytes_sum": {
      "sum": {
        "field": "dst_bytes"
      }
    }
  }
}
```
`Result`
```
{
   "took": 19,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 40186,
      "max_score": 1,
      "hits": [
      ]
   },
   "aggregations": {
      "src_bytes_sum": {
         "value": 141909289
      },
      "dst_bytes_sum": {
         "value": 315008631
      }
   }
}
```
```
POST /connectome-20141207/conn/_search?pretty
{ "size": 0,
  "aggs": {
    "src_bytes_sum": {
      "sum": {
        "field": "src_bytes"
      }
    },
    "dst_bytes_sum": {
      "sum": {
        "field": "dst_bytes"
      }
    }
  }
}
```
`Result`
```
{
   "took": 5,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 40609,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "src_bytes_sum": {
         "value": 141996380
      },
      "dst_bytes_sum": {
         "value": 315097623
      }
   }
}
```


####Avg Aggregation

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "avg_duration": {
      "avg": {
        "field": "duration"
      }
    }
  }
}
```
`Result`
```
{
   "took": 18,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 41100,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "avg_duration": {
         "value": 14.847179138442826
      }
   }
}
```

####Stats Aggregation

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "stats_duration": {
      "stats": {
        "field": "duration"
      }
    }
  }
}
```
`Result`
```
{
   "took": 20,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 41129,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "stats_duration": {
         "count": 41129,
         "min": -1,
         "max": 36299.162081,
         "avg": 14.85006427158453,
         "sum": 610768.2934260002
      }
   }
}
```
```

GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "stats_duration": {
      "stats": {
        "field": "duration"
      }
    }
  },
  "query": {
    "query_string": {
      "query": "NOT duration:(\\-1)"
    }
  }
}
```
```
{
   "took": 7,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 37251,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "stats_duration": {
         "count": 37251,
         "min": 0.000003,
         "max": 36299.162081,
         "avg": 17.077468715121743,
         "sum": 636152.787107
      }
   }
}
```

####Ranges Aggregation

- Range를 정의하여 group by 수행

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "ranges_duration": {
      "range": {
        "field": "duration",
        "ranges": [
          {"to": 600},
          {"from": 600, "to": 1200},
          {"from": 1200, "to": 1800},
          {"from": 1800, "to": 2400},
          {"from": 3000, "to": 3600},
          {"from": 3600}
        ]
      }
    }
  }
}
```
`Result`
```
{
   "took": 16,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 41327,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "ranges_duration": {
         "buckets": [
            {
               "key": "*-600.0",
               "to": 600,
               "to_as_string": "600.0",
               "doc_count": 41245
            },
            {
               "key": "600.0-1200.0",
               "from": 600,
               "from_as_string": "600.0",
               "to": 1200,
               "to_as_string": "1200.0",
               "doc_count": 40
            },
            {
               "key": "1200.0-1800.0",
               "from": 1200,
               "from_as_string": "1200.0",
               "to": 1800,
               "to_as_string": "1800.0",
               "doc_count": 11
            },
            {
               "key": "1800.0-2400.0",
               "from": 1800,
               "from_as_string": "1800.0",
               "to": 2400,
               "to_as_string": "2400.0",
               "doc_count": 6
            },
            {
               "key": "3000.0-3600.0",
               "from": 3000,
               "from_as_string": "3000.0",
               "to": 3600,
               "to_as_string": "3600.0",
               "doc_count": 2
            },
            {
               "key": "3600.0-*",
               "from": 3600,
               "from_as_string": "3600.0",
               "doc_count": 21
            }
         ]
      }
   }
}
```

####Histogram Aggregation

- Range를 Interval로 입력하여 group by 수행

```
GET /connectome-20141207/conn/_search
{ "size": 0,
  "aggs": {
    "histogram_duration": {
      "histogram": {
        "field": "duration",
        "interval": 600
      }
    }
  }
}
```
`Result`
```
{
   "took": 5,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 42679,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "histogram_duration": {
         "buckets": [
            {
               "key": -600,
               "doc_count": 4528
            },
            {
               "key": 0,
               "doc_count": 38061
            },
            {
               "key": 600,
               "doc_count": 44
            },
            {
               "key": 1200,
               "doc_count": 11
            },
            {
               "key": 1800,
               "doc_count": 7
            },
            {
               "key": 2400,
               "doc_count": 2
            },
            {
               "key": 3000,
               "doc_count": 2
            },
            {
               "key": 4200,
               "doc_count": 18
            },
            {
               "key": 4800,
               "doc_count": 1
            },
            {
               "key": 8400,
               "doc_count": 2
            },
            {
               "key": 9600,
               "doc_count": 1
            },
            {
               "key": 19200,
               "doc_count": 1
            },
            {
               "key": 36000,
               "doc_count": 1
            }
         ]
      }
   }
}
```

- 시간당 histogram

```
GET /connectome-20141209/conn/_search
{ "size": 0,
  "aggs": {
    "histogram_date": {
      "histogram": {
        "field": "@timestamp",
        "interval": 3600000
      }
    }
  }
}
```
`Result`
```
{
   "took": 25,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 108778,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "histogram_date": {
         "buckets": [
            {
               "key_as_string": "2014-12-09T00:00:00.000Z",
               "key": 1418083200000,
               "doc_count": 2239
            },
            {
               "key_as_string": "2014-12-09T01:00:00.000Z",
               "key": 1418086800000,
               "doc_count": 2161
            },
            {
               "key_as_string": "2014-12-09T02:00:00.000Z",
               "key": 1418090400000,
               "doc_count": 2002
            },
            {
               "key_as_string": "2014-12-09T03:00:00.000Z",
               "key": 1418094000000,
               "doc_count": 2713
            },
            {
               "key_as_string": "2014-12-09T04:00:00.000Z",
               "key": 1418097600000,
               "doc_count": 1821
            },
            {
               "key_as_string": "2014-12-09T05:00:00.000Z",
               "key": 1418101200000,
               "doc_count": 1461
            },
            {
               "key_as_string": "2014-12-09T06:00:00.000Z",
               "key": 1418104800000,
               "doc_count": 1697
            },
            {
               "key_as_string": "2014-12-09T07:00:00.000Z",
               "key": 1418108400000,
               "doc_count": 2317
            },
            {
               "key_as_string": "2014-12-09T08:00:00.000Z",
               "key": 1418112000000,
               "doc_count": 3539
            },
            {
               "key_as_string": "2014-12-09T09:00:00.000Z",
               "key": 1418115600000,
               "doc_count": 12607
            },
            {
               "key_as_string": "2014-12-09T10:00:00.000Z",
               "key": 1418119200000,
               "doc_count": 8612
            },
            {
               "key_as_string": "2014-12-09T11:00:00.000Z",
               "key": 1418122800000,
               "doc_count": 13738
            },
            {
               "key_as_string": "2014-12-09T12:00:00.000Z",
               "key": 1418126400000,
               "doc_count": 17208
            },
            {
               "key_as_string": "2014-12-09T13:00:00.000Z",
               "key": 1418130000000,
               "doc_count": 6518
            },
            {
               "key_as_string": "2014-12-09T14:00:00.000Z",
               "key": 1418133600000,
               "doc_count": 14880
            },
            {
               "key_as_string": "2014-12-09T15:00:00.000Z",
               "key": 1418137200000,
               "doc_count": 12090
            },
            {
               "key_as_string": "2014-12-09T16:00:00.000Z",
               "key": 1418140800000,
               "doc_count": 3175
            }
         ]
      }
   }
}
```


- 현재 시간 기준으로 일별 histogram

```
GET /_all/conn/_search
{ "size": 0,
  "aggs": {
    "histogram_date": {
      "histogram": {
        "field": "@timestamp",
        "interval": 144000000
      }
    }
  }
}
```
`Result`
```
{
   "took": 24,
   "timed_out": false,
   "_shards": {
      "total": 69,
      "successful": 63,
      "failed": 0
   },
   "hits": {
      "total": 759372,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "histogram_date": {
         "buckets": [
            {
               "key": 1417392000000,
               "doc_count": 5
            },
            {
               "key": 1417536000000,
               "doc_count": 127711
            },
            {
               "key": 1417680000000,
               "doc_count": 184003
            },
            {
               "key": 1417824000000,
               "doc_count": 100107
            },
            {
               "key": 1417968000000,
               "doc_count": 254950
            },
            {
               "key": 1418112000000,
               "doc_count": 92596
            }
         ]
      }
   }
}
```

####Date Range Aggregation

```
GET /_all/conn/_search
{
  "size": 0,
  "aggs": {
      "range": {
          "date_range": {
              "field": "@timestamp",
              "format": "YYYY-MM-dd",
              "ranges": [
                  { "from" : "now-2d", "to": "now-1d" },
                  { "from": "now-1d" }
              ]
          }
      }
  }
}
```
`Result`
```
{
   "took": 16,
   "timed_out": false,
   "_shards": {
      "total": 69,
      "successful": 69,
      "failed": 0
   },
   "hits": {
      "total": 758434,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "range": {
         "buckets": [
            {
               "key": "2014-12-07-2014-12-08",
               "from": 1417936217609,
               "from_as_string": "2014-12-07",
               "to": 1418022617609,
               "to_as_string": "2014-12-08",
               "doc_count": 61202
            },
            {
               "key": "2014-12-08-*",
               "from": 1418022617609,
               "from_as_string": "2014-12-08",
               "doc_count": 307813
            }
         ]
      }
   }
}
```

####Ip Range Aggregation
- 데이터를 ip type으로 지정하면 ipv6에 대해서는 수용하지 못하고 drop됨
- string검색은 불가능함
- (from ~ to) 방식

```
GET /test1/type1/_search
{
  "size": 0,
  "aggs" : {
        "ip_ranges" : {
            "ip_range" : {
                "field" : "src_ip",
                "ranges" : [
                    { "to" : "192.168.1.55" },
                    { "from" : "192.168.1.55" }
                ]
            }
        }
    }
}
```
`Result`
```
{
   "took": 16,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 75125,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "ip_ranges": {
         "buckets": [
            {
               "key": "*-192.168.1.55",
               "to": 3232235831,
               "to_as_string": "192.168.1.55",
               "doc_count": 11899
            },
            {
               "key": "192.168.1.55-*",
               "from": 3232235831,
               "from_as_string": "192.168.1.55",
               "doc_count": 63226
            }
         ]
      }
   }
}
```

- mask방식

```
GET /test1/type1/_search
{
  "size": 0,
  "aggs" : {
        "ip_ranges" : {
            "ip_range" : {
                "field" : "src_ip",
                "ranges" : [
                    { "mask" : "192.168.1.0/24" },
                    { "mask" : "172.16.2.0/24" }
                ]
            }
        }
    }
}
```
`Result`
```
{
   "took": 8,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 75125,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "ip_ranges": {
         "buckets": [
            {
               "key": "172.16.2.0/24",
               "from": 2886730240,
               "from_as_string": "172.16.2.0",
               "to": 2886730496,
               "to_as_string": "172.16.3.0",
               "doc_count": 61
            },
            {
               "key": "192.168.1.0/24",
               "from": 3232235776,
               "from_as_string": "192.168.1.0",
               "to": 3232236032,
               "to_as_string": "192.168.2.0",
               "doc_count": 73773
            }
         ]
      }
   }
}
```
####Cardinality Aggregation

- unique count 를 계산하기 위해서 사용됨

```
GET /connectome-20141207/_search
{ "size": 0,
  "query": {
      "query_string": {
        "query": "*:*"
    }
  },
  "aggs": {
    "src_ip_count": {
      "cardinality": {
        "field": "src_ip"
      }
    },
    "dst_ip_count": {
      "cardinality": {
        "field": "dst_ip"
      }
    },
    "dst_cc_count": {
      "cardinality": {
        "field": "dst_cc"
      }
    },
    "dst_port_count": {
      "cardinality": {
        "field": "dst_port"
      }
    }
  }
}}
```
`Result`
```
{
   "took": 9,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 70551,
      "max_score": 0,
      "hits": []
   },
   "aggregations": {
      "dst_cc_count": {
         "value": 63
      },
      "dst_ip_count": {
         "value": 5092
      },
      "dst_port_count": {
         "value": 81
      },
      "src_ip_count": {
         "value": 973
      }
   }
}
```


####기타

- 음수(negative value)검색

```
GET /connectome-20141205/_search?q=duration:(\-1)&size=100
```

```
GET /connectome-20141205/_search
{ "query": {
      "query_string": {
        "query": "duration:(\\-1)"
    }
  }
}

```
`Result`
```
{
   "took": 1,
   "timed_out": false,
   "_shards": {
      "total": 5,
      "successful": 5,
      "failed": 0
   },
   "hits": {
      "total": 13638,
      "max_score": 1,
      "hits": [
         {
            "_index": "connectome-20141205",
            "_type": "conn",
            "_id": "CW7uUz1MefhtkT5q7",
            "_score": 1,
            "_source": {
               "report_time": "20141205000036",
               "src_bytes": -1,
               "uid": "CW7uUz1MefhtkT5q7",
               "src_asname": "unknown-0",
               "@timestamp": "2014-12-05T00:00:36+00:00",
               "resp_cname": "Korea Republic of",
               "tunnel_parents": "(empty)",
               "dst_ip_bytes": 0,
               "dst_asn": 4766,
               "dst_asname": "Korea Telecom",
               "duration": -1,
               "src_lati": 0,
               "src_port": "59481",
               "src_country_name": "unknown-",
               "conn_state": "S0",
               "pcr": -1,
               "elastic_time": "20141205000048",
               "src_asn": 0,
               "src_ip": "192.168.1.29",
               "src_cc": "(empty)",
               "src_pkts": 1,
               "dst_long": 126.980003,
               "dst_cc": "KR",
               "device_ip": "172.16.2.3",
               "prtc": "tcp",
               "dst_lati": 37.57,
               "missed_bytes": 0,
               "app_prtc": "-",
               "conn_local": "T",
               "dst_pkts": 0,
               "log_type_Name": "conn.log",
               "src_long": 0,
               "eps_time": "1417705236.438597",
               "dst_port": "6881",
               "dst_ip": "222.98.132.4",
               "src_ip_bytes": 48,
               "dst_bytes": -1,
               "history": "S"
            }
         },
```

- 특수문자로 dash(-)는 같은 방법을 검색 불가능

```
GET /connectome-20141205/_search?q=app_prtc:(\-)

GET /connectome-20141205/_search
{ "query": {
      "query_string": {
        "query": "app_prtc:(\\-)"
    }
  }
}
```

### search template
### search shards api
- 검색을 수행하는 인덱스와 샤드의 정보를 검색하는 명령어

### facets
++Facets are deprecated and will be removed in a future release. You are encouraged to migrate to aggregations instead.++

### suggesters

### multi search api

### count api

### search exists api

### validate api

### explain api
- like expalin SQL

### percolator
- Traditionally you design documents based on your data and store them into an index and then define queries via the search api in order to retrieve these documents. The percolator works in the opposite direction, first you store queries into an index and then via the percolate api you define documents in order to retrieve these queries.

### more like this api

## Analysis

```
curl -XGET 'localhost:9200/_analyze?analyzer=standard&pretty' -d 'this is a test'
```
`Result`
```
{
  "tokens" : [ {
    "token" : "this",
    "start_offset" : 0,
    "end_offset" : 4,
    "type" : "<ALPHANUM>",
    "position" : 1
  }, {
    "token" : "is",
    "start_offset" : 5,
    "end_offset" : 7,
    "type" : "<ALPHANUM>",
    "position" : 2
  }, {
    "token" : "a",
    "start_offset" : 8,
    "end_offset" : 9,
    "type" : "<ALPHANUM>",
    "position" : 3
  }, {
    "token" : "test",
    "start_offset" : 10,
    "end_offset" : 14,
    "type" : "<ALPHANUM>",
    "position" : 4
  } ]
}
```
`example`
```
#!/usr/bin/env python
# encoding: utf-8
import os
import time
import datetime
from elasticsearch import Elasticsearch
es = Elasticsearch("172.16.100.102:9200")
delimiter = ","
file_extension = "csv"
#field = "all"
conn_fields = ['report_time', 'src_bytes', 'uid', 'src_asname', '@timestamp', 'resp_cname', 'tunnel_parents', 'dst_ip_bytes', 'dst_asn', 'dst_asname', 'duration', 'src_lati', 'src_port', 'src_country_name', 'conn_state', 'pcr', 'elastic_time', 'src_asn', 'src_ip', 'src_cc', 'app_prtc', 'dst_port', 'dst_cc', 'device_ip', 'prtc', 'missed_bytes', 'dst_lati', 'conn_local', 'dst_pkts', 'log_type_Name', 'src_long', 'eps_time', 'src_pkts', 'dst_ip', 'src_ip_bytes', 'dst_bytes', 'history']
# tagconn_fields = ['report_time', 'src_bytes', 'uid', 'src_asname', 'resp_cname', 'tunnel_parents', 'dst_asn', 'dst_asname', 'duration', 'src_lati', 'src_port', 'src_country_name', 'conn_state', 'src_asn', 'src_ip', 'src_cc', 'app_prtc', 'dst_port', 'dst_cc', 'device_ip', 'prtc', 'missed_bytes', 'dst_lati', 'conn_local', 'dst_pkts', 'log_type_Name', 'src_long', 'eps_time', 'src_pkts', 'dst_ip', 'src_ip_bytes', 'dst_bytes', 'history', 'dst_long', 'tt_ref', 'tt_url', 'tt_type', 'tt_desc', 'tt_domain', 'tt_fqdn', 'tt_ip']
tagconn_fields = ['report_time', 'resp_cname', 'dst_asname', 'duration', 'src_ip', 'app_prtc', 'dst_ip', 'prtc', 'dst_port', 'src_bytes', 'dst_bytes', 'src_pkts', 'dst_pkts'];
def makeFileName() :
    timestamp = datetime.datetime.fromtimestamp(time.time()).strftime("%Y%m%d%H%M%S");
    filename = timestamp + "_" + "query_result" + "."  + file_extension;
    return filename
def main() :
    filename = makeFileName()
    csv_fd = open(filename, 'w')
    res = es.search(index="connectome-20141124", doc_type="tagconn", body={"size": 10000, "query": {"range": {"duration" : { "gte" : 3000 }}}})
    # index부분을 명시하지 않으면 모든 index에서 검색한다. size를 명시하지 않으면 기본적으로 10개만 가져 온다.
    # res = es.search(doc_type="tagconn", body={"query": {"range": {"duration" : { "gte" : 3000 }}}})
    print("Got %d Hits:" % res['hits']['total'])
    csv_fd.write("Got %d Hits:" % res['hits']['total'])
    csv_fd.write("\n")
    for hit in res['hits']['hits']:
        a = hit["_source"]
        #csv_fd.write("%s" %(a));
        #csv_fd.write("\n")
        line = ""
        for field in tagconn_fields :
            if (type(a[field]) == float) :
                line += "%0.1f" % a[field]
            else :
                line += "\"%s\"" % a[field]
            line += delimiter
        line = line[0:-1]
        csv_fd.write(line)
        csv_fd.write("\n")
    csv_fd.close()
if __name__ == '__main__':
    main()
```
