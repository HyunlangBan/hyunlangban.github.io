---
layout: post
title: Flask, Curl and Chrome Dev Tools
tag: fullstack
---

### Intro to Flask
API server를 만들기 위해 Flask를 사용할 것이다. Flask는 microframework로 core functionality는 단순하게 유지하되 수많은 extensions과 개발자들이 
다른 functionality(i.e. authentication, database support..)를 추가하는 것을 허용한다.

### Creating a Baisc Flask Application

#### Directory & Virtualenv Set Up
1. 커맨드창에서 `mkdir [proejct_name]`으로 폴더를 만들어 준 후 해당 폴더로 이동한다.
2. `pip3 install flask`로 flask를 설치한다.
3. `flaskr` 폴더를 만들고 그 안에 `__init__.py`를 생성한다.

#### Create_app
```python
from flask import Flask, jsonify

def create_app(test_config=None):
  app = Flask(__name__)    #1
  
  @app.route('/')  #2
  def hello():  #3
    return jsonify({'message': 'Hello, World!'})  #4
  
  return app
```
- #1. `__name__`은 application이 어떤 디렉토리에 저장된 지 알 수 있도록 알려주는 역할을 한다. 그렇기때문에 추가적인 configuration이나 
찾고있는 relative paths가 어디로 가야할지 알 수 있다.
- #2. 파라메터로 우리가 이동할 path를 가진다. 
  - `@app.route`는 GET request에 응답하는 것이 기본 설정이다.
- #3. route를 handle할 메소드를 선언한다.
  - 중복된 route decorator나 route handler는 발생할 수 없다.
- #4.메세지를 포함한 객체를 보내기위해 `jsonify()`를 사용한다.
  - `jsonify()`를 해주지 않고 딕셔너리를 그대로 return한다면 TypeError가 발생한다.

### Run your appliction
- 윈도우 환경
```
set FLASK_APP=flaskr  #1
set FLASK_ENV=development  #2
flask run
```
**띄어쓰기 주의**

- #1. app이 application을 어디서 찾아야할지 알려준다. 1번을 실행하면 `flaskr`폴더 안에 `init.py`파일이 있는 것을 알게 된다.
- #2. Development mode로, 변경이 발생할 때마다 server를 자동으로 재실행해주기 때문에 개발 시간을 줄일 수 있다. 
처음에만 1, 2번을 실행해주면 된다.

### Basic Flask Set Up

#### Flask Project Directory
![project_directory](/img/projectdirectory.png)

#### Configuration
큰 프로젝트에서는 development 환경, production 환경, testing 환경 등을 위한 복잡한 configuration들을 가진다. Flask는 이것을 더 간단하게 만들어줄 수 있다.
```python
def create_app(test_config=None):
  # create and configure the app
  app = Flask(__name__, instance_relative_config=True)  #1
  app.config.from_mapping(  #2
    SECRET_KEY='dev',
    DATABASE=os.path.join(app.instance_path, 'flaskr.splite'),
  }
  if test_config is None:  #3
    # load the instance config, if it exists, when not testing
    app.config.from_pyfile('config.py', silent=True)  #4
```
- #1. 추가된 파라메터: 같은 프로젝트 디렉터리 내에 configuration들이 존재하는 것을 말한다.
- #2. app 오브젝트의 config를 `from_mapping`으로 수정할 수 있다.
- #3. testing mode인지 아닌지를 확인한다.
- #4. 주어진 파일에서 config를 불러온다.

<br>

![communication](/img/communication.png)
