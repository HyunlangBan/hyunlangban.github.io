---
layout: post
title: Introduction to APIs
tag: fullstack
---
### APIs(Application Programming Interfaces)

이전 시간까지는 web을 위해 data를 어떻게 모델링하는지에 대해 배웠다. 이번 강의에서는 API에 대해 더 많이 배우게 될 것이다. API는 통제된 방식으로 백엔드를 
노출하기 위해 필수적이며, 접근하기 위한 behaviors들을 규정할 수 있고 [데이터의 무결성](https://untitledtblog.tistory.com/123)을 유지하면서 데이터를 조정할 수 있다.

### What are APIs?
API는 이름에서 알 수 있듯이 *interface*이다. 두 개의 다른 시스템들이 서로 interact하기 위해 만들어졌다.

google map은 방대한 양의 데이터를 가지고 있다. 그리고 많은 다른 어플리케이션들은 구글 맵을 사용하고 있다. 하지만 구글맵은 모든 사람들에게 그들이 가진 
모든 데이터나 그들이 구현한 것을 노출하고 싶지 않을것이다. 

API는 Google이 그들의 functionality 일부를 공유하여 안전한 방법으로 데이터를 공유할 수 있도록 한다. 

#### APIs...
- API는 어플리케이션 구현을 노출하지 않는다. 코드를 노출할 필요가 없다.
- 당신의 어플리케이션과 데이터의 노출을 통제한다. 어떤 데이터를 사람들이 접근할 수 있도록 할지, 어떤 종류의 데이터인지, 어떤 데이터만이 검색되고 어떤 데이터를
 다른 사람들이 수정할 수 있을지 등의 규칙을 정할 수 있다.
- 데이터에 접근하는 표준화된 방법을 제공한다.

키포인트는 API functionality는 provioer의 실제 구현에 무관하게 규정된다는 것이다. 당신은 API를 통해 interact하기 위해 어플리케이션 구현의 전체를 이해할 필요가 없다는 것이다.

### How APIs Work
은행을 가면 은행원이 고객과 금고간에 중간다리(interface) 역할을 한다. 이것은 우리가 client-server 커뮤니케이션에서 볼 수 있는 관계와 같다. 사용자 또는 
client는 API 서버에 request를 보내고 서버에서는 요청을 파싱([다른 언어로 작성된 문서를 디코딩 한다](https://est0que.tistory.com/11))하고 데이터베이스에서 요청을 처리하고
([queries the database](http://www.terms.co.kr/query.htm)) response를 포맷([그 내용을 어떻게 표현해야하는 지에 관한 필수적인 정보를 추가](http://www.terms.co.kr/format.htm))하여 되돌려보낸다.

프로세스를 정리하면 다음과 같다.
1. Client가 API 서버에 request를 보낸다.
2. API는 그 요청을 파싱한다.
3. 그 요청이 제대로 포맷되었다면 서버는 정보를 위해 데이터베이스에서 요청을 처리하거나 request에 해당하는 action을 수행한다.
4. 서버는 response를 포맷하고 client에게 전송한다.
5. client는 구현에 따라 그 response를 랜더(html로 입력받아 해석 후 모니터로 출력)한다.


### Internet Protocols(IPs)
Internet Protocol(IP)는 인터넷을 통해 컴퓨터간 데이터를 전송하는 프로토콜이다. 각 컴퓨터에는 인터넷에 연결된 다른 모든 컴퓨터에서 식별하는 고유한 
IP주소가 있어야한다.

다음과 같은 다른 Internet Protocol도 존재한다.
- Transmission Control Protocol(TCP): 데이터 전송에 사용된다.
- Hypertext Transmission Protocol(HTTP): 텍스트와 하이퍼링크들을 전송하는데 사용된다.
- File Transfer Protocol(FTP): server와 client간에 파일들을 이동시키는데 사용된다.

API는 HTTP를 통해 client에게 데이터를 전송하기 때문에 우리는 주로 HTTP에 대해 다루게 될 것이다.

### RESTful APIs
REST는 Representational State Transfer를 의미한다. 2000년에 Roy Rielding에 의해 고안된 아키텍처 스타일이다.
- Uniform Interface: 모든 rest 아키텍처는 데이터 자원에 접근하고 처리하는 표준화된 방법을 가져야한다. 데이터 자원의 representation(예: JSON vs XML)을 처리하는 방법을 설명하는 self-descriptive messages와 고유한 리소스 식별자(i.e., 고유 URL)가 서버 response에 포함된다. 즉, 자원은 representation과 그 representation을 어떻게 처리하는지 self-descriptive message로 설명된 규칙들로만 접근 가능하다.
- Stateless: 모든 client request는 독립적이다. server는 후속 request들을 만들기 위하여 어떠한 application data도 저장할 필요가 없다.
- Client-Server: 아키텍처에는 ciient와 server가 모두 존재해야 한다.
- Cacheable & Layered System: [캐싱](https://richong.tistory.com/95)과 레이어링(계층화)은 네트워크 효율성을 높여준다.

### Let's get started!
![server](/img/server.png)
지난시간에는 파이썬으로 어떻게 데이터베이스를 설정하고 다루는지에 관해 배웠고 우리는 이제 API server interface에 관해 이야기를 해 볼 것이다.
중요한 키포인트는 이것은 데이터와 client의 중간에 있는 interface라는 점이다. 그러므로 이것의 역할은 매우 중요하다. 우리의 API는 데이터 무결성을 유지하고 클라이언트와의 커뮤니케이션은 매우 명확해야한다는 것을 명심하라. 만약 API가 잘 구성되고 제대로 문서화되었다면 우리의 data는 
안전할 것이고 수많은 cilent들과 user들에게 사용될 것이다.
