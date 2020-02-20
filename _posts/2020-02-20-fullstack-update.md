---
layout: post
title: Updating a Todo Item
tag: fullstack
---

### Implement Updating a Todo Item
- 기존 todos에서 체크박스를 추가하여 완료/미완료 표시를 하기로 한다.

#### MVC 
- View: 각 todo 아이템들 옆에 체크박스가 나타나도록 업데이트 한다. 사용자가 체크박스를 [toggle](http://www.terms.co.kr/toggle.htm)하면 controller로 어떤 todo item이 update되어야할지
completed state를 True로 바꿀지 False로 바꿀지에 대한 request를 보낸다.
- Cotroller: 컨트롤러는 기존의 todo 모델을 조정하고 상태를 업데이트 하고 view에 response를 되돌려준다. 예를들어 컨트롤러는 뷰에게 업데이트된 아이템
들을 보여주기 위하여 전체 페이지를 새로고침을 하라고 말할 수 있다.

#### U: Update
- SQL
```sql
UPDATE table_name
SET comlumn1 = value1
WHERE condition;
```

- SQLAlchemy ORM
```python
user = User.query.get(some_id)
user.name = 'Some new name'
db.session.commit()
```

<br>

#### View Update - Show Checkboxes
<script src="https://gist.github.com/HyunlangBan/19a43d15fef0a51ace72fa473ba87dbd.js"></script>
- Jinja의 if statement: `d.completed`가 `True`이면 checked이고 `False`이면 아무것도 발생하지 않는다.
- event function에서 id를 사용하기위해서 `data-id`라는 data attribute를 만들어 jinja를 통해 id값을 가져온다. (`data-`뒤에 붙는 이름으로 딕셔너리가 생성된다. ex. {'id': 12} 

<br>

#### View Update - Send Request
```html
## index.html
<script>
  const checkboxes = document.querySelectorAll('.check-completed');
  for (let i = 0; i < checkboxes.length; i++){      # 1
    const checkbox = checkbox[i];
    checkbox.onchange = function(e) {               # 2
      # console.log('event', e);
      const newCompleted = e.target.checked;        # 3
      const todoId = e.target.dataset['id'];        # 4
      fetch('/todos/' + todoId + 'set-completed', {               # 5
        method: 'POST',
        body: JSON.stringify({
          'completed': newCompleted
        }),
        headers: {
          'Content-Type`: 'application/json'
        }
      })
      .then(function() {
        document.getElementById('error').className = 'hidden';
      })
      .catch(function() {
        document.getElementById('error').className = '';
      })
    }
  }
...
```

- #1. items의 모든 체크박스를 for문을 이용해 확인한다.
- #2. 만약 체크박스에 변경이 발생했다면 event function을 실행한다.
- #3. 체크 박스를 토글했을때 Console에서 event->target->checked를 확인하면 true와 false를 확인할 수 있다. 해당 상태를 `newCompleted`라는 변수에 저장한다.
- #4. 마찬가지 front end 영역에서 만들어준 id값을 가져온다.
- #5. 지정한 route(item의 id별로 달라짐)로 request를 전송한다.

<br>

#### Define the route handler
```python
@app.route('/todos/<todo_id>/set-completed', methods=['POST'])   # 1
def set_completed_todo(todo_id):
  try:
    completed = reguest.get_json()['completed']
    todo = Todo.query.get(todo_id)
    todo.completed = completed            # 2
    db.session.commit()
  except:
    db.session.rollback()
  finally:
    db.session.close()
  return redirect(url_for('index'))    # 3
```
- #1. <todo_id>: `<>`안에 파라메터를 넣으면 메소드 내에서 바로 argument로 사용이 가능해진다.
    - request를 보낼때 <todo-id>에 id value가 있는 채로 보내지므로 handler에서는 바로 todo-id를 사용할 수 있다.
- #2. 해당 item의 completed 컬럼에서의 값을 request에서 전달받은 상태로 설정한다. 
- #3. 새로운 todo list를 확인하기 위해 전체 페이지를 새로고침한다.
  
<br>

#### Fixing Ordering
아이템들을 체크 한 후 새로고침을 눌러보면 아이템들의 정렬이 변하는 것을 볼 수 있다.
이것을 해결하기 위해 `id`를 이용하여 추가된 순으로 정렬해본다.
```python
@app.route('/')
def index():
  return render_template('index.html', data = Todo.query.order_by('id').all())
```
