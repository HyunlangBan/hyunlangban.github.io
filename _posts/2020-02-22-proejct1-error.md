---
layout: post
title: Project1 오류 & 부족한 개념
tag: fullstack
---

### Errors
---
### Postgresql에서 릴레이션을 찾을 수 없을 때
#### 문제상황
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
 스키마 문제인줄 알았는데 설정을 아무리 바꿔도 안나왔다.
 
 #### 해결
 오류가 났던 이유는 바로.. 테이블 이름에 대문자가 있어서였다. 대문자가 있으면 **쌍따옴표 안**에 넣어서 구분을 해주어야한다.
 `\d "Artist"`로 입력했더니 넣어준 칼럼들이 잘 나온다.


<br>
<br>

### 프로젝트 진행중에 파이썬 인터프리터에서 쿼리를 테스트하고 싶을때
#### 문제상황
수업에서 했던 예제에서는 프로젝트 폴더로 이동 후 파이썬 인터프리터에서 `from app import db, model_name`를 입력하고 쿼리문을 바로 입력했을 때 결과가 나왔었다. 그래서 이번에도 테스트를 위해 `from app db, Show`를 하고 `Show.query.all()`과 같은 쿼리문을 입력하니 다음과 같은 오류가 나왔다.
```
>>> Show.query.all()
Traceback (most recent call last):
  File "C:\ProgramData\Anaconda3\lib\site-packages\sqlalchemy\util\_collections.py", line 1010, in __call__
    return self.registry[key]
KeyError: <greenlet.greenlet object at 0x0000021F8DE02438>

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\ProgramData\Anaconda3\lib\site-packages\flask_sqlalchemy\__init__.py", line 519, in __get__
    return type.query_class(mapper, session=self.sa.session())
  File "C:\ProgramData\Anaconda3\lib\site-packages\sqlalchemy\orm\scoping.py", line 78, in __call__
    return self.registry()
  File "C:\ProgramData\Anaconda3\lib\site-packages\sqlalchemy\util\_collections.py", line 1012, in __call__
    return self.registry.setdefault(key, self.createfunc())
  File "C:\ProgramData\Anaconda3\lib\site-packages\sqlalchemy\orm\session.py", line 3213, in __call__
    return self.class_(**local_kw)
  File "C:\ProgramData\Anaconda3\lib\site-packages\flask_sqlalchemy\__init__.py", line 136, in __init__
    self.app = app = db.get_app()
  File "C:\ProgramData\Anaconda3\lib\site-packages\flask_sqlalchemy\__init__.py", line 982, in get_app
    'No application found. Either work inside a view function or push'
RuntimeError: No application found. Either work inside a view function or push an application context. See http://flask-sqlalchemy.pocoo.org/contexts/.
```
그 이유는 이 프로젝트에서는 데이터베이스 연결을 config.py에서 따로 해주는데 이것때문에 프롬프트에서는 데이터베이스와 연결이 되지 않는 것이었다.

#### 해결
파이썬 인터프리터에서 쿼리문을 사용하기 위해서는 context를 부여해주어야 한다고 한다. 그렇게 하기 위해서는 파이썬 인터프리터에서 `with app.app_context():`를 사용해야한다.
```
from app import app, db, Show

with app.app_context():
   Show.query.all()
```
주의할 점은 tab을 사용하면 바로 오류가나기 때문에 띄어쓰기 3번을 해야한다. 

<br>

### 부족한 개념
---
#### Association table in many-to-many relationships
수업에서 many-to-many 관계에서는 두개의 one-to-many relationship이 생긴다고 했었고 중간에 있는 table은 association table로 `db.Model`이 아닌 `db.Table`로 만들어주는 것으로 알고있었다.

하지만 학생들이 남긴 질문들과 답변들을 보니 다른 사람들은 Show를 클래스(`db.Model`)로 선언하여 만들어주고 있었다. 나는 이부분에서 혼란스러웠다. 그럼 Show는 association table이 아닌걸까? 분명히 many-to-many relationship인데 내가 생각을 잘못한걸까하고 계속 고민했다.

처음에는 내가 관계를 잘못 생각한거고 Show는 독립적인 테이블이라고 생각했었는데 아무리 생각해도 Show가 중간에 안낄수가 없었다. 그럼 왜 association table을 처음에는 table로 선언하라고 알려주고 프로젝트에서는 model로 선언한걸까 이해가 되지 않았다.

결론은 association table이라고 `db.Table`로만 만들 수 있는게 아니고 `db.Table`, `db.Model` 둘다 사용 가능하다. 
`db.Table`을 사용하는 것이 좀 더 간단할 뿐이다. `db.Model`로 선언하게 되면 `db.Table`로 선언했을때보다 추가적인 정보들이 담기게되는데 그러한 정보가 필요하다면 `db.Model`로 선언한다. 

참고자료: [db.Model vs db.Table in Flask-SQLAlchemy](https://stackoverflow.com/questions/45044926/db-model-vs-db-table-in-flask-sqlalchemy)
