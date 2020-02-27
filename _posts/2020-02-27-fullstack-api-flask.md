---
layout: post
title: HTTP and Flask Basics
tag: fullstack
---
### Introduction to HTTP
Hypertext Transfer Protocol(HTTP)는 컴퓨터들이 서로 커뮤니케이션 할 수 있도록 표준화된 방법을 제공하는 프로토콜이다. 이것은 1990년부터 인터넷을 통한 
데이터 커뮤니케이션의 기초가 되어왔고 clinet-server communication이 어떻게 작동하는지 이해하는데에 필수적이다.

우리가 흔히 보는 웹사이트 url을 보면 앞에 http, https가 붙어있는데 이것은 사용하는 프로토콜을 나타낸다.

### Features
- Connectionless: Request가 전송되면 client는 connection을 연다. response를 받으면 clinet는 connection을 닫는다. client와 server는 
오직 response와 request 동안에만 connection을 유지한다. 나중의 response들은 새로운 connection에서 발생한다. 이렇게 하는 이유는 connection이
 열리면 client의 client와 server의 ports가 열리고 occupied된다. 우리는 ports가 실제로 사용되지 않을 때는 occupied된 채로 두고 싶지 않다. 그렇기때문에 
 ports들이 request와 response가 진행되는 connection동안에만 열리도록 하는 것이다.
- Stateless: 연속되는 requests간에 의존성이 없다. request의 성공은 그 request가 제대로 포맷되어있는지 그리고 정확한 데이터를 가지고 있는지에 달려있다.
 이것은 debug를 훨씬 더 쉽게 만들어주는데, 만약 request가 실패하면 이전의 request를 볼 필요 없이 하나만 보면 되기 때문이다.
- NOT Sessionless: Header, cookies, caching을 이용하여 각각의 HTTP request가 같은 context를 공유할 수 있도록 sessions이 만들어질 수 있다. 이것은 효율성을 위해 매우 중요하다.
- Media Indepentdent: 어떠한 타입의 데이터이든 client와 server 모두 그 데이터 포맷을 어떻게 다루는지 알고 있는 한 HTTP를 통해 전송될 수 있다. 
그래서 전송되는 정보의 content type이 무엇인지 구분할 수 있는 message가 보내져야 한다.(우리의 경우에는 JSON을 이용할 것이다.)


### Elements
- Universal Resource Identifiers(URIs): URI는 보통 URL이나 주소이다. 이것들은 접근하고자 하는 자원을 구체적으로 가르킨다. 
우리가 주소를 통해 웹사이트에 접속할 수 있는 것처럼 API도 원하는 모든 데이터들이 구체적인 주소를 가지고 있을 것이다.
  - e.g. `http://www.example.com/tasks/term=homework`
  - Scheme: 자원에 접근하기 위해 사용되는 protocol을 명시한다.(`http`)
  - Host: 자원을 가지고 있는 host를 명시한다.(`www.example.com`)
  - Path: 요청되는 구체적인 자원을 명시한다.(`/tasks`)
  - Query: 선택적 구성요소로, 쿼리 문자열은 리소스가 검색 매개 변수와 같은 어떤 목적으로 사용할 수 있는 정보를 제공한다.(`/term=homework`)
- Messages: HTTP에는 2가지 message가 존재한다. 하나는 client에서 server로 전송되는 requests이고 다른 하나는 server에서 client로 전송되는 responses이다.
두 메세지는 그것들의 전체에 걸쳐 모두 uniform한 특정 구성요소들을 가지고 있다.
- Status Codes: Status code는 request가 성공적으로 처리되었는지 알 수 있고 어떤 success가 발생했는지 또는 어떤 문제가 발생했는지의 정보들을 얻을 수 있는 매우 빠른 방법이다.
(e.g. 404 not found)

#### Side Note: URI vs URL
- URI는 모든 자원의 identifier를 뜻한다. 이것은 자원의 이름, 주소 모두 될 수 있다.
- URL은 자원의 위치만을 뜻한다. 즉, 이것은 오직 address만을 의미한다.
- 정리하면 URL은 오직 address만을 뜻하는 반면 URI는 name, address 모두 가능하다. 그렇기 때문에 URL은 URI의 한 종류라고 볼 수 있다.

