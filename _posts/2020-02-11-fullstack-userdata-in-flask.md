---
layout: post
title: Getting User Data in Flask
tag: [fullstack]
---

### Getting User Data in Flask 
View에서 Controller로 user data를 가져오는 방법은 3가지이다.
- URL query parameters
- Forms
- JSON

#### URL query parameters
- URL query parameters는 key-value의 짝이 URL과 물음표 뒤에 붙는다.
   - `www.example.com/foo?field1=value1`
- Flask는 `Requests` object에 있는 `args` object를 제공한다.
   - `value1 = request.args.get('field1')`
- URL query parameters는 빠르게 정보를 가져오는 방법 중 하나이지만 받아오는 정보의 양이 많을 때는 response body를 쓰는 것이 더 합리적이다.
#### Form data
- `request.form.get('<name>')`은 HTML에서 `name` attribute에 따라서 form에 들어간 값들을 읽어온다.
  - `username = request.form.get('username')`

#### Note: defaults
- `request.args.get`, `request.form.get`은 모두 optional second parameter를 가질 수 있다.
  - `request.args.get('foo', 'my default')`는 result가 비어있을 때 기본 값으로 `my default`를 가진다.

#### JSON
- 위의 방법보다 더 현대적인 방법은 JSON의 response body를 사용하는 것이다.
- `request.data`는 JSON을 string으로 가져온다(retrieves). 그리고 `json.loads`를 호출해서 `request.data` string을 파이썬의 lists나 dictionaries로 변환한다.
- data type이 `application/json`일때
   ```
   data_string = request.data
   data_dictionary = json.loads(data_string)
   ```

<br>

### Using HTML form submission to get the data
#### Example
```
<form action="/create-todo" method="post">
  <div>
    <label for="name">Create a To-Do Item</label>
    <input type="text" id="description" name="description">
  </div>
  <div>
    <input type="submit" id="submit" value="Create" />
  </div>
</form>
```
- forms는 서버에 데이터를 전송하기 위해 `action`(route의 이름)과 `method`(route method) 갖는다.
  - route method에는 post, get이 있다.
- form control element의 이름 에트리뷰트는 `request.get(<key>)`에서 데이터를 가져올 때 key로 사용된다.
- 모든 forms는 submit button을 정의하거나 사용자가 ENTER를 눌렀을 때 데이터가 submit 되도록 한다.

<br>

### Form Methods `POST` vs `GET`
#### On a POST method
```
<form action="/create" method="post">
   <div>
     <label for="field1">Field 1</label>
     <input type="text" name="field1">
   </div>
   <div>
     <label for="field2">Field 2</label>
     <input type="text" name="field2">
   </div>
   <div>
     <input type="submit" id="submit" value="Create" />
   </div>
 </form>
```
- Submit 버튼을 누르면 HTTP POST request를 `/create` route로 request body와 함께 전송한다.
  - Content-Type: application/x-www-form-urlencoded
  - Request Body: `field1=value1&field2=value2`
    - key-value 짝들이 &으로 붙게된다. Flask는 이 값들을 구분할 수 있으며 name attribute에 맞는 value를 돌려준다.--> `request.form.get('username')`
- POSTs는 더 긴 form의 제출에 적합하다. URL query parameters는 request body에 비해 더 길어질 수 있기 때문이다(max 2048 characters).
- forms은 POST와 GET request만으로 전송될 수 있다.
 
 #### On a GET method
 ```
 <form action="/create" method="get">
 ....
 ```
 - GET request는 URL뒤에 form의 데이터가 합쳐진 URL parameters로 전송된다.
   - `/create?field1=value&field2=value2`
   - URL parameters에서 데이터를 얻는 방법: `request.args.get('field')`
 - smaller form submission에 더 이상적이다.

<br>

### In Flask
POST 메소드를 사용하여 `/create` route로 데이터를 전송할 때 Flask에서는 어떻게 처리해아할까?
```
@app.route('/create', method=['POST']  # route handler
def create():
  value1 = request.form.get('field1')  # 두 방법 모두 값을 받아올 수 있음
  value2 = request.from['field2']
  
  // do something with the user data ... # 받아온 값을 데이터 베이스에 추가해야함
  
  return render_template('index.html') # 결과를 나타낼 view
```

#### Developing our view
이전에 만들었던 todo app이 사용자 값을 받아서 데이터베이스에 레코드를 추가할 수 있도록 변경해보자.
```html
<html>
   <head>
      <title>Todo App</title>
   </head>
   <body>
   <!-- 추가된 내용 -->  
      <form method="post" action="/todos/craete">  <!-- action은 보통 '/resource의 이름/수행할 action'으로 짓는다 -->
         <input type="text" name="description" />
         <input type="sumbit" value="Create" />
      </form>
   <!----------------->
      <ul>
         {% for d in data %}
         <li>{{ d.description }}</li>
         {% endfor %}
      </ul>
   </body>
<html>
```

#### Developing the controller
```python
# app.py

from flask import Flask, render_template, request, redirect, url_for
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__) 
app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://postgres:gus123@localhost:5432/todoapp'
db = SQLAlchemy(app) 

class Todo(db.Model):
    __tablename__ = 'todos'
    id = db.Column(db.Integer, primary_key=True)
    description = db.Column(db.String(), nullable=False)

    def __repr__(self):
        return f'<Todo {self.id} {self.description}>'

db.create_all()

#--------------------------------------------------------
@app.route('/todos/create', methods=['POST'])
def create_todo():
    description = request.form.get('description', '')
    todo = Todo(description=description)
    db.session.add(todo)
    db.session.commit()
    return redirect(url_for('index')) # index route handler
    
# '/todos/create' route로 description의 value를 받아온다.
# 위에서 선언한 Todo의 description에 해당 값을 추가한다.
# commit으로 데이터베이스에 반영시킨다.
# 새롭게 바뀐 index 페이지를 redirect한다.
#--------------------------------------------------------

@app.route('/')
def index():
    return render_template('index.html', data = Todo.query.all())


if __name__ == '__main__':
    app.run(debug=True)
```

<br>

View에 내용을 입력하면 Controller가 값을 받아서 Model을 조정하고 그 결과를 view가 반영할 수 있도록 한다.**(MVC flow)**
