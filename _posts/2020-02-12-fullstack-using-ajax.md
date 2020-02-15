---
layout: post
title: Using AJAX to send data to flask
tag: fullstack
---

### Using AJAX to send data to flask
- Data request는 동기(synchronous)이거나 비동기(asynchronous)이다.
- Async data requests는 서버에게 보내졌다가 페이지 새로고침 없이 클라이언트에게 되돌아 온다.
- Async requests(AJAX requests)는 다음 두 메소드 중 하나를 사용한다.
  - XMLHttpRequest
  - **Fetch (modern way)**
- [더 자세한 내용 참고](https://coding-factory.tistory.com/143)

### Using `XMLHttpRequest`
#### Send a Request To a Server
{% highlight javascript %}
var xhttp = new XMLHttpRequest();  // #1
description = document.getElementById("description").value;  // #2
xhttp.open("GET", "/todos/create?description="+description);  // #3
xhttp.send();  // #4
{% endhighlight %}
>- The XMLHttpRequest object can be used to exchange data with a web server behind the scenes. 
>- open(method, url, async):	Specifies the type of request
>  - method: the type of request: GET or POST
>  - url: the server (file) location
>  - async: true (asynchronous) or false (synchronous)
>- send():	Sends the request to the server (used for GET)
>- send(string):	Sends the request to the server (used for POST)

<br>

- #1. XMLHttpRequest 객체 생성 <br>
- #2. id가 description인 form에 입력된 value값을 description에 할당 <br>
   (DOM에서 request와 함께 보낼 data를 가져온다.) <br>
- #3. open a connection from client to server, specified where you're looking to send. <br>
- #4. 서버에 해당 요청을 전송하고 connection을 종료 <br>

<br>

서버에서 요청 처리가 끝나면..<br>
- 동기: the server dictates how the view should then uptake. <br>
- 비동기: It's on the client side that you reacts to the server and you figure out how to update the DOM that is already loaded on the client based on the response that you get.

#### Server Response - XMLHttpRequest on success
{% highlight javascript %}
xhttp.onreadystagechange = function() {
  if (this.readyState === 4 && this.status === 200) {
  // on successful response
  console.log(xhttp.responseText);
  }
};
{% endhighlight %}
>The `readyState` property holds the status of the `XMLHttpRequest`. <br>
>The `onreadystatechange` property defines a function to be executed when the `readyState` changes. <br>
>The `status` property and the `statusText` property holds the status of the `XMLHttpRequest` object. <br>
>The `onreadystatechange` function is called every time the `readyState` changes. <br>
>**When `readyState` is 4 and status is 200, the response is ready.**


### Using `Fetch`
{% highlight javascript %}
fetch('/my/request', {
  method: 'POST',
  body: JSON.stringity({
    'description': 'some description here'
  }),
  headers: {
    'Content-Type': 'application/json'    // To server knows to parse it as JSON
  }
});
{% endhighlight %}
- `fetch`는 request를 더 쉽게 보낼 수 있도록 해준다.
- request와 관련된 headers, body, method와 같은 파라메터를 가진다.
- `fetch(<url-route>, <object of request parametsers>)`

#### todoapp
- View
<script src="https://gist.github.com/HyunlangBan/3d603248ba7172acfdd8849a8c243c6d.js"></script>

- Controller
<script src="https://gist.github.com/HyunlangBan/47a67de754ae0f73ed3eb02b2a56f12a.js"></script>

- 비동기였을 때는 페이지를 redirect하면서 전체가 새로고침이 되어야했는데 동기에서는 새로운 부분(response)만 기존의 화면에서 추가되는 것을 볼 수 있다.
- 또한 이전에는 컨트롤러가 response를 받아서 view를 결정했지만 여기서는 client side(view)에서 request를 전송하고 response를 받아서 화면에 출력하는 것까지 결정한다. 컨트롤러는 model의 수행만 명령하고 JSON data를 client로 return한다.

<br>

### Using sessions in controllers
- Commits은 성공할수도 있고 실패할 수도 있다. 우리는 연결을 종료할 때 potential implicit commits가 발생하는 것을 방지하기 위해서 rollback을 수행해야 한다. 
  - connection을 닫을 때 의도하지 않았더라도 데이터베이스에 pending된 transactions이 commit되기 때문이다.
- 가장 좋은 방법은 컨트롤러에서 사용되는 모든 새션의 끝에서 connections을 종료하여 그 connection이 connection pool로 돌아가서 다시 사용될 수 있도록 하는 것이다.

#### Example
```python
import sys

try:
  todo = Todo(description=description)
  db.session.add(todo)
  db.session.commit()
except:
  db.session.rollback()
  error=True
  print(sys.exc_info())
finally:
  db.session.close()
```

<br>

### Implement `try`...`except`...`finally` in the app
```python
from flask import abort
import sys
...
@app.route('/todos/create', methods=['POST'])
def create_todo():
  error = False
  try:
    description = request.get_json()['description']
    todo = Todo(description=description)
    db.session.add(todo)
    db.session.commit()
  except:
    error = True
    eb.session.rollback()
    print(sys.esc_info())         # 구체적인 error의 내용을 나타낸다.
  finally:
    db.session.close()
  if not error:
    return jsonify({
      'description': todo.description
    })
...
```
> expire_on_commit: Defaults to `True`. When `True`, all instances will be fully expired after each `commit()`, so that all attribute/object access subsequent to a completed transaction will load from the most recent database state.

db에 commit후 종료된 세션에 있던 instance들은 모두 expired되었기 때문에 SQLAlchemy는 위와 같은 error를 발생시킨다.
`db = SQLAlchemy(app, session_options={"expire_on_commit": False})`로 변경하면 에러를 없앨 수 있지만 의도하지 않은 부작용이 생길 수 있기 때문에 사용하지 않을 것이다.

#### Let's remove the error
```python
...
@app.route('/todos/create', methods=['POST'])
def create_todo():
  error = False
  body = {}
  try:
    description = request.get_json()['description']
    todo = Todo(description=description)
    db.session.add(todo)
    db.session.commit()
    body['description'] = todo.description        ### 해결방법: session을 종료하기전에 body를 return해두기
  except:
    error = True
    eb.session.rollback()
    print(sys.esc_info())   
  finally:
    db.session.close()
  if not error:
    return jsonify({body})
...
```
