---
layout: post
tag: fullstack
title: Relationships
---

### Modeling Relationships
우리는 이제까지 하나의 모델에 대한 CRUD를 배웠다. 하지만 앞으로 여러 모델들이 서로 관계를 가지고 있는 web apps를 만나게 될 것이다.
모델들간의 관계는 한 모델에 대한 액션이 다른 모델에게도 발생할 수 있는지를 결정한다. 그렇기 때문에 관계가 있는 모델끼리는 한 모델에 어떤 일이 발생하면
 관계된 다른 모델들도 영향을 받을 수 있는 것이다.(ex. 사용자의 계정을 삭제하면 사용자와 관계된 모든 사진, 문서 등이 함께 삭제된다.)
<br><br>
**GOAL**: Todo App에서 todo list라는 모델을 하나 더 만들고 카테고리화하여 각 카테고리에 토글했을 때 맞는 아이템들이 나올 수 있도록 relationship을 설정할 것이다.


#### Relationships & Join
![join.png](/img/join.png)

- 두 테이블(parent, child)이 있을 때 child 테이블에서 foreign key를 갖는다.
- foreign key는 parent 테이블의 기본키이다.

#### join qeury example
Sarah가 가지고 있는 자동차 브랜드와 모델, 년도는?
```sql
SELECT make, model, year FROM vehicles
JOIN drivers
ON vehicles.driver_id = driver.id
WHERE drivers.name = 'Sarah';
```

<br>

### Relationships in SQLAlchemy ORM
Join된 데이터가 필요할때 우리는 조인된 테이블을 저장하고 거기서 값을 가져와야할까 아니면 매번 조인을 해야할까? SQLAlchemy ORM에서는 `db.relationship()`을 한번만 설정하면 우리가 원할때마다 join statements를 생성해준다.

#### Example
```
class SomeParent(db.Model):
 id = db.Column(db.Integer, primary_key=True)
 name = db.Column(db.String(50), nallable=False)
 children = db.relationship('SomeChild', backref='some_parent')
```
- `db.relationship()`은 parent 모델 내에서 설정한다.
- 자식(child) 모델의 복수형으로(e.g.`children`) 이름을 설정한다. 그리고 파라메터로는 자식 클래스의 이름을 적어주고 `backref`를 설정한다.

+) `backref` 이해하고 넘어가기
`backref`에는 child object가 할당될 parent object의 이름을 적는 것이다.
[API](https://docs.sqlalchemy.org/en/13/orm/relationship_api.html#sqlalchemy.orm.relationship)에는 'indicates the string name of a property to be placed on the related mapper’s class that will handle this relationship in the other direction.'라고 쓰여있다.
<br>
예시로 보면 다음과 같다.
```
child1 = SomeChild(name='Andrew')
child1.some_parent
```
`some_parent`는 아까 `backref`에서 설정했던 이름이다. 내가 만약 `SomeChild`에서 이름이 Andrew인 새로운 객체를 만들어 `child1`이라고 했다면 `child1.some_parent`는 `child1` object가 속한 parent object를 return한다.
+) [backref in foreign key](https://github.com/coleifer/peewee/issues/2027) 
