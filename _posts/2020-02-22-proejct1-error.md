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
#### one to many relationship: 왜 `Show`는 association table이 아닐까?
- `Artist`와 `Venue`는 many to many relationship이다. 수업에서는 many-to-many relationship에서는 두개의 one-to-many relationships를 만들고 중간을 연결해주는 association table이 필요하다고 했다. 근데 왜 `Artist`와 `Venue`를 연결해주는 `Show`는 association table로 만들지 않고 새로 model을 만들어주는걸까?


#### Mandatory, Optional Relationship
답은 그 관계가 반드시 발생하는 mandatory인지 아니면 관계가 없을 수도 있는 optional인지에 따라 달라지는 것이었다.
- many-to-many relationships에서 둘 중 하나가 mandatory라면 join을 했을때 null값이 발생하지 않는다.
  - 예를 들어 주문을 할 때는 무조건 한개 이상의 물건이 담겨야한다. 하지만 물건들 중 모두가 주문이 되야할 필요는 없으며 또한 여러 주문에 담길 수도 있다.
  - 이 관계에서 join을 하게되면 order_id에 따라 해당되는 product들이 연결될 것이다.
- 하지만 모든 관계가 optional이라면?
  - artist는 한개 이상의 venue를 구할수도 있고 안구할 수도 있다 또한 venue도 한팀 이상의 artist를 구할수도 있고 안구할수도 있다.
  - 이런 상황에서 우리가 한 엔티티의 id를 다른 엔티티의 릴레이션에 저장한다면, 특정 엔티티의 특정 인스턴스에 대한 관계가 존재하지 않기 때문에 id에 대해서 null 값을 가지는 경우가 있다는 것을 의미한다. 하지만 id는 null값을 가질 수 없으므로 **두 엔티티의 관계를 나타내는 세번째 릴레이션**이 따로 필요하다. 그 릴레이션이 바로 `Show`가 되는 것이다.
- [참고: Entity-Relationship Modelling](https://www.cs.uct.ac.za/mit_notes/database/htmls/chp06.html)
 
