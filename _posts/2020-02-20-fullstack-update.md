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
- Jinja의 if statement: `d.completed`가 `True`이면 checked하고 아니면 아무것도 하지 않는다.


#### View Update - Send Request
```html
## index.html
<script>
  const checkboxes = document.querySelectorAll('.check-completed');
  for (let i = 0; i < checkboxes.length; i++){      # 1
    const checkbox = checkbox[i];
    checkbox.onchange = function(e) {               # 2
      console.log('event', e);
      const newCompleted = e.target.checked;        # 3
      fetch('/todos/set-completed', {               # 4
        method: 'POST',
        body: JSON.stringify({
          'completed': newCompleted
        }),
        headers: {
          'Content-Type`: 'application/json'
        }
      })
    }
  }
...
```

- #1. items의 모든 체크박스를 for문을 이용해 확인한다.
- #2. 만약 체크박스에 변경이 발생했다면 event function을 실행한다.
- #3. 체크 박스를 토글했을때 Console에서 event->target->checked를 확인하면 true와 false를 확인할 수 있다. 해당 상태를 `newCompleted`라는 변수에 저장한다.
- #4. 지정한 route로 request를 전송한다.
