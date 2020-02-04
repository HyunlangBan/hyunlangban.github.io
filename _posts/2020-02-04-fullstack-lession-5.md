---
layout: post
title: Bulid a CRUD App with SQLALchemy
tag: [fullstack, udacity]
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

- /templates/index.html
  - Flask는 모든 템플릿을 현재 프로젝트 디렉토리 내의 templates폴더에서 관리함
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

### Result
![todoapp_result](/img/todoapp_result.png)
