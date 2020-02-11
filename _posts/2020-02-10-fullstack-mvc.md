---
layout: post
title: MVC & Handling User Input
tag: fullstack
---
### MVC
**MVC(Model View Controller)**: Full stack apllication의 common pattern으로, application을 Model, Veiw, Contorller 3개의 레이어로 생각하며
apllication의 어떤 파트를 관리하고 있는지 나타내기 위함이다.

#### Layers
- Model: 모델과 데이터베이스 내에서 발생하는 것들과 web app의 모든 objects들의 논리적 관계와 특성들을 관리한다.
- veiw: 데이터를 가지고와서 사용자에게 어떻게 보여줄지 관리한다.
- controllers: model과 view는 직접 대화하지 않고 controller를 통해서 대화한다. 모델과 뷰에게 명령이 어떻게 전달될지, 또한 어떻게 모델과
뷰가 interact하며 서로를 업데이트 되게 할 수 있을지에 대한 로직들을 제어한다.

![mvc](/img/mvc.png)

#### View
사용자가 웹사이트를 방문할 때 보는 것이 view이며 CSS, Javascript를 포함한 html파일은 사용자의 관점에서 web app과 interact할 수 있도록 해준다.
view에서 뭔가를 클릭했을 때 서버에게 요청을 전송한다. 그 요청들은 특정한 경로를 통해 controller에게 전송된다.

#### Controller
사용자의 요청들을 처리하고 모델에게 데이터베이스에 있는 데이터로 어떤 작업을 해야할지 알린다.(ex. read, write...)
그리고 모델에서 오는 response에 근거하여 어떤 view를 보여주어야 할지 알려준다.

#### Model
cotorller가 직접적으로 veiw를 결정할 때(ex.다른 페이지로 이동)를 제외하고는 model에서 업데이트가 발생할시 view도 업데이트 된다.
왜냐햐면 view는 conroller를 통해 model의 데이터에 종속되어있기 때문이다.

#### Example
```python
@app.route('/')
  def index():
    return render_template('index.html', data = Todo.query.all())
```

index method는 contoller이며 사용자가 다음에 무엇을 봐야할지, model이 어떤 interaction을 할지 결정한다:  `SELECT * FROM todos`를 한 후 
그 데이터를 나타내기 위해서 `index.html` template을 사용해야한다고 view에게 말한다.


이렇게 application을 Model, Controller, View의 관점으로 나누어서 보게되면 모든 레이어 전반에 걸쳐 나타나는 버그들을 쉽게 분류해낼 수 있다.
문제가 생기면 먼저 '이 문제는 지금 model, controller, view 중 어디에서 나타나는걸까?'라는 생각을 한 뒤 해당 레이어에서 문제를 찾아낸다.

<br>

### Handling User Input
우리는 데이터베이스에 있는 값을 가져올 뿐만 아니라 view에서 사용자 지정값을 받아서 어떤것을 추가, 업데이트하거나 삭제해야한다.

#### Craete To-Do Item Functionality
- On the view: html에서 userinput을 받을 수 있는 text field와 같은 form과 값을 submit할 수 있는 버튼을 생성한다.(Accept user input)
- On the controller: user input을 받고 model을 manipulate한다.(Retrieve user input)
- On the model: 데이터베이스에서 레코드를 추가하고 새롭게 생성된 to-do item을 컨트롤러에게 return한다.(Manipulate models using user input(creating a record))
- On the controller: 새롭게 생성된 to-do tiem을 받고 그 값으로 어떻게 view를 update할지 결정한다.

<br>

### Getting User Data in Flask - Part 1
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

