---
layout: post
tag: fullstack
title: One-to-Many, Many-to-Many Relationships
---

### One-to-Many Relationship Setup
이제까지 만들어온 Todo app에 To-Do Lists를 추가하고 To-do model과 새로운 To-Do List model간에 relationship을 설정해본다.
- To-Do List는 많은 To-Dos를 가질 수 있고, 각 To-Do는 하나의 To-Do List에 속하므로 두 모델 간에는 **one to many relationship**이 존재한다.
  - TodoList: Parent
  - Todo: Child
<br>

#### Creating the `TodoList` model and adding the foreign key to child `Todo` model
```python
class TodoList(db.Model):
  __tablename__='todolists'
  id = db.Column(db.Integer, primary_key=True)
  name = db.Column(db.String(), nullable=False)
  todos = db.relationship('Todo', backref='list', lazy=True)

class Todo(db.Model):
  __tablename__ = 'todos'
  id = db.Column(db.Integer, primary_key=True)
  description = db.Column(db.String(), nullable=False)
  completed = db.Column(db.Boolean, default=False)
  list_id = db.Column(db.Integer, db.ForeignKey('todolists.id'), nullable=False)
```
<br>

#### Create and run a migration to upgrade the schema
```
C:\Users\gusfk\Desktop\class-demos\todoapp>flask db migrate
C:\ProgramData\Anaconda3\lib\site-packages\flask_sqlalchemy\__init__.py:835: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
  'SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and '
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'todolists'
INFO  [alembic.ddl.postgresql] Detected sequence named 'todos_id_seq' as owned by integer column 'todos(id)', assuming SERIAL and omitting
INFO  [alembic.autogenerate.compare] Detected added column 'todos.list_id'
INFO  [alembic.autogenerate.compare] Detected added foreign key (list_id)(id) on table todos
Generating C:\Users\gusfk\Desktop\class-demos\todoapp\migrations\versions\dde729cffcaf_.py ...  done
```
- `flask db migrage`를 하게 되면 새로운 테이블인 `todolists`와 `todos`에 새로 추가한 foreign key 컬럼을 발견한다.
- migration file을 확인 한 후 `flask db upgrade`를 하게되면 NotNullViolation error가 발생한다.
  - 이유는 기존의 data에서는 추가될 새 컬럼(nullable=False)인 `list_id`의 값이 null이기 때문
<br>

#### NotNullViolation Error 해결방법
1. 생성된 migration 파일에 가서 `list_id`의 컬럼 속성을 `nullable=False`에서 `nullable=True`로 바꾼다.
2. `app.py`에서도 마찬가지로 `nullable=True`로 변경한다.
3. `flask db upgrade`를 해주면 변경된 migration script가 성공적으로 실행된다.
4. 생성된 `todolists` 테이블에서 `todos`의 `list_id`가 참조할 값들을 생성한다.
  - (e.g. `INSERT INTO todolists (name) VALUES ('Uncategorized');`)
5. `todos` 테이블에서 null값이 들어간 `list_id`를 해당되는 값으로 업데이트 해준다.
  - (e.g. `UPDATE todos SET list_id = 1 WHERE list_id IS NULL;`)
6. `app.py`로 가서 `list_id`의 `nullable`을 다시 `False`로 설정한다.
7. `flask db migrate`를 하면 해당 변화를 발견하고 migration이 생성되고, `flask db upgrade`를 해준다.

