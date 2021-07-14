---
title: "[Digital Forensic] NONamed_MagicIMAGE"
date: 2021-07-15T04:53:38+09:00
categories:
- Digital Forensic
- NONamed
tags:
- DF
keywords:
- tech
#thumbnailImage: //example.com/image.jpg
---

<!--more-->

![Problem](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "MagicIMAGE 문제")

NONamed_MagicIMAGE 문제는 위와 같다.  
제공된 파일을 먼저 다운로드했다.  

![제공된 이미지](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "mandu.png")

당연하게도 정상적인 png 파일이 아니였다.  
이런 문제는 가장 먼저 파일 시그니처를 확인한다. 파일의 유형을 파악하기 위해서  

![mandu.png 바이너리 값](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "바이너리 값")

정상적인 PNG의 파일 시그니처 값은 "89 50 4E 47 0D 0A 1A 0A"인 반면  
mandu.png는 "89 50 4E 47 0E 0B 1F 0B"인 것을 확인할 수 있다.  
이를 정상적인 시그니처 값으로 수정해보았다.  

![fix Signature](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "PNG 시그니처 값 수정")

![Flag](https://github.com/RoomRooms/blog/blob/master/img/Digital%20Forensic/Suninatas/21/foremost.PNG?raw=true "Flag 값")

그 결과 위와 같이 Flag 값을 얻을 수 있다.  
Flag 값은 직접 찾아보세요!
