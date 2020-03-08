---
layout: post
tag: fullstack
title: Endpoints and Payloads
---

### Organizing API Endpoints

API endpoints를 구성할 때, actions이 아닌 resources에 기반해야한다. 주어진 URL endpoint에서 어떤 actions이 발생할지는 request methods가 결정한다. 전체적인 API의 scheme은 일관적이고 명확하고 정확해야한다.

#### Principles
- 직관적이어야 한다.
- 리소스에 의한 구성이어야한다.
  - path에 동사가 아닌 명사가 와야한다.
  - 어떤 operation이 작동할지는 사용된 method가 결정 할 것이다.
  - GOOD: `https://example.com/posts`
  - BAD: `https://example.com/get_posts`
- 일관적인 scheme을 유지해야한다.
  - 집합에서는 복수형 명사를 사용한다.
  - 특정 아이템을 명시하려면 파라메터를 사용한다.
  - GOOD: `https://example.com/entrees`, `https://example.com/entrees/5`
  - BAD: `https://example.com/entree`, 
  `https://example.com/entree_five`
- 너무 복잡하거나 길게 만들지 않는다.
  - No longer than `collection/item/collection`
  - GOOD: `https://example.com/entrees/5/reviews`
  - BAD: `https://example.com/entrees/5/customers/4/reviews`

#### Methods& Endpoints Review
주어진 자원의 URI에서 수행되는 오퍼레이션은 사용된 request method에 따라 결정된다고 하였다. API 문서는 수행되는 오퍼레이션과 response를 통해 return되는 데이터를 정확하게 설명해야 하지만 API를 사용하는 모두에게 직관적이어야 한다.

|Resources|GET|POST|PATCH|DELETE|
|:-------:|:--:|:--:|:---:|:----:|
/tasks|Get all tasks|Create a new task|Partial update of all tasks| Delete all tasks
/tasks/1|Get the details of task 1|Error!- already exist|Partial update of task 1|Delete task 1
\tasks\1\notes|Get all the notes for task 1|Create a new note for task 1|Partial update of all notes of task 1|Delete all notes of task 1

<br>

### CORS(Cross-Origin Resource Sharing)
Same-origin policy는 웹페이지 1의 스크립트가 동일한 도메인을 공유하는 경우에만 웹페이지 2의 데이터에 엑세스할 수 있도록 하는 웹 보안 개념이다. 

이에 따라 다음과 같은 경우에서 에러가 발생한다.
- Different domains
- Different subdomains(`example.com` and `api.example.com`)
- Different ports(`example.com` and `example.com:1234`)
- Different protocols(`http://example.com` and `https://example.com`)

하지만 이것들은 진짜 오류는 아니다. 당신과 당신의 사용자들을 해야할 일을 하고 있을 뿐이다. 만약 attacker가 악성 스크립트를 광고에 포함시켰다면, 광고를 호스팅하는 웹사이트에 엑세스할 때 해당 스크립트가 은행 웹사이트에 성공적으로 요청을 보내지 못하도록 해준다.

우리는 같은 origin을 갖고 있지 않는 API들을 사용해야 할 때가 있다. 이것을 가능하게 하기 위해서는, 우리가 non-trival request를 가지고 있을 때 preflight OPTION request가 보내지고 그 OPTION request는 'Do I have permission to make this request?', 'Have access to the resource at all?', 'Have access to the resource for the method that I'm using?'과 같은 조사를 진행한다. Option request가 status code 100, contiune로 반환 되면 허가가 승인된 것이다. CORS가 없다면 실제 request는 절대로 전송될 수 없다.


#### CORS headers
request가 제대로 처리되기 위해서 CORS는 서버가 허용할 것을 명시하는 headers를 이용한다.

- Access-Control-Allow-Origin
  - 어떤 clinet domain들이 자원에 접근할 수 있는가? 모든 도메인이라면 *을 사용
- Access-Control-Allow-Credentials
  - 인증에 쿠키를 사용하는 경우에만 - 해당 값은 무조건 참이어야함
- Access-Control-Allow-Methods
  - 허용되는 http methods의 목록
- Access-Control-Allow-Headers
  - 서버가 허용하는 http 요청 헤더 값 목록(특히 사용자 지정 헤더를 사용할 경우 유용)

<br>

### Flask-CORS
Flask-CORS는 CORS를 다루기 위한 extension이다.

