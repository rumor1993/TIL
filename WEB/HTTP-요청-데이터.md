# HTTP 요청 데이터

HTTP 요청을 통해서 클라이언트에서 서버로 전달하는 방법

## GET - 쿼리 파라미터

- url?name=kim
- URL의 쿼리 파라미터에 데이터를 포함하는 방식
- 길이에 제한이 있고 보안적인 데이터를 넘기는 경우 사용하기 힘듬

## POST - HTML Form

- Content-Type: application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파라미터 형식으로 전달 url?name=kim
- 길이에 제한이 없고 URL에 데이터가 유출되지 않음

## HTTP message body

- HTTP API에서 주로 사용함 (JSON, XML, TEXT)
- 데이터 형식은 주로 JSON 객체 사용
- GET, POST, PUT, PATCH
