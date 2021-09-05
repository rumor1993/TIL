[##_Image|kage@dWBajT/btrd15O8xnz/ocaPS72BlHZq6WqWfoZsSK/img.jpg|alignCenter|width="802" height="401" data-origin-width="800" data-origin-height="400" data-ke-mobilestyle="widthOrigin"|||_##]

**모니터링상 에러를 줄여라**

플로우 리뉴얼 서비스가 성공적으로 오픈을 한지 일주일가량 동안 스카우터 모니터링을 확인해보니 Duplication Key Error로 인한 오류가 많이 발생하고 있는 부분을 확인했다. 이를 해결해야하는 업무를 팀장님에게 지시를 받고 이를 해결하고자 그 자리에서 원인과 이를 어떤식으로 해결 할 것인지 이야기를 했다.

원인은 플로우에서는 노드를 이용해 채팅서버를 구성했고 이를 통해 실시간 처리를 진행하고 있는데 동일한 사용자가 디바이스를 동시에 채팅창을 열어두고 읽음처리를 진행하면 디비에 동일한 호출이 들어오면서 Duplication에러가 발생하는 내용이 였다. 

해당부분에서 발생하는 문제점은 불필요한 요청이 여러번 서버에 들어오게 되는 구조를 바꾸는 것도 중요하지만 에러발생률에 따라 에러 알럿을 호출하는 경우가 있어서 에러부터 줄이고 이후에 구조변경 및 로직을 수정하기로 했다.

**Postgresql Upsert**

서버 및 로직을 수정하기에 앞서 Postgre 자체에서 이를 해결하는 방법을 찾아보기로 했다. 이미 로직들에는 해당 데이터가 존재하는지 체크하고 insert를 추가하는 로직이 있었으나 아예 동시에 요청이 들어오면 해당 로직으로 검증이 불가능한다.

Postgresql 공식 문서에 튜토리얼을 확인해보니 Upsert라는 기능이 있었고 이를 사용하면 Duplication 에러가 발생하는 시점에 어떤 동작을 하도록 할건지 정의할 수 있는 기능이 있었다 간단한게 말해 중복에러가 발생하면 어떤 동작을 취해라를 설정할 수 있는 것이다.

Postgresql Upsert 사용법

```
INSERT INTO table_name(column_list) 
VALUES(value_list)
ON CONFLICT target action;
```

-    (column\_name)– 컬럼명
-    ON CONSTRAINT  UNIQUE constraint 값을 사용
-    WHERE predicate– a[WHERE clause](https://www.postgresqltutorial.com/postgresql-where/)with a predicate. 

-    DO NOTHING– Conflict가 발생했을때 아무동작을 하지 않도록 함
-    DO UPDATE SET column\_1 = value\_1, .. 컬럼필드의 값을 변경하도록 처리

해당 사용법에서 운영중인 서비스에 사용할 옵션은 UNIQUE constraint 값과 DO NOTHING을 사용할 예정이다.

이를 통해서 오류가 발생했을시 아무런 동작이 없도록 처리하도록 변경을 했다.

\* 단 해당 Upsert 기능은 Postgresql 9.5 버전 이상부터 사용이 가능하다.

더 자세한 내용이나 사용법 및 예제를 보고 싶다면 아래 링크를 통해 튜토리얼을 확인해보길 바란다.

[https://www.postgresqltutorial.com/postgresql-upsert/](https://www.postgresqltutorial.com/postgresql-upsert/)

[

PostgreSQL Upsert Using INSERT ON CONFLICT statement

Summary: in this tutorial, you will learn how to use PostgreSQL upsert feature to insert or update data if the row that is being inserted already exists in the table. Introduction to the PostgreSQL upsert In relational databases, the term upsert is referr

www.postgresqltutorial.com



](https://www.postgresqltutorial.com/postgresql-upsert/)