---
layout: article
title: elasticsearch 에서 색인 mapping 설정 팁
tag: [elasticsearch, mapping, dynamic]
mathjax: true
---
## elasticsearch 에서 동적 mapping 활용법

elasticsearch 에서 index 생성시 색인대상 규격을 설정하는 템플릿 (이하 mapping)이 존재합니다
mapping 설정 중 dynamic 이라는 설정이 있는데요 이 설정에 따라 지정된 규격외 색인을 받아드릴 수도, 지정된 규격외 색인을 거부하기도, 부분적으로 규격을 업데이트 할 수 있습니다. dynamic 설정에 대한 설명과 활용방법을 설명하겠습니다

### mapping dynamic  종류 및 의미

값 | 의미
:------------: | :-------------:
true | 새로운 필드가 감지되면 매핑에 추가, 색인 가능, 추가된 필드 검색 가능
false | 새로운 필드가 감지되면 매핑에 추가 안됨, 색인 가능, 추가된 필드 검색 불가, 추가된 필드 출력 가능
strict | 색인 시점에 알려지지 않은 필드를 추가 되면 예외를 발생하여 색인되지 않음


### [TIP] strict 설정인 index에 신규 필드 추가로 색인 하는 방법

보통 elasticsearch에 규격외 색인을 거부하고 검색대상 필드 여부를 지정해서 최소한의 색인크기로 최대의 성능을 뽑기 위해 `dynamic:strict` 설정을 사용하고 있는데 elasticsearch를 활용하다 보면 기존 설정을 가지고 있는 index에 필드가 추가된 데이터를 색인해야 하는 상황이 발생한다
`dynamic:strict` 설정일때 규격 외 JSON을 es에 던지면 예외를 발생시키나 Put mapping API를 이용하면 `dynamic:strict` 설정인 index에도 규격 외 JSON을 색인 할 수 있다

```json
{
  "new_index_1": {
    "mappings": {
      "v1": {
        "properties": {
          "count": {
            "type": "long"
          },
          "duration": {
            "type": "long"
          }
        }
      }
    }
  }
}
```
위와 같은 mapping 설정이 되어 있는 new_index_1 index가 있다고 가정 했을 경우 count, duration 필드 외 다른 추가적인 필드 혹은 데이터가 추가된 json이 new_index_1에 색인 요청이 올 경우 아래와 같은 메세지를 출력하게 된다
```bash
# soldOut이 추가된 JSON을 색인 요청
PUT new_index_1/v1/12345678
{
  "count": 17,
  "duration": 18,
  "soldOut": true,
}

# return 된 결과
{
  "error": {
    "root_cause": [
      {
        "type": "strict_dynamic_mapping_exception",
        "reason": "mapping set to strict, dynamic introduction of [soldOut] within [v1] is not allowed"
      }
    ],
    "type": "strict_dynamic_mapping_exception",
    "reason": "mapping set to strict, dynamic introduction of [soldOut] within [v1] is not allowed"
  },
  "status": 400
}
```
위 상황을 해결하기 위해선 soldOut이 추가된 JSON을 색인 요청 하기 전에 new_index_1 index에 mapping 설정을 추가 해주면 soldOut이 추가된 JSON이 색인된다

```bash
PUT new_index_1/_mapping/v1
{
  "properties": {
    "soldOut": {
      "type": "boolean"
    }
  }
}

# soldOut이 추가된 JSON을 색인 요청
PUT new_index_1/v1/12345678
{
  "count": 17,
  "duration": 18,
  "soldOut": true,
}

# return 된 결과
{
  "_index": "new_index_1",
  "_type": "v1",
  "_id": "12345678",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 1,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```

결론적으로 `dynamic:strict` 설정인 index라 할지라도 put mapping api를 이용하여 추가대상 필드 정보를 입력 하면 기존 mapping 형식에 추가된 상태로 색인이 가능하다 또한 색인 type의 변경도 가능하다 단 `object` → `nested`으로 변경은 가능하나 `nested` → `object`의 색인 타입 변경은 불가능 하다.


### 참고

 - elastic 공식홈페이지 elasticsearch reference mapping paramaters dynamic - [바로가기](https://www.elastic.co/guide/en/elasticsearch/reference/7.0/dynamic.html)