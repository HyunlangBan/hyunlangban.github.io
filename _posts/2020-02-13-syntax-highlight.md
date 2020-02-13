---
layout: post
title: type-theme에 syntax highlighting 적용하기
tag: jekyll
---

jekyll로 블로그를 처음 만들었을 때 선택했던 테마가 [type-theme](https://github.com/rohanchandra/type-theme)이다.

사실 아직도 jekyll로 블로그를 운영하는게 어렵기만 하다.

내가 가진 css, html 지식 선에서 고치고 싶은건 고쳐보다가 어느덧 코드 블럭이 고치고 싶어졌다.

#### 고치고 싶었던 이유
- 블로그 배경색과 코드블럭 배경색이 동일해서 코드가 눈에 안띈다.(inline code도 동일)
- 글씨체가 너무 마음에 안든다.
- 하이라이팅도 마음에 안든다.

#### 한계점
- 아직 나에게는 복잡하게만 느껴지는 jekyll 구조
- syntax 기본 파일이 있는데 `.scss`이다. 난생 처음본다. 태그도 너무 복잡했고 아는 선에서 편집할 수 가 없었다.
- 하다보니 plugin을 쓰라는데 gem이니 뭐니 무슨말인지 모르겠다.

이런 상황에서 시행착오를 엄청나게 겪었다. 일단 검색해서 나오는 글들은 거의 다 따라해봤는데도 제대로 적용이 되지 않았다.

그 때 마음에 쏙 드는 syntax lighlighter를 발견했다. 바로 [Prism](https://prismjs.com/). 결론적으로는 실패했었지만 Tomorrow Ngiht 테마가 완전 취향 저격이었다.


#### 설치방법(type-theme기준)
1. `/assets/css`에 `prism.css`를 올린다.
2. `/assets/css`에 `prism.js`를 올린다.
3. `/includes/head.html`에 `<link rel="stylesheet" href="{{ "/assets/css/prism.css" | relative_url }}">`을 해준다.
4. 공식 사이트에서는 body에 넣으라고 하는데 어떤 블로그는 `footer.html`에 넣어도 된다고 했다. 나는 footer에 넣었던 것 같다.
- in `/_includes/footer.html`: `<link rel="stylesheet" href="{{ "/assets/css/prism.css" | relative_url }}">`
- in `/_layouts/default.html`: post를 작성할때 `default.html`을 기반으로 작성되고 여기에 <body>태그가 존재하므로 그 안에 위와 똑같은 내용을 적어주면 된다.


#### 문제점
- 코드블럭이 바뀌긴 했고 파이썬 코드나 자바스크립트 코드에서는 적용이 잘 되었으나 html에서 여백이 몇배가 커져서 코드를 알아볼 수 없는 지경이었다.
  검색해보니 [stackoverflow](https://stackoverflow.com/questions/14559436/prism-html-highlighter)에 관련 글이 있었다. 근데 이건 jekyll의 경우는 아니다.

#### 해결방안
- gem에 [prism-plugin](https://rubygems.org/gems/jekyll-prism-plugin/versions/0.0.1)이 있고 이걸 추가하면 jekyll에서도 쓸 수 있는 것 같다.
  - 따라해봤는데 안된다. `gem`을 어떻게 쓰는지 잘 모르겠다. gemfile에도 추가하고 config에 있는 plugin에도 추가했는데..
  

### 결국.....
이 외에도 많은 삽질을 계속 하다가 결국 처음으로 되돌아왔다.
지킬을 자유자재로 쓸 수 없는 지금의 나로서는 기본에 충실하는게 답이었다. 그냥 rouge를 쓰는 것이...
(궁금한게 config에는 기본 설정이 `highlight: rouge`라고 써있었는데 `syntax.scss`는 pygments게 들어있다. 뭘까?)

#### 최종 해결방안
- 기본 syntax파일(`/_sass/external/syntax.scss`)에 주석으로 달려있는 [pygments-css](https://github.com/richleland/pygments-css)에서 monokai를 선택했다.
글꼴은 포함이 안되어있으므로 하이라이팅과 블럭색이 바뀌었다.
- 해결하지 못한 글꼴과 inline코드를 포기할 수 없어서 그냥 헤더에 `prism.css`를 링크해버렸다.
- 그리고 코드를 작성할 때는 pygments를 사용할 때의 방식으로 코드 블럭을 작성해주면 된다. 기본 markdown방식으로 하면 적용이 안된다.
  - `{% highlight myLanguage %}your code{% endhighlight %}`
  
  
결국은 코드블럭을 바꾸기는 했다. 배경도 어두워졌고 글꼴도 바뀌었다. 
하지만 gem plugin 사용법을 다시 더 찾아본다음에 tomorrow night 테마를 다시 적용해보고 싶다.
