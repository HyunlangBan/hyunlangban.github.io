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
