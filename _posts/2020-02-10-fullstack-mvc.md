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





