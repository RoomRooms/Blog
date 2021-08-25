---
title: "[Digital Forensic] NONamed_우리 이제 그만하자"
date: 2021-08-25T17:10:54+09:00
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

![Problem](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/Problem.PNG)

이번 글에서 해결할 문제다!  
문제에 따르면 디스크D에 문제가 있다는 것 같다.  
여기서 MFT나 VBR 쪽 문제일 것 같다는 느낌이 왔다.  

문제 파일을 압축 해제하면 다음과 같다.  

![Problem2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/Problem2.PNG)

우리가 다뤄볼 볼륨 D를 FTK Imager를 통해서 열어보자!  

![Broken](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/Broken.PNG)

열리지 않는다. 역시 박살난 이미지구만유..  
이제 HxD를 통해서 열어보자!  

![BR](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/BR.PNG)

열자마자 바로 부트 레코드가 있다.  
이런 경우는 단일 디스크라는 의미! ( 필자도 찾아보다가 알았다. )  

일단 BR에 문제가 없는지 한번 확인해보자.  
가변적인 값을 제외하고 고정된 값들을 먼저 확인했는데, OEM 값이 이상하다.  
위 사진의 밑줄을 한 부분을 보면 소문자로 \"ntfs\"라고 써져있다.  
원래 저 곳은 대문자로 \"NTFS\"가 정상이다. 고쳐주자!  

![BR2](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/BR2.PNG)

쓰읍.. 혹시 복구가 되었는지 확인을 위해 다시 한 번 FTK Imager로 열어봤다!  

![Origin](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/Origin.PNG)

어우.. 바로 복구가 됐다..  
이제 볼륨 내부를 돌아보자!  
내부에 수상한 파일이 있다. 바로 \"important\.txt\"이다.  
내용은 아래와 같다.  

![Important](https://raw.githubusercontent.com/RoomRooms/blog/master/img/Digital%20Forensic/NONamed/NONamed_Us_Memory2/Important.PNG)

흠.. 내부 값이 참 수상하다. 이를 Base64로 디코딩했더니?.. Flag 값이 나왔다.  
디스크 복구인 줄 알고 긴장했는데, 약간 아쉽다.  
