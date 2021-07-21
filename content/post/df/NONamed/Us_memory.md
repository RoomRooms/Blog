---
title: "[Digital Forensic] NONamed_우리의 추억들"
date: 2021-07-22T03:20:24+09:00
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

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_memory/Problem.PNG)

이번 글에서 풀어볼 문제다!  
일단 먼저 제공되는 파일을 다운받고 이를 FTK Imager를 통해 열었다.  

문제를 잘 읽어보면 \"중요한 사진들\"이 \"삭제\"되었다고 한다.  
삭제된 사진이라는 키워드에 파일 썸네일 관련 문제라는 것을 직감했다!  
(실력이 늘고 있는건가.. 두근두근한다.. 모든 것은 갓 만두 님의 은혜..)

![Thum](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_memory/Thum.PNG)

위에 있는 파일들은 각각에 해당하는 픽셀 크기에 해당하는 썸네일들이 저장되어 있다.  
이 파일들을 thumbcacheviewer을 사용해서 열어보았다!.  
가장 선명한 1024는 아무 썸네일도 없었다..  
그 다음인 256을 열어보았다!!  

![Thum](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_memory/Flag.jpg)

Flag 발견!!
