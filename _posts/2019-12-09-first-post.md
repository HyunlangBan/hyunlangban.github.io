---
layout: post
title: 첫글! 깃허브 블로그 만들기
---

기술 블로그를 접하게 되면서 깃허브 블로그를 많이 보게 되었고 일일커밋이나 나의 공부 기록등을 자세하게 남길 곳이 있으면 좋을 것 같아서 블로그를 만들기로 결심했다.<br>
이름은 **'초보 개발자의 한걸음씩 나아가기록'**!<br><br>
처음에는 '초보개발자의 한걸음씩 나아가기'였는데 뭔가 맘에 안들어서 '흠 ..그럼 기록을 남기는 거니까 나아가기록으로 해볼까' 했는데 내 마음에는 든다. 블로그 이름대로 계속 나아가는 기록이 되길 바란다:) <br><br>

### 깃허브 블로그를 만들어보자!
맨 처음에는 그냥 구글링을 해서 제일 처음으로 나오는 [게시물](https://dreamgonfly.github.io/2018/01/27/jekyll-remote-theme.html)을 클릭했다. 한번 스윽 훑어보니 음 생각보다 쉽겠는걸? 했지만 하루 종일 걸려버렸다...<br><br>

#### 막힌부분 1. 저장소 이름


블로그를 만들 저장소 이름을 자신의 **깃허브 유저네임**으로 해야했지만 나는 그냥 성을 빼고 만들어버린것... 그렇게 만들면 내 블로그 주소를 치면 404 Error를 만날 수 있다.ㅎㅎ<br><br>
자신의 깃허브 유저네임과 **똑같이**(대소문자 구분없음) 만들어야한다!!<br>
<br>

#### 막힌부분 2. Jekyll Theme - Remote Theme

`_config.yml`파일에 `remote-theme`을 추가하면 분명히 된다고 했는데 나는 아무리 해도 안되는 것이다. <br>
그래서 원본 `_config.yml`을 가서 봤더니<br>
<br>

```
# Theme Settings
#
# Review documentation to determine if you should use `theme` or `remote_theme`
# https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#installing-the-theme
```
<br>
라는 것을 확인할 수 있었다.<br>
연결된 문서에서는 bundler 커맨드를 사용해야한다고 해서 bundler 설치를 하려고 [Getting Started](https://bundler.io/)를 따라해도 안되는 것이었다....<br>
Jekyll도 bundler도 다 처음보는데 다 생각대로 안따라줘서 블로그 만드는게 이렇게 어려운 일인가 싶었다...<br><br>


그래서 다시 다 접어두고 [다른 글](https://blog.chulgil.me/how-to-make-blog-using-github-4)을 참고했다. 하지만 bundler를 피해갈 순 없었다.(엉엉)<br>
그래서 다시 열심히 검색해보니 [윈도우즈에서의 Jekyll](https://blog.chulgil.me/how-to-make-blog-using-github-4/)을 발견했고 많은 도움을 받았다.<br><br>

여기서 설명하는대로 Ruby를 설치했더니 bundler도 설치할 수 있었다!!<br>
나도 그냥 해보면서 되는대로 해결했기 때문에 제대로 잘 한 건지는 모르겠지만 결국 내 블로그는 만들어졌으니 잘 해결 된 것 같다:)<br><br>

#### 막힌부분3: 깃허브 

내가 새로 찾은 글은 로컬 저장소에서 블로그를 만든 뒤 다시 깃허브로 이동을 시키는 방식이었는데 나는 커맨드로 깃허브를 거의 사용한 적이 없다. 평소에는 그냥 깃허브 홈페이지에 들어가서 만들거나 수정했다. 그래서 내 깃허브를 클론시키는거나 폴더와 파일들을 모두 옮기는 것 모두 애를 먹었다. <br><br>해당 블로그에서 설명하는 [GitHub로 배포] 부분을 따라했을 때 안되는 부분이 많았다. 하지만 깃허브를 어떻게 사용하는지 잘 모르는 상태이기 때문에 왜 틀린지 어떻게 해야할지 답답한 부분이 많았다. <br><br>우여곡절 끝에 clone을 하니 또 push가 안먹는다.....ㅠㅠㅠㅠㅠㅠㅠ 그래서 pull도 해보고(깃허브 진짜 잘 모름...) 그냥 이것저것 해보는데 다 안되서 깃허브 저장소 새로 만들면<br><br>
### …or push an existing repository from the command line
```
git remote add origin git@github.com:HyunlangBan/type-theme.git
git push -u origin master
```
<br>
라는 것을 볼 수 있는데 그냥 이거 따라했더니 옮겨졌다. 감사합니다...!!!!!!<br> 깃허브에서 계속 헤매면서 나의 무지함을 절실히 깨닫고 깃허브를 제대로 공부해야겠다는 다짐을 했다.<br>

블로그를 만들면서 예상보다 더 많이 헤매서 좀 슬펐지만 막상 만들어진 걸 보니 너무 기분이 좋다. <br>
여러 블로그들을 참고하면서 잘 만들어나가야지

