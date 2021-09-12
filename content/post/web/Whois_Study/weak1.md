---
title: "[Web] Web Study Weak 1"
date: 2021-09-13T01:54:18+09:00
categories:
- Web
- Whois
tags:
- Web
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

오늘은 Whois에서 진행되는 정보보안교육에서 받은 과제를 포스팅하려 한다!  
Web에 대한 과제인만큼 기본적인 배경지식으로 시작하고자 한다.  

# HTTP
HTTP(Hypertext Transfer Protocol)는 인터넷에서 데이터 통신을 하기 위해  
사용되는 서버/클라이언트 모델의 프로토콜입니다.  
지금 우리가 인터넷에서 영상, word같은 문서, exe 실행파일을 받을 수 있는 것처럼  
정말 다양한 데이터를 주고 받을 수 있습니다.  

이러한 HTTP는 기본적으로 사용자는 개인 컴퓨터의 브라우저를 통해 접속하며  
서비스 제공은 서버를 통해서 이루어집니다.  
이처럼 하나의 서버를 복수의 사용자가 이용합니다.  
여기서 문제가 발생하게 됩니다. 바로 서버 부하입니다.  
서버에 가해지는 부하를 줄여주기 위해 HTTP는 기본적으로 비연결을 지향합니다.  
Ps. HTTP 1.1에서 connection 헤더에 keep-alive를 설정하면 연결을 유지할 수 있습니다.  

서버가 부담하게되는 요소는 연결만이 아닙니다.  
각 클라이언트의 정보를 모두 저장하기에는 서버의 저장공간은 한정적입니다.  
이를 해결하는 특성이 상태정보를 유지하는 않는다는 것입니다.  
연결이 끊기는 순간 상태 정보를 제거하는 겁니다.  
이를 통해 한정된 자원을 아낄 수 있는거죠.  

하지만 생각해보면 매연결마다 정보는 초기화되면 속도가...  
그 외에도 유지를 했으면 하는 정보는 어떡하지?...  
이를 위해 사용하는 기술이 쿠키, 세션 등입니다.  

## 쿠키

달콤하니 우유가 땡기는 이 데이터는 Key-Value의 형태를 갖습니다.  
클라이언트(이하 클라)는 서버에 요청을 보내었을 때,  
서버는 해당 클라의 상태 정보를 쿠키의 형태로 기록합니다.  
그리고 응답할 때 해당 쿠키를 같이 전송합니다. (HTTP 헤더 속 Set-Cookie)  
클라는 받은 쿠키를 저장하고 있다가 서버에 요청이 있을 때마다 같이 전송합니다.  
서버는 전달받은 쿠키값을 읽어 상태를 확인합니다.  

클라의 상태를 기록해주는 쿠키는 서버에 부담이 거의 없습니다.  
클라에 저장되며 서버는 전달받은 쿠키값을 읽고 분석할 뿐이니까요.  
문제는? 쿠키가 클라에 저장되기에 변조될 위험이 존재한다는 것입니다.  
실제로 쿠키를 암호화하지 않을 경우 가장 쉽게는 크롬 Plug in으로 변조할 수 있습니다.  

하.. 그럼 어떡하지..  
걱정마세요! 세션이 우리를 광명으로 이끌 것 입니다.  

## 세션

세션은 가장 쉽게 말하자면 쿠키를 서버에도 저장하는 것입니다.  
왜? 쿠키 변조를 통한 해킹을 방지하기 위해서 입니다.  
세션은 브라우저 종료시 같이 파기됩니다.  
기본적으로 쿠키와 비슷합니다.  
클라의 요청에 서버는 Session ID라는 것을 만들고 이에 해당되는 메모리를 할당합니다.  
그 공간에 클라의 상태 정보를 저장합니다.  
이후 Session ID를 담아 클라로 응답을 보냅니다.  
매 요청마다 클라는 Session ID를 실어서 보내는데  
해당 정보는 서버에서 대조하여 해당 세션 ID에 관련된 정보를 응답합니다.  

와 세션이 우리에게 광명을..이 아닙니다.  
세션이 많아지면?.. 상태정보를 유지하지 않는 HTTP의 특성에 반대됩니다.  
즉, 과한 세션은 서버에 부하를 준다는 것이지요.  
현실적으로 대부분의 웹사이트는 쿠키를 주로 사용합니다.  

하.. 세션과 쿠키를 짬뽕한 건 없나?.. 짬짜면같은 매력..  
있습니다. 예.

## 세션 쿠키

세션 쿠키는 만료 날짜가 있는 쿠키들처럼 브라우저 종료 후에도 남는 것이 아닌  
세션과 같이 브라우저 종료시 사라지는 쿠키를 의미합니다.  
짬짜면같은 너.. 색다르다..

짬짜면을 먹었다면 이제 고~~급 짬뽕을 먹어볼까요? JWT입니다.

## JWT

JWT(Json Web Token)는 말 그대로 Json을 이용해서 클라 상태 정보를 저장하는 Web Token입니다.  
JWT는 기본적으로 static 변수랑 디스크에 저장됩니다.  
엥 왜 Static에?.. 매번 사용할 때마다 디스크에서 로드하면 자원이... 너무..  
그래서 static에도 저장합니다.  

클라는 요청을 할 때, JWT를 실어서 서버에 전송합니다.  
서버는 JWT가 허가된 JWT인지 확인합니다.  
또한, 로그아웃을 하게된다면? 디스크에 저장된 JWT까지 모두 삭제합니다.  

이제 다음 과제, REST API!!

# REST API

