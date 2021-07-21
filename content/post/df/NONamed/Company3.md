---
title: "[Digital Forensic] NONamed_유출된 자료 거래 사건 3"
date: 2021-07-22T07:41:42+09:00
categories:
- Digital Forensic
- NONamed
tags:
- df
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company3/Problem.PNG)

이번 포스팅에서 해결할 문제다!!!  
동거자의 이름과 계정 삭제된 시각을 알아내야 한다!  

처음 문제를 이건 보안 로그라는 감이 왔다.  
이벤트 로그를 보다보면 계정의 로그인 정보들이 있는 곳이 있다.  
그 곳을 분석하면 될 것 같다.  

먼저 Google에 \"windows 사용자 계정 삭제 이벤트 로그\" 라고 검색했다.  
그러면 [Link](https://docs.microsoft.com/ko-kr/windows/security/threat-protection/auditing/event-4726)를 찾을 수 있을 것이다.  
해당 글은 이벤트 로그에서 사용자 계정 삭제에 대한 설명을 해주고 있다.  
이 지식을 활용하기 위해서 먼저 문제 파일에서 이벤트 로그를 추출해내야 한다.  

![logs](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company3/logs.PNG)

이벤트 로그가 저장되어 있는 곳은 \"\%systemroot\%system32\winevt\Logs\"에 있다.  
보안 로그는 사용자 계정이 생성되고 삭제되는 기록이 있다.  
그러니 우리에게 필요한 로그는? Security.evtx이다.  
보안 로그는 이벤트 뷰어를 통해서 열어보자!  

![sec](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company3/sec.PNG)

우리가 찾고자 하는 이벤트는 사용자 계정 삭제다.  
이에 해당하는 이벤트 ID는 4726이다.  
이를 이벤트 뷰어의 필터링을 이용해서 찾아보자!!

![Filter](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company3/Filter.PNG)

찾았다!! 여기에 계정명과 삭제 시각이 기록되어 있다.

![Flag](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company3/Flag.PNG)

## 여기서부터는 [Site](https://ssol2-jjanghacker.tistory.com/entry/N0Named-B-%EC%9C%A0%EC%B6%9C%EB%90%9C-%EC%9E%90%EB%A3%8C-%EA%B1%B0%EB%9E%98-%EC%82%AC%EA%B1%B43)을 참조했다..

사실 원래 여기까지 했다면 정답이다..  
근데 Flag 값이 틀렸다고 나왔다.  
그래서 계정명이 아닌 사용자 이름을 찾으려고 했다.  
당연히 찾을 수도 없었고 의미 없는 삽질이였다..  
위 사이트를 참고하고 깨달은 점은.. Flag를 24 기준으로 적은게 아니라  
12 기준으로 적어서 틀렸다는 것이다... 하...  


