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

**+)** `backref` 이해하고 넘어가기
`backref`에는 child object가 할당될 parent object의 이름을 적는 것이다.
[SQLAlchemy ORM relationship docs](https://docs.sqlalchemy.org/en/13/orm/relationship_api.html#sqlalchemy.orm.relationship)에는 'indicates the string name of a property to be placed on the related mapper’s class that will handle this relationship in the other direction.'라고 쓰여있다.
<br>
예시로 보면 다음과 같다.
```
child1 = SomeChild(name='Andrew')
child1.some_parent
```
`some_parent`는 아까 `backref`에서 설정했던 이름이다. 내가 만약 `SomeChild`에서 이름이 Andrew인 새로운 객체를 만들어 `child1`이라고 했다면 `child1.some_parent`는 `child1` object가 속한 parent object를 return한다.
+) [backref in foreign key](https://github.com/coleifer/peewee/issues/2027) 

<br>

### Configuring Relationships
- `child1.some_parent`를 호출하면 SQLAlchemy는 데이터베이스에서 parent를 언제 로드할지 결정한다.

#### parents를 언제 로드하는지가 왜 중요한가?
- Joins are expensive.
- User idling(대기)을 피해야한다. 150ms이상의 딜레이는 noticeable하다.
- Joins는 경험에 부정적인 영향을 미치지 않는 UX에서 짧은 시간동안 이루어져야 한다. 

#### Lazy loading vs. Eager loading
- Lazy loading(**Default**): lazy loading은 필요할때만(e.g. `child.parent` object가 호출될때) 로딩이 발생한다.
  - Pro: 초기 대기 시간이 없다. 필요한 것만 로드한다.
  - Con: joined asset에 대한 요청이 있을때마다 매번 join SQL을 생성해내야 하므로 호출이 많을 때에는 좋은 방법이 아니다.
- Eager loading: 모든 joined data objects를 한번에 로드한다.
  - Pro: 추후에 발생할 수 있는 query들을 줄여준다. 
  - Con: 처음에 joined table을 로드할때 시간이 오래걸린다.
 <br><br>
기본 옵션은 `lazy=True`이므로 생략 가능하다.
`children = db.relationship('ChildModel', backref='some_parent', lazy=True)`

<br>

### Other relationship options: `collection_class` and `cascade`
```python
class Parent(db.Model):
 __tablename__ = 'parents'
 id = db.Column(db.Integer, primary_key=True)
 name = db.Column(db.String(), nullable=False)
 children = db.relationship(
  'Child', backre='parent', lazy=True,
  collection_class=list,
  cascade = 'save-update' # OR: all, delete-orphan
```
- `collection_class`: children의 collection을 list, dictionary, set 등으로 다양하게 나타낼 수 있다.
- `cascade`: parent에 update, delete가 발생했을 때 children은 어떻게 처리할지 결정한다.

<br>

### Foreign Key Constraint Setup
- `db.relationship`은 foreign key 설정을 해주지 않기 때문에 foreign key constraint(제약조건)를 지닌 child model에서 `some_parent_id`라는 컬럼을 추가해야한다.
- `db.relationship`은 parent model에서 했지만 foreign key constraint는 child 모델에서 해야한다.
- Foreign key constraint는 foreign key 컬럼과 foreign table의 primary key가 항상 대응되는것을 보장하면서 null값을 가지지않는 referential integrity을 지닌다.

#### `db.ForienKey`
![foreignkey](/img/foreignkey.png)
- `some_parent_id`의 데이터 타입은 foreign table `id`의 데이터 타입과 동일해야한다.

#### Example: a driver has many vehicles
```python
class Driver(db.Model):
 __tablename__ = 'drivers'
 id = db.Column(db.Integer, primary_key = True)
 ...
 vehicles = db.relationship('Vehicle', backref='driver', lazy=True)
 
class Vehicle(db.Model):
 __talbename__='vehicles'
 id = db.Column(db.Integer, primary_key=True)
 make = db.Column(db.String(), nullable = False)
 ...
 driver_id = db.Column(db.Integer, db.ForeignKey('drivers.id'), nullable=False)
```
 
