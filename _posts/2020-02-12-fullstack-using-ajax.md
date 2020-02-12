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
```javascript
var xhttp = new XMLHttpRequest();  // #1
description = document.getElementById("description").value;  // #2
xhttp.open("GET", "/todos/create?description="+description);  // #3
xhttp.send();  // #4
```
>- The XMLHttpRequest object can be used to exchange data with a web server behind the scenes. 
>- open(method, url, async):	Specifies the type of request
>  - method: the type of request: GET or POST
>  - url: the server (file) location
>  - async: true (asynchronous) or false (synchronous)
>- send():	Sends the request to the server (used for GET)
>- send(string):	Sends the request to the server (used for POST)

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
```javascript
xhttp.onreadystagechange = function() {
  if (this.readyState === 4 && this.status === 200) {
  // on successful response
  console.log(xhttp.responseText);
  }
};
```
>The `readyState` property holds the status of the `XMLHttpRequest`. <br>
>The `onreadystatechange` property defines a function to be executed when the `readyState` changes. <br>
>The `status` property and the `statusText` property holds the status of the `XMLHttpRequest` object. <br>
>The `onreadystatechange` function is called every time the `readyState` changes. <br>
>**When `readyState` is 4 and status is 200, the response is ready.**


### Using `Fetch`
```
fetch('/my/request', {
  method: 'POST',
  body: JSON.stringity({
    'description': 'some description here'
  }),
  headers: {
    'Content-Type': 'application/json'    // To server knows to parse it as JSON
  }
});
```
- `fetch`는 request를 더 쉽게 보낼 수 있도록 해준다.
- request와 관련된 headers, body, method와 같은 파라메터를 가진다.
- `fetch(<url-route>, <object of request parametsers>)`