#### Sample Code
```python
from flask import Flask
from flask_cors import CORS

def create_app(test_config=None):
    app = Flask(__name__)
#   CORS(app) -- initialize Flask-CORS all default options.
    cors = CORS(app, resources={r"/api/*": {"origins":"*"}})  #1

    # CORS Headers
    @app.after_request
    def after_request(response):
        response.headers.add('Access-Control-Allow-Headers', 'Content-Type, Authorizaion, true')
        response.headers.add('Access-Control-Allow-Methods', 'GET, PATCH, POST, DELETE, OPTIONS')
        return response  #3

    @app.route('/messages')     #2
    @cross_origin()
    def get_messages():
        return 'GETTING MESSAGES'
```
- #1. Resource-Specific Usage: 특정 도메인에서만 자원에 접근하도록 하고 싶은 경우나 특정 route에서 cross-origin이 가능하게 하도록 할 때 사용한다. key에는 해당되는 resources를 명시하고 value에는 어떤 origin이 그러한 자원에 접근할 수 있는지를 명시한다. 

- #2. Route-Specific Usage: 특정 endpoint에서만 CORS를 활성화 하고 싶은 경우 `@cross_origin()`을 사용할 수 있다.

- #3. response는 preflight option request에 보내져 client가 백엔드에 full request를 보내기 위해 필요한 정보를 얻게 된다.

<br>

### Flask Route Decorator
`@app.route` decorator는 Variable Rules와 multiple HTTP methods를 처리할 수 있다.

#### Variable Rules
Flask에서 variability를 처리하기 위해 `@app.route` decorator의 path argument안에 `<variable_name>`을 추가하면 이것은 variable_name으로 function의 keyword argument가 된다.

또한 argument의 타입을 `<converter:variable_name>`으로 지정해 줄 수 있다.
```
@app.route('/entrees/<int:entree_id>')
def retreive_entree(entree_id):
    return 'Entree %d' % endtree_id
```

#### HTTP Methods
기본적으로 `@app.route` decorator는 오직 GET requests에만 응답한다. 더 많은 request types를 사용하기 위해서는 method 파라메터가 필요하다.
```
@app.route('/hello', methods=['GET', 'POST'])
def greeting():
    if request.method == 'POST':
        return create_greeting()
    else:
        return send_greeting()
```

OPTIONS requests는 자동으로 실행되고 HEAD 또한 GET이 존재하면 자동으로 실행된다.

<br>

### Pagination in Flask
대용량 데이터를 처리할 때, 모든 데이터를 프런트엔드로 보내려고 하면 응답 및 클라이언트 렌더링 속도가 느려진다.

이 문제를 처리하는 일반적인 방법은 전송하는 데이터를 페이지화하여 덩어리로 보내는 것이다. 위에서 논의한 변수와 마찬가지로 플라스크는 페이지나 검색어와 같은 추가 요청 조건을 얻기 위한 요청 인수를 처리할 수 있다.

#### Query Parameters
```
www.example.com/entrees?page=1
www.example.com/entrees?page=1&allergens=peanut
```

#### Request Arguments
플라스크에서, query parameters로 request가 수신된 경우 `@app.route` decorator의 route는 동일하고 request object arguments는 파라메터를 포함한다.

`request.args`는 Python 딕셔너리이므로 `get` 메소드를 이용해서 value에 접근할 수 있다. 또한 default vaule를 제공할 수 있다.(예시에서는 1이다.)
```
@app.route('/entrees', methods=['GET'])
    def get_entrees():
        page = request.args.get('page', 1, type=int)
```
<br>

### Flask Error Handling
`abort()` 메소드의 기본 response는 cilent나 사용자들에게 적합하지 않다.
또한 우리는 모든 우리의 서버 response들이 일관된 포멧을 갖도록 하고 오류와 관련하여 client에게 적절한 정보를 제공하기를 원한다.

`@app.errorhandler` decorator를 사용하면 예상되는 오류에 대한 동작을 지정할 수 있다. 이 decorator를 사용할 때 다음과 같은 점들을 고려해야햔다.
- Decorator의 argument로 status code나 Python error가 들어가야한다.
- Logical naming of the function handler
- JSON object response의 일관된 포맷과 메시징
```python
@app.errorhandler(404)
def not_found(error):
    return jsonify({
        "success": False, 
        "error": 404,
        "message": "Not found"
        }), 404
```
