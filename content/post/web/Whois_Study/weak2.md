---
title: "[Web] Web Study Weak 2"
date: 2021-09-20T17:07:18+09:00
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

오늘의 포스팅에서는 CORS와 CSP(Content Security Policy)를 다룰 것이다.

# CORS

 CORS의 사전적 의미를 먼저 알아보자!  
 Cross-origin resource sharing, 즉 CORS다. 한글로는 교차 출처 자원 공유라고 한다.  
 이름만 들으면 Web 상에서 서로의 자원을 주고 받는 형태의 기술인가? 라는 생각밖에 안 든다.  
 
 CORS에 대해 자세히 알기 위해서는 동일 출처 정책 이라는 것을 알아야 한다.  
 동일 출저 정책이란 특정 출처의 자원을 사용할 때, 다른 출처에서의 리소스 접근을 금지하는 정책입니다.  
 한 마디로 같은 출처 내에서만 해결해라! 라는 거죠.  
 이런 귀찮은 건 왜 만들었지?.. 싶지만 역시 보안 때문입니다.  
 
 Web에서는 외부 리소스를 제한하는데 이는 XSS와 같은 해킹을 방어하기 위함입니다.  
 즉, 보안성을 위해 내부에서 일 처리해! 라는 의미입니다.  
 하지만 일 하다보면 외부 자원이 필요하게 됩니다.  
 이 때, 다른 출처의 리소스를 호출하기 위한 하나의 방법이 CORS입니다.  
 특정 방식으로 외부와 통신하는 것으로 동일 출처 정책의 인가를 받는 통신이라는 겁니다.  
 
 CORS는 크게 두 가지의 통신 방식이 존재합니다.  
 단순 요청과 예비 요청입니다.  
 
## 단순 요청

 단순 요청은 외부 출처(서버)에 특정한 형식(조건)을 충족한 요청을 의미합니다.  
 조건은 아래와 같습니다.  
  
 * Method
   - GET
   - POST
   - HEAD
   
 * 헤더 ( 아래 헤더만 사용 가능 )
   - Accept
   - Accept-Language
   - Content-Language
   - Content-Type
   - DPR,
   - Downlink
   - Save-Data
   - Viewport-Width
   - Width
 
 * Content-Type ( 아래 값만 사용 가능 )
   - application/x-www-form-urlencoded
   - multipart/form-data
   - text/plain
 
 위와 같은 조건 외 몇 가지 더 존재합니다.  
 ( 참고는 아래에 링크 남겨두겠습니다! )  
 
 위의 조건을 충족하여 서버에 요청을 보내면  
 서버는 Access-Control-Allow-Origin 헤더를 포함하여 응답합니다.  
 브라우저는 위 헤더를 확인해서 CORS을 진행할 것인지 판단합니다.  

## 예비 요청

 예비 요청은 단순 요청을 보내기 전에 안전 여부를 확인하기 위해 먼저 예비 요청을 보내고 나서 확인이 되면 요청을 하는 방식입니다.  
 즉, 조건은 큰 차이가 없다는 것입니다.  
 

# CSP

 자 이제 CSP입니다. Content-Security Policy의 약자입니다.  
 말 그대로 악의적인 컨텐츠 실행으로 인한 해킹을 방지하기 위한 보안 정책입니다.  
 
 CSP는 악의적 컨텐츠 실행을 방지하기 위해 신뢰 가능한 컨텐츠 목록을 갖는다.  
 이 목록 내의 컨텐츠는 허가되지만 이 이외의 비인가 컨텐츠는 차단하는 방식이다.  
 방화벽의 White List 방식을 생각하면 편할 거다.  
 특수 헤더를 HTTP에 삽입하여 특정 컨텐츠의 출처를 확인하여 목록과 대조한다.  
 
 헤더에는 여러 가지 종류가 존재하며 그 예는 아래와 같다.
 
 - Content-Security-Policy: default-src 'self'
 - Content-Security-Policy: default-src 'self' *.example
 
 ---
 
 위 두 개념을 공부하는 데에 참고한 블로그들입니다.  
 저 같은 분들이 공부하도록 도움을 주시는 블로거님들 감사합니다.  
 
 [참고1](https://w01fgang.tistory.com/147)  
 [참고2](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)  
 [참고3](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)  
 [참고4](https://beomy.github.io/tech/browser/cors/)
