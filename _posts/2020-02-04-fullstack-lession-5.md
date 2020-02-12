---
layout: post
title: Bulid a CRUD App with SQLALchemy
tag: fullstack
---

### Introduction
#### CRUD
- **CREATE**
  - `INSERT` in SQL
  - `db.session.add(user1)` in ORM
- **READ**
  - `SELECT` in SQL
  - `User.query.all()` in ORM
- **UPDATE**
  - `UPDATE` in SQL
  - `user1.foo = 'new value'` in ORM
- **DELETE**
  - `DELETE` in SQL
  - `db.session.delete(user1)` in ORM
  
 <br>
 
### Create a Dummy ToDo App
  
- app.py

```python
from flask import Flask, render_template
  
app = Flask(__name___) ## application 생성시 이름이 현재 python 파일과 동일하도록 설정
  
@app.route('/')
  def index():
  return render_template('index.html', ## `index.html`: route의 이름, route handler의 이름과 동일하게 설정
                                       ## render_template: user가 route를 방문할 때마다 HTML파일이 유저에게 render되도록 함
  data = [{'description': 'Todo 1'}, {'description' : 'Todo 2'}, {'description': 'Todo 3'}])
  ## html 파일에서 todo list를 직접 쓰는 것이 아닌 서버를 통해 가져오고 싶으므로 data라는 변수로 list를 생성함
  ## Falsk는 template engine인 Jinja/Jinja2를 통해 data를 HTML templates에서 사용하는 것을 허용
  ## Jinja는 non-html을 HTML 파일 안에서 사용하는 것을 허용한다.
  
if name == '__main__':
  app.run(debug=True)
```
<br>

- /templates/index.html
  - Flask는 모든 템플릿을 현재 프로젝트 디렉토리 내의 templates폴더에서 관리함
  - 데이터를 받아오기위해 jinja 이용
  
```html
<html>
    <head>
        <title>Todo App</title>
    </head>
    <body>
        <ul>
            {% for d in data %} 
            <li>{{ d.description }}</li>
            {% endfor %}
        </ul>
    </body>
</html>
```

<br>

### Result
![todoapp_result](/img/todoapp_result.png)


### Implementing Reads: The "R" in CRUD
이전에는 하드 코딩을 통해서 todo items를 직접 쓰고 받아왔지만 이제는 데이터 베이스에 존재하는 실제 데이터를 받아오도록 한다.(Read)
```python
import flask from Flask, render_template
import flask_sqlalchemy from SQLAlchemy   # db object를 사용하기 위해

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:password@loaclhost:5432/todoapp'  # db와 flask를 연결함. SQLAlchemy는 db를 직접 생성해주지 않기 때문에 postgres의 createdb command line tool 사용
db = SQLAlchemy(app) # create db object

class Todo(db.Model):
  id = db.Column(db.Integer, primary_key=True)
  description = db.Column(db.String(), nullable=False)  # 공통적으로 사용되는 에트리뷰트, 하지만 중복될 가능성이 있으므로(non-unique) 기본키는 될 수 없다.
  
  def __repr__(self):
    return f'<Todo {self.id} {self.description}>' # useful debugging statements when we print these objects
    
db.create_all()  # 위에서 작성한 테이블이 만들어지도록

@app.route('/')
  def index():
    return render_template('index.html', data = Todo.query.all()) # data --> todoapp db의 Todo 테이블에서 가져옴
    
if __name__ == '__main__':
  app.run(debug=True)
```

데이터베이스상의 데이터에 변화가 발생하면 사용자의 veiw에도 동일한 변화가 발생한다 --> MVC pattern 

#### Reset database anytime
```
dropdb todoapp && createdb todoapp
```
  
