---
layout: post
tag: fullstack
title: API Testing
---

### Why Testing?
어느 코드를 작성하든지, 당신은 API를 테스트하여 요청이 예상대로 처리되고, 전송된 응답이 정확하며, 데이터베이스에서 수행되는 작업이 올바르고 유지되는지 확인하고 싶을 것이다.

모든 테스트와 마찬가지로, API에 대한 unittests를 작성하는 것은 동작을 검증한다. API의 경우 다음과 같이 테스트가 기록되어야 한다.
- 예상되는 request handling behavior 확인
- Success-response 구조가 올바른지 확인
- 예상되는 에러들이 적절히 다루어지는지 확인
- CRUD 작업들이 지속되는지 확인

Behaviors를 검증하는 것 외에도 철저한 test suits를 갖추면 API를 업데이트 할 때 이전의 모든 기능들을 쉽게 테스트 할 수 있다.

앱 개발을 위한 운영 순서는 항상 다음과 같아야한다:
1.  Develpment
2. Unit Testing
3. Quality Assurance
4. Production


### Testing in Flask
```python
class AppNameTestCase(unittest.TestCase):       #1
    """This class represents the ___ test case"""

    def setUp(self):       #2
        """Executed before each test. Define test variables and initialize app."""
        self.client = app.test_client
        pass

    def tearDown(self):      #3
        """Executed after reach test"""
        pass

    def test_given_behavior(self):       #4
        """Test _____________ """
        res = self.client().get('/')  # endpoint를 지정해준다.

        self.assertEqual(res.status_code, 200)

# Make the tests conveniently executable
if __name__ == "__main__":       #5
unittest.main()
```
- #1. Application을 위한 test case class를 정의한다. 
- #2. `setUp` fauction을 정의하고 실행한다.
  - 각 테스트 전에 실행되며 테스트에 필요한 다른 context뿐만 아니라 app과 test client를 초기화해야 하는 곳이다. Flask 라이브러리에서 application을 위한 test client를 제공한다.
- #3. `teatDown` 메소드를 정의한다.
  - 이 메소드는 각 테스트 이후에 실행된다. 테스트 성공 여부에 관계 없이 `setUp`이 성공적으로 실행되는 한 실행된다.
  - 되돌려야할 것이 있을 때 사용된다.
- #4. 당신의 테스트를 정의한다. 
  - 모두 `"test_"`로 시작해야하며 test의 목적에 관한 doc string을 포함해야 한다.
  - 테스트를 정의할 때 당신은 client가 request를 만들게 함으로써 response를 가져와야 한다.
  - `self.assertEqual`을 사용하여 status code 및 모든 관련 operations를 확인해야 한다.
- #5. Test suite를 실행한다.
  - 커맨드 라인에서 `python test_file_name.py`를 실행한다.
  
 ### TDD(Test-Driven Development) for APIs
테스트 기반 개발(또는 TDD)은 프로덕션에서 매우 일반적으로 사용되는 소프트웨어 개발 패러다임이다. Executable code 이전에 테스트를 작성하며 지속적으로 반복하는 짧고 빠른 개발 주기에 기반을 두고 있다.


![TDD.img](/img/tdd.png)

1. Write test for specific application behavior.
2. Run the tests and watch them fail.
3. Write code to execute the required behavior.
4. Test the code and rewrite as necessary to pass the test
4. Refactor your code.
5. Repeat - write your next test.


