---
layout: post
title: Project1 오류 정리
tag: fullstack
---

### Proejct1 도중 생긴 오류 정리
#### Postgresql에서 릴레이션을 찾을 수 없을 때
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
 
