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

<br>

### CRUD on a LIST of To-Dos
- 좌측에 카테고리 리스트, 우측에 To-Dos를 배치하여 원하는 카테고리를 누르면 그 카테고리에 속한 To-Do들이 나타나도록 해야한다.
<br>

#### MVC Flow
- **View**: 사용자가 리스트를 클릭할 수 있도록 하고 클릭하면 controller로 클릭한 리스트의 ID를 포함한 정보와 함께 request를 보내야한다.
- **Controller**: **Model**에게 해당 리스트 ID에 속한 to-dos들을 가져오라고 알린다. 모든 item들을 불러오면 View가 업데이트된(선택한 리스트의 id에 속한) to-do item들을 나타내도록 알려준다.

<br>

#### Homepage 수정하기
기존에는 homepage에서 데이터베이스에 있는 모든 todo items이 나열되도록 했었다. 하지만 이제는 특정 list에 속한 item들만을 나타내고자한다.

```python
@app.route('/lists/<list_id>')
def get_list_todos(list_id):
  return render_template('index.html', data = Todo.query.filter_by(list_id=list_id).order_by('id).all())

@app.route('/')   #homepage
def index():
  return redirect(url_for('get_list_todos', list_id=1))
## 홈페이지에서 get_list_todos route handler에 redirect하여 list_id=1에 속하는 todo item들을 나타내도록 한다.
```

<br>

#### 리스트에 새 카테고리 추가하기
- In Terminal
```
# move to your project directory and run python
>>> from app import db, TodoList, Todo
>>> list = TodoList(name='Urgent')       # 새 카테고리 추가
>>> todo = Todo(description='Urgent todo 1')
>>> todo2 = Todo(description='Urgent todo 2')
>>> todo3 = Todo(description='Urgent todo 3')
>>> todo.list = list      # todo와 연관된 parent object를 연결해주어야 하므로 앞에서 선언한 list로 설정한다.
>>> todo2.list = list
>>> todo3.list = list 
>>> db.session.add(list)   # list만 add하면 관련된 children은 자동으로 추가 된다. cascade option의 기본 설정이기 때문이다.
>>> db.session.commit
```
- SQlAlchemy는 parent가 add될때 관련된 children이 함께 자동으로 add될 수 있도록 해준다.
- 또한 테이블 내에서 발생하는 모든 ordering details를 처리해준다.

<br>

#### Update View
좌측에 list, 우측에 todos가 나타나도록 view를 업데이트한다.
```python
# app.py

@app.route('/lists/<list_id>')
def get_list_todos(list_id):
  return render_template('index.html',
  lists=TodoList.query.all(),
  active_list=TodoList.query.get(list_id),    # view의 todos 부분에서 현재 어떤 카테고리인지 알 수 있도록 이름을 표시하기 위함
  todos=Todo.query.filter_by(list_id=list_id).order_by('id').all()
)
```
- 데이터베이스에서 todolists, todos의 데이터를 모두 가져온다.

<br>

<script src="https://gist.github.com/HyunlangBan/46661543ad19416fa6853af0fdb67c68.js"></script>