### HTTP Request Elements
![requestelement](/img/requestelement.png)
또는 다음과 같이 표현될 수도 있다.
```
GET /tasks?term=homework HTTP/2.0
Host: http://www.example.com
Accept-Language:en
```

- Method: 수행되어야하는 오퍼레이션을 선언한다.
- Path: Scheme과 host를 제외한 fetch되어야하는 자원의 URL이다.
- HTTP Versions
- Headers: Optional information, success as Accept-Language
- Body: Optional information, 보통 POST나 PATCH와 같은 메소드를 위한 것이며 server에 보내져야 하는 리소스를 포함하고 있다.


### HTTP Request Methods
- GET: 주어진 URI의 요청된 자원에 대한 정보를 오직 불러오기만 한다.
- POST: Data를 server에 보내고 새로운 자원을 생성한다.
  - i.e. Post request를 보내면 전송한 데이터가 데이터베이스에서 어떤 object의 새로운 instance를 생성할 것이다.
- PUT: Request data로 타겟 리소스의 모든 representation을 대체한다.
  - i.e. 만약 인스타그램을 올린다고 하면 이미지를 포함하지 않고 글만 적어서 올린다면 이미지는 사라지고 글은 업데이트 된다.
- PATCH: Request data로 타겟 리소스의 representation을 일부 변경한다.
  - i.e. 똑같은 인스타그램의 상황에서 이미지를 포함하지 않고 글만 적어서 올렸을 때 이미지는 그대로 유지되고 글만 업데이트된다.
  - 의도한 부분만 수정할 수 있기 때문에 PUT보다는 안전한 방법이다.
- DELETE: URI로 명시된 자원의 모든 representation을 제거한다.
- OPTIONS: 요청된 자원을 위한 communication option들을 전송한다.

### HTTP Responses
![responseElement](/img/responseelement.png)

#### Elements
- Status Code & Status Message
- HTTP Version
- Headers: Request header와 비슷하다. response와 resource representation에 관한 정보를 제공한다. 보편적인 headers는 다음과 같다.
  - Date: response가 언제 보내졌는지
  - Content-Type: request의 body의 media type
- Body: 요청된 리소스를 포함하는 optional data

#### Status Codes
API developer로서 정확한 status code를 보내는 것이 중요하다. status code는 무엇이 error를 발생시켰는지 또한 어떻게 진행되었는지를 이해하는데에 도움을 준다.

#### Codes fall into five categories
- `1xx`: Informational
- `2xx`: Success
- `3xx`: Redirection
  - 무슨 일이 발생해서 백엔드에 redirection이 발생했다면 우리는 그것을 알고 있어야한다.
- `4xx`: Client Error
  - Request가 제대로 포맷되지 않았거나 body가 잘못되었을 때와 같이 request에서 뭔가가 잘못되었을때 발생한다.
- `5xx`: Server Error
  - Request는 괜찮았지만 server가 성공적으로 일을 처리하지 못했을때와 같이 백엔드에 무언가가 발생했을 때 일어난다.

#### Common Codes
- `100`: Continue
  - Option을 받았고 프로세스에 대한 허가를 받았으므로 request를 계속 보내도 된다는 뜻
- `200`: OK
  - i.e. `GET` reqeust에서 성공적으로 처리되어 body를 return받았을때
- `201`: Created
  - i.e. `POST` request가 성공적으로 처리되었을 때 
- `304`: Not Modified
  - i.e. `PUT`/`PATCH` requests가 성공적으로 처리되지 않았을때
- `400`: Bad Request
- `401`: Unauthorized
  - Request는 잘 포맷되었으나 원하는 것 또는 리소스에 access를 가지고 있지 않을때
- `404`: Not Found
  - 요청하는 것이 백엔드에 존재하지 않음
- `405`: Method Not Allowed
- `500`: Internal Server Error
