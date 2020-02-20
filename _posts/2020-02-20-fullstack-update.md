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

#### View Update - 체크박스 나타내기
<script src="https://gist.github.com/HyunlangBan/19a43d15fef0a51ace72fa473ba87dbd.js"></script>
- Jinja의 if statement: `d.completed`가 `True`이면 checked하고 아니면 아무것도 하지 않는다.



