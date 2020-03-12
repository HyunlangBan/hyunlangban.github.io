---
layout: post
tag: fullstack
title: Authentication Methods
---
### Common Authentication Methods
#### Username and Passwords
누가 digital system에 접근하고 있는지를 확인하기 위하여 사용자를 인증하기 위한 많은 방법들이 존재한다. 가장 보편적인 방법은 username과 password 짝을 이용하는 것이다. 

보통 로그인 화면에서 plain text로 이루어진 아이디와 패스워드를 적고 확인 버튼을 누르면 username과 password가 포함된 body와 함께 서버에게 request를 전송된다. 

username이 unique하므로 username을 이용하여 database에서 해당 유저에 대한 정보를 요청한다. User table에서는 추가 정보와 함께 해당 유저의 정보를 return한다. 이 때 data의 일부분으로 password를 포함해서 보내는데 이것은 좋은 방법이 아니다. 데이터베이스에 plain text로 만들어진 passwords를 절대 저장해서는 안된다. password의 표현 방법은 따로 있다는 것을 명심해라. 

API server에서는 사용자가 제출한 password와 db에 저장된 password가 일치하는지 확인한다.(`postuser.password == dbuser.password`)

동일하다면 200 response을 front-end에 전달하고 로그인을 허용한다. 만약 일치하지 않는다면 401 HTTP status code로 접근을 거부할 수 있다. 

>- 401 Unauthorized: 자원에 접근하기 위해서 client는 반드시 인증(Authentication)을 통과해야하고 Server가 요청된 부분에서 identity를 검증하지 못했다.
>- 403 Forbidden: Client가 해당 자원에 접근할 수 있는 허가를 받지 못했다. 401과 달리, server가 client가 누군지는 알고 있는 상태지만 자원에 대한 접근권한(Authorization)이 없다.

#### Intro to Problems with Passwords
개발자로서 패스워드에 대한 문제는 통제 밖의 일이다. 많은 문제들이 개발자가 직접 영향을 줄 수 없는 사용자의 행동들에서 비롯된다.
- 사용자들이 그들의 비밀번호를 잊는다.
    - 잊지 않기 위해 포스트잇이나 word등 모두의 접근이 가능한 곳에 메모해둔다.
- 사용자들이 간단한 패스워드를 사용한다.
- 사용자들이 공통된 패스워드를 사용한다.
- 사용자들이 하나의 패스워드를 여러 사이트에 사용한다.
- 사용자들이 패스워드를 공유한다.
    - 만약 다른사람이 패스워드를 도용하여 접속했다고 해도 이를 알아내기 어렵다.

반대로 우리의 통제권안에 있는 문제들도 있다.
- 패스워드가 노출될 수 있다.
  - 만약 plain text로 password를 저장하거나 보안이 취약한 시스템에 저장한다면, 정보를 가져서는 안되는 사람에게 비밀번호가 넘어갈 수 있다.
- 개발자들이 부정확하게 확인할 수도 있다. 
  - 개발자들은 password 확인을 할때 꽤 쉽게 실수를 범한다. 아주 사소한 실수라도 심각한 보안 취약점이 될 수 있다. 
- 개발자들이 쉽고 빠른 방법을 선택할수도 있다.
  - 그들은 가장 안전하지 않더라도 가장 간단한 방법을 선택할 것이다.

<br>

### Alternative Authentication Methods
#### Single Sign-On(SSO)

![sso](/img/sso.png)

'누가 request를 만들고있는가?'에 대한 질문에 대답하기 위하여 다른 누군가를 신뢰하는 것이다. 

SSO는 많은 이점을 가지고 있다.
- 우리가 실제 인증을 수행하지 않아도 된다.
  - 우리는 거대하고 견고한 시스템 내의 엔지니어들이 이 일을 잘 해내고 있음을 신뢰한다.
- 이미 로그인된 서비스를 사용해서 인증함으로써 사용자들이 더 쉽게 접근할 수 있도록 한다.

#### Multi-Factor Authentication
![multi-factor-auth](/img/mfa.png)

사용자가 비밀번호를 공유했거나 또는 누군가가 비밀번호를 도용하고 있을수도 있다. Multi-factor authentication은 패스워드의 위에 한 층의 trust를 더 부여하는 것이다. 
이 인증 방법은 오직 당신만이 물리적 또는 민감하고 안전한 무언가에 접근 할 수 있다고 믿는다. 

보통 문자 인증처럼 text-based interface가 보편적이다. 사용자가 사용자의 휴대폰을 안전한 장소에 보관하고 있다는 가정하에 이루어진다.  로그인을 하면 code가 사용자의 휴대폰으로 보내지고 이 코드는 일회성이며 해당 사용자의 휴대폰만 감지한다. 

#### Passwordless
Multi-Factor Authentication을 극단적으로 생각해보면 사실 패스워드 그 자체는 인증 과정에서 가장 취약한 부분이라는 것을 깨닫게 된다. Passwordless design pattern은 당신만이 물리적인 어떤 것에 접근할 수 있고 처음의 당신의 비밀번호가 형편 없다고 믿는다. 

![passwordless](/img/passwordless.png)

그들은 오직 인증할 user ID가 무엇인지만 보내면 된다. 서버는 alternative device에 코드를 전송하고 사용자가 해당 코드를 sevice에 다시 보낼 수 있는 interface를 제공한다. 

Slack의 magic link와 Google의 cell phone sign-in이 이에 해당한다.

#### Biometric Authentication
아마도 가장 안전한 인증 방법은 biomatric authentication일 것이다. 가장 보편적인 방법은 휴대폰의 지문 인증과 같은 것이다. 이러한 시스템들은 꽤 복잡하고 견고하다. 

하지만 지문을 복사하거나 형편없게 만들어진 biometric authentication system을 파괴할 수도 있다. 때문에 retina scnas이나 보다 복잡한 indicators를 사용하면 더욱 안전하다.


이 대안적인 방법들은 확실히 검증되지 않았다. 모든 시스템들과 마찬가지로, 여전히 관련된 위험이 존재한다. 예를 들어, multi-factor auth는 SMS 메시지를 읽을 수 있는 안드로이드의 악성 앱으로 인해 계속해서 좌절되고 있다. 이 취약성이 발견되자 구글은 이러한 메시지에 액세스하기 위한 사용 권한 시스템을 변경했다. 하지만, 적들은 알림 표시줄에서 메시지를 읽는 새로운 방법을 발견해냈다. 위에서 제시된 인증 방법들을 결합하고, 시스템의 가장 중요한 부분에 대해 생각함으로써, 개발자로서 당신은 위험을 최소화할 수 있지만, 결코 진정으로 위험을 제거할 수는 없다.
