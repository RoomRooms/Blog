---
title: "[Digital Forensic] NONamed_유출된 자료 거래 사건 2"
date: 2021-07-22T04:57:05+09:00
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

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/Problem.PNG)

오늘의 문제다!!  
처음 문제를 본 순간, 아 이건 어렵다. 라는 직감이 강하게 왔다.  
(실제로 어려웠다.. 바로 전 글에서 실력 늘었나?.. 하고 좋아했는데..)  

USB 관련 문제다.   
과거에 Forensic-Proof에서 USB 흔적 추적에 대한 글을 본 적이 있어서 그 글을 많이 참고했다.
(Forensic-Proof는 정말.. 최고의 사이트인 것 같다..)  
[Forensic-Proof](http://forensic-proof.com/archives/3632)

현재 우리가 알고자 하는 정보는 USB로 옮겨졌을 것으로 의심되는 파일을 찾는 것  
변경된 파일 이름과 변경 시각을 알아내고자 한다.  

위 두 정보는 NTFS Log Tracker를 통해서 알아낼 수 있다.  
이를 위해 먼저 NTFS Log Tracker에 필요한 파일들을 추출했다.  

![LogFile, MFT](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/Log&mft.PNG)

![J](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/J.PNG)

위 세 가지 파일을 모두 추출한 다음에 NTFS Log Tracker에 넣어 분석했다.  

![Log Tracker](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/LogTracker1.PNG)

문제는 로그가 너무 많다는 것이다!!  
이것을 다 보는 것이 아니라 특정 시간대를 찾아야 한다고 생각했다.  
나는 그 시간대를 USB가 연결된 시간으로 생각했다.  
USB에 옮겼을 것으로 의심되는 파일이기에  
연결되고 얼마 안되서 파일을 변경하였을 것이다.  
물론 파일을 이동한 기록을 찾을 수 없다.  

USB 최초 연결 시간은 레지스트리를 통해서 알 수 있다.  
하지만 해당 문제는 이미지 파일이기에 하이브 파일을 추출했다.  
우리에게 필요한 하이브는 SYSTEM과 SOFTWARE다.  

![SYS&SOFT](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/sys&soft.PNG)

두 하이브 파일 모두 %WINDIR%system32\config 아래에 존재한다.  
두 파일을 추출한 후, 이를 REGA를 통해서 분석했다.  

SYSTEM\ControlSet00#\Control\DeviceClasses\{10497B1B-BA51-44E5-8318-A65C837B6661}\{Sub Keys}  
SOFTWARE\Microsoft\Windows Portable Devices\Devices\{Device Entry}  

위 두 경로 모두 USB 최초 연결 시각을 알 수 있는 레지스트리다.  
만일을 위해 두 개를 모두 확인했다.  

![Rega](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/REGA.PNG)

이를 통해 확인한 시각은 2020-02-20 17:38:54와 2020-02-20 17:47:41 이다.  
두 개의 USB를 사용한 것으로 확인된다.  
이를 메모장에 기록해두자!  

![Info](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/Info.PNG)

시간은 알았다. 그럼 이 정보를 사용해서 이벤트를 확인해보자.  
아까 분석했던 NTFS Log Tracker에 먼저 2020-02-20 17:38:54로 찾아갔다.  
여기서부터 천천히 이벤트를 확인해보았다.  

천천히 내려가던 중, 이름 변경 이벤트가 있었다.  

![Flag](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/Flag.PNG)

이를 보고 수상하게 생각하여 변경된 이름인 todaysmemo.hwp를 검색했다.  
검색 결과 17:48:26에 파일을 삭제한 것을 알 수 있다.  

![search](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Company2/search.PNG)

이 시간을 되새김해보면 아까 찾은 남은 하나의 USB 최초 연결시각과 1분도 차이나지 않는다.  
추측이지만 피의자는 파일의 이름을 변경 후, 다른 하나의 USB (SAMSUNG)으로 옮긴 것 같다.  

위에 주어진 파일명과 시간을 이용해 Flag를 알 수 있다!!  

