---
title: "[Digital Forensic] NONamed_infect"
date: 2021-07-22T02:45:54+09:00
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

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Problem.PNG "Problem")

오늘 풀어볼 문제다.  
먼저 문제에서 제공하는 infect.ad1 파일을 다운로드했다.

문제를 읽어보면 악성코드에 감염된 PC이며 해당 악성 프로그램을 찾고 실행 시간을 파악해야한다.  
가장 먼저 떠오른 것은 Prefetch이다.  

![Pre1](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Prefetch1.PNG)

[root]\Windows\Prefetch 위치에 존재한다.  
먼저 이를 추출하여 WinPrefetchView라는 툴을 사용하여 내용을 확인해보았다.  

![Pre2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Prefetch2.PNG)

마구잡이로 탐색을 시작해서 크게 이상한 흔적은 찾지 못했다.  
그래도 조금 의심이 가는 두 개의 흔적을 찾았다.

![S2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Stranger1.PNG)
![S2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Stranger2.PNG)

첫 번째 흔적은 단순히 이름이 이상했지만,  
두 번째 흔적은 CMD가 실행된 흔적이였다.  
일반적인 사용자는 CMD를 사용하는 일이 거의 없을텐데  
파일 기준으로 최근에 실행된 흔적이 존재했다.  
그래서 위 두 흔적의 최초 실행시간, 마지막 실행시간을 정리해두었다.  

![Sinfo](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/SInfo1.PNG)

하지만 아직 악성프로그램을 찾기에는 정보가 너무 부족했다.  
그래서 Users에 있는 swing의 바탕화면을 찾아봤다.  

![Vbox1](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Vbox1.PNG)

너무 악성 프로그램같은 파일이 존재한다.  
아직 단정할 수는 없기에 Downloads도 찾아봤다.  

![Vbox2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Vbox2.PNG)

오.... 맞는 것 같다.  
일단 해당 프로그램이 최초 실행된 시각과 마지막 실행시각을 알기 위해서 Prefetch를 확인했다.  

![Vbox3](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Vbox3.PNG)

해당 프로그램이 실행되고 있던 일을 알기 위해서 수정시간별로 정렬해보았다.  

![Vbox4](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_infect/Vbox4.PNG)

아까 수상하게 여겼던 CMD가 Vbox 뒤에 바로 실행된 것으로 보아  
악성 프로그램임을 짐작할 수 있다.  
이를 정리하여 Flag로 입력했다!  