REST API를 설명하기 전에 REST를 먼저 설명해야 한다.  
REST(Representational State Transfer)은 HTTP에 존재하는 모든 자원에 고유한 URI를 부여하여  
구별하며 HTTP method를 이용해 해당 자원에 대한 CRUD를 적용하는 것 입니다.  
즉, 각 자원들을 체계적으로 분류하고 Method가 반환하는 상태 코드와  
Method의 위험 등급에 따라 분리하는 것입니다.  

예를 들자면 나는 네이버의 메일의 받은 메일함 속 특정 메일을 보고싶어! 하면  
mail.naver.com/receive_mail/Whois-Study  
처럼 항목을 자세하게 나누는 것입니다.  

Method도 자원만큼 분리를 해야하는데, 단순히 데이터를 조회만 한뿐인 GET과  
데이터를 직접 입력하고(POST), 수정하는(PUT)이 같은 위험 등급이라면 위험할 것입니다.  
즉, 위험 등급에 따라 분리를 하고 높은 위험 등급을 갖는 Method를 더 주의하여야 합니다.  

그리고 반환값, 상태 코드 등을 그대로 반환하는 것이 아닌 상황에 맞는 응답을 주어야 합니다.  
예를 들자면 클라이언트가 GET을 통해 에러를 발생시킨다고 가정해봅니다.  
해커는 해당 에러값을 분석하거나 에러를 통해 해당 명제가 참인지 거짓인지 판별할 수 있습니다.  
이를 블라인드 SQL 인젝션이라고 합니다.  
이를 방지하기 위해서는 응답값을 그대로 반환하는 것이 아닌 응답값의 표현을 전송하는 것입니다.  
예를 들면 User ID is MISSING from the database.라는 결과를 반환하는 것이 아닌  
'오류가 발생하였습니다' 와 같이 실제 응답값과 클라이언트 반환값을 분리시키는 것입니다.  

REST API는 REST를 기반으로 서비스 API를 만드는 것을 의미하며,  
체계적 구조를 하나의 규칙처럼 제공하여 상호간 통신을 보다 수월하게 하도록 합니다.  
물론 보안성도 더 좋아지겠죠.  

REST API의 Method는 총 4가지로 GET, POST, PUT, DELETE입니다.  

|HTTP Method|CRUD|Example|
|:---:|:---:|:---:|
|GET|자원 조회|SELECT a From b|
|POST|자원 생성|CREATE Table c|
|PUT|자원 수정|UPDATE d SET ~~ |
|DELETE|자원 제거|DELETE Table f|

자 이제, Web에 대한 지식을 배웠으니 해킹을 배워봅시다.  

## los.rubiya.kr

위 사이트는 다양한 Web 문제가 있는 사이트입니다.  
오늘은 그 중 Gremlin, Cobolt, Goblin에 대해 리뷰할 것입니다.  

### Gremlin

![Grem](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/grem.PNG?raw=true)

php 코드를 보면 정규표현식을 통한 필터링 외에는 크게 보안 정책이 없는 것을 확인할 수 있습니다.  
가장 기본적인 SQL injection인 'or 1 = 1 %23을 pw에 넣어줍니다.  
(%23은 \#으로 URL Encoding한 값입니다.)

![Grem_Fin](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/grem_fin.PNG?raw=true)

다음 문제는 Cobolt입니다.  

![Cobolt](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/col.PNG?raw=true)

이번에 몇 가지 달라진 점들이 있지만 가장 중요한 부분은 아래와 같다.
> if(\$result\['id'\] == 'admin') solve("cobolt");  

id가 admin이면 앞뒤 안가리고 해결된다는 것.  
이를 위해 id=admin'%23을 넣어주었다.  

![Cobolt_Fin](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/Col_fin.PNG?raw=true)

위와 같이 해결된 것을 알 수 있다.  

다음 문제는 Goblin입니다.  

![Goblin](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/goblin.PNG?raw=true)

이번에는 아예 정규표현식을 강하게 규정했습니다.  
\' 혹은 \"을 차단했네요.  
이를 해결하기 위해서 사용하는 방식이 인코딩입니다.  
서버에서 처리하도록 injection 값을 인코딩하여 정규표현식을 우회하는 것입니다.  
admin이라는 값을 id에 넣어야 하기에 admin을 헥사값으로 인코딩해보겠습니다.  

![hex](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/hex.PNG?raw=true)

61646D696E라는 값이 나옵니다. 이 값을 헥사를 뜻하는 0x를 붙인 후에 id=에 넣어주면 됩니다.  

![goblin_fin](https://github.com/RoomRooms/blog/blob/master/img/Web/Whois_Study/weak1/Goblin_fin.PNG?raw=true)

문제는 대충 풀어보았는데, 만일 내가 개발자라면? 이 취약점을 어떻게 막을 수 있을까를 생각해보았습니다.  

Gremlin -> Cobolt -> Goblin 순으로 보안을 신경쓴만큼 Goblin을 기준으로 생각했습니다.  
가장 처음으로 드는 생각은 인코딩한 값을 인식하지 못하도록 서버에서 인코딩값을 차단하는 것입니다.  
하지만 이렇게 블랙리스트 방식으로는 답이 없을 것 같아서 다른 대책을 생각했습니다.  

지난 2021/09/06 에 있었던 웹 해킹 강좌에서 배운 Prepared Statement를 사용한 것입니다.  
사용자의 입력을 받는 구문을 미리 컴파일하는 것으로 의도하지 않은 조작을 방지하는 기술입니다.  
그 외를 생각해보면 화이트리스트 방식으로 한정된 문자만을 허용하는 방식이 가장 효율적일 것 같습니다.  

