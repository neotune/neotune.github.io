---
layout: article
title: kibana scripted field sample
tag: [elasticsearch, kibana, scripted field]
mathjax: true
---
## kibana scripted field sample

kibana에서 visualize 생성시 기존 mapping 필드를 가지고 가공하여 새로운 필드를 생성 할 수 있는 scripted field의 script sample 몇가지를 공유 합니다 (kibana 5.5.1 기준으로 설명 드립니다)


### request URL 에서 API URI만 추출
```python
AS-IS : /api/search/v4/deals?keyword=%EB%8B%A4%EC%9D%B4%EC%96%B4%EB%A6%AC
TO-BE : /api/search/v4/deals
```

1. kibana → Management → Index Patterns → 스크립트 필드를 생성할 index 선택
2. scripted fields 텝 선택 → Add Scripted Field 버튼 클릭
3. Name에 원하는 필드 입력 (ex:script_URI)
4. Language painless 선택
5. Type String 선택
6. Format String 선택
7. Script에 아래의 내용 입력

```python
def request= doc['추출할 필드명'].value;
if (request != null){
  int start_num = request.indexOf('?');
  if (start_num > 0){
  return request.substring(0, start_num);
  }
}
return "";

# sample ---------------------------------
def request= doc['request.keyword'].value;
if (request != null){
  int start_num = request.indexOf('?');
  if (start_num > 0){
  return request.substring(0, start_num);
  }
}
return "";
```

### request URL 에 포함되어 있는 parameter에서 원하는 key의 value만  추출
```python
AS-IS : /api/search/v4/deals?platformType=IOS
TO-BE : IOS
```
1. 서버내 kibana 설치 폴더 안에 있는 elasticsearch.yml에 아래와 같이 설정 추가 (추가 되어 있다면 다음으로 패스) → kibana 재시작
```bash
script.painless.regex.enabled: true
```
2. kibana → Management → Index Patterns → 스크립트 필드를 생성할 index 선택
3. scripted fields 텝 선택 → Add Scripted Field 버튼 클릭
4. Name에 원하는 필드 입력 (ex:script_URI)
5. Language painless 선택
6. Type String 선택
7. Format String 선택
8. Script에 아래의 내용 입력

```python
def m = /(?:^|[?&])'추출할 파라메터명'=([^&]*)/.matcher(doc['추출할 필드명'].value);
if ( m.matches() ) {
   return Integer.parseInt(m.group(1))
} else {
   return ""
}

# sample ---------------------------------
def m = /(?:^|[?&])platformType=([^&]*)/.matcher(doc['request.keyword'].value);
if ( m.matches() ) {
   return Integer.parseInt(m.group(1))
} else {
   return ""
}
```

### 참고

 - elastic 공식홈페이지 Kibana 스크립트 필드에서 Painless 사용 - [바로가기](https://www.elastic.co/kr/blog/using-painless-kibana-scripted-fields)

