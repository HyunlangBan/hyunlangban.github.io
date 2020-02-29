---
layout: post
title: Project1 오류 & 부족한 개념
tag: fullstack
---

### Proejct1 도중 생긴 오류 정리
#### 1. Postgresql에서 릴레이션을 찾을 수 없을 때
분명 `\dt`를 했을 때
```
project1=# \dt
              List of relations
 Schema |      Name       | Type  |  Owner
--------+-----------------+-------+----------
 public | Artist          | table | postgres
 public | Venue           | table | postgres
 public | alembic_version | table | postgres
 ```
 와 같이 나오는데 컬럼들이 잘 들어갔는지 확인하려고 `\d Artist`를 아무리해도 '"Artist" 이름을 릴레이션(relation) 없음.'와 같은 오류만 나오는 것이다.
 스키마 문제인줄 알았는데 설정을 아무리 바꿔도 안나왔다. <br><br>
 오류가 났던 이유는 바로.. 테이블 이름에 대문자가 있어서였다. 대문자가 있으면 **쌍따옴표 안**에 넣어서 구분을 해주어야한다.
 `\d "Artist"`로 입력했더니 넣어준 칼럼들이 잘 나온다.
 
<br>

### 부족한 개념

#### Association table in many-to-many relationships
수업에서 many-to-many 관계에서는 두개의 one-to-many relationship이 생긴다고 했었고 중간에 있는 table은 association table로 `db.Model`이 아닌 `db.Table`로 만들어주는 것으로 알고있었다.

하지만 학생들이 남긴 질문들과 답변들을 보니 다른 사람들은 Show를 클래스(`db.Model`)로 선언하여 만들어주고 있었다. 나는 이부분에서 혼란스러웠다. 그럼 Show는 association table이 아닌걸까? 분명히 many-to-many relationship인데 내가 생각을 잘못한걸까하고 계속 고민했다.

처음에는 내가 관계를 잘못 생각한거고 Show는 독립적인 테이블이라고 생각했었는데 아무리 생각해도 Show가 중간에 안낄수가 없었다. 그럼 왜 association table을 처음에는 table로 선언하라고 알려주고 프로젝트에서는 model로 선언한걸까 이해가 되지 않았다.

결론은 association table이라고 `db.Table`로만 만들 수 있는게 아니고 `db.Table`, `db.Model` 둘다 사용 가능하다. 
`db.Table`을 사용하는 것이 좀 더 간단할 뿐이다. `db.Model`로 선언하게 되면 `db.Table`로 선언했을때보다 추가적인 정보들이 담기게되는데 그러한 정보가 필요하다면 `db.Model`로 선언한다. 

참고자료: [db.Model vs db.Table in Flask-SQLAlchemy](https://stackoverflow.com/questions/45044926/db-model-vs-db-table-in-flask-sqlalchemy)
