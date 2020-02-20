---
layout: post
title: The D in CRUD
tag: fullstack
---

### Delete a Todo Item
- item 옆에 있는 x표시를 누르면 아이템이 삭제되로록 만든다.

#### MVC
- View: 사용자가 누를 수 있는 삭제 버튼을 만들고 눌렀을 때 contorller로 data를 전송한다.
- Controller: 어떤 item을 삭제해야하는지 id를 통해 전달받고 model이 id를 통해 적절한 todo item을 가져올 수 있도록 한다. 또한 Model이 해당 todo object들을 데이터베이스에서 삭제하도록 알려준다.
Model이 성공적으로 일을 끝마치면 View가 업데이트 된 페이지를 나타낼 수 있도록 명령한다.

#### D: Delete
- SQL
```sql
DELETE FROM table_name
WHERE condition;
```

- SQLAlchemy ORM
```python
todo = Todo.query.get(todo_id)
db.session.delete(todo)
# or...
Todo.query.filter_by(id=todo_id).delete()

db.session.commit()
```

#### Update the view
<script src="https://gist.github.com/HyunlangBan/759b3adf28db2f01e914d67e6bc7d81b.js"></script>

#### Use the `DELETE` method
- In `<script>...</script>` of the `index.html`
```js
const deleteBtns = document.querySeletorAll('.delete-button');
  for(let i = 0; i < deletionBtns.length; i++) {
    const btn = deleteBtns[i];
    btn.onclick = function(e) {
      const todoId = e.target.dataset['id'];
      fetch('/todos/' + todoId, {
        method: 'DELETE'
      });
    }
  }
```

#### Define the route handler
```python
# app.py

@app.route('/todos/<todo_id>', methods=['DELETE'])
def delete_todo(todo_id):
  try:
    Todo.query.filter_by(id=todo_id).delete()
    db.session.commit()
  except:
    db.session.rollback()
  finally:
    db.session.close()
  return jsonify({ 'success': True })
```
