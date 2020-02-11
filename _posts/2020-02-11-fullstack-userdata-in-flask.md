---
layout: post
title: Getting User Data in Flask
tag: fullstack
---

Getting User Data in Flask 
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

